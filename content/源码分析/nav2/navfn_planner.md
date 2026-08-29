# navfn
```cpp
COSTTYPE * costarr;  //代价数组,长度nx * ny
float * potarr;  //势场函数
bool * pending;  //等待标志
int nobs;  //障碍数量
int * pb1, * pb2, * pb3; //优先区块的存储缓冲区
float curT;  //当前阈值
float priInc;  //优先值阈值增量
float * gradx, * grady;  //梯度数组
float * pathx, * pathy;  //路径点
int npath;  //路径点数量
int npathbuf;  //路径的缓冲区
int * pb1, * pb2, * pb3;  //优先队列的三块内存
int * curP, * nextP, * overP;  //存储三个队列的数组
int curPe, nextPe, overPe;  //末节点索引
void setNavArr(int nx, int ny); //设置相关数组大小
```

## 主要函数
### setCostmap
**将ROS costmap转为本地costmap**

| **ROS costmap值** | |
| :---: | --- |
| **0** | **空地** |
| **1-252** | **膨胀区，越大越靠近障碍** |
| **253** | **贴边** |
| **254** | **障碍物** |
| **255** | **未知** |


```cpp
//宏定义
//COST_OBS_ROS 253
//COST_OBS 254
NavFn::setCostmap(const COSTTYPE * cmap, bool isROS, bool allow_unknown)
{
	COSTTYPE * cm = costarr;
	if (isROS) {  // ROS-type cost array
		for (int i = 0; i < ny; i++) {
			int k = i * nx;
			for (int j = 0; j < nx; j++, k++, cmap++, cm++) {
				//顺序取出costmap列表
				*cm = COST_OBS;
				int v = *cmap;
				//如果costmap在[0,252]，按照线性赋值，为正常区域
				if (v < COST_OBS_ROS) {
					//对原costmap进行线性映射增大梯度特征
					v = COST_NEUTRAL + COST_FACTOR * v;
					//如果线性映射后值太大，限制上限
					if (v >= COST_OBS) {
						v = COST_OBS - 1;
					}
					*cm = v;
				} else if (v == COST_UNKNOWN_ROS && allow_unknown) {
					//如果原costmap为未知区域但是参数允许对未知区域规划
					//将未知区域设为上限
					v = COST_OBS - 1;
					*cm = v;
				}
			}
		}
		//结果：正常区域被线性缩放，未知区域被设为上限值（如果允许的话）
		//缩放后，正常区间[COST_NEUTRAL,COST_OBS-1（贴边）]，
		//COST_OBS（违法区域）
		//UNKNOWN（未知区域）=COST_OBS-1（贴边）
```

---

### propNavFnAstar
```cpp
bool
NavFn::propNavFnAstar(int cycles)
{
  int nwv = 0;  // max priority block size
  int nc = 0;  //在优先级缓冲区中的节点
  int cycle = 0;  //循环数

  //计算起点终点距离，还记得setCostmap中对costmap的调整吗
  //v = COST_NEUTRAL + COST_FACTOR * v;
  //最小代价为COST_NEUTRAL，将距离x单位距离代价模拟h(n)
  float dist = hypot(goal[0] - start[0], goal[1] - start[1]) * 
	static_cast<float>(COST_NEUTRAL);
  //调整阈值上限为直线距离+COST_OBS以便拓展
  curT = dist + curT;

  //初始节点
  int startCell = start[1] * nx + start[0];

  //主循环
  for (; cycle < cycles; cycle++) {
	//buffer中没有数据退出
    if (curPe == 0 && nextPe == 0) {
      break;
    }

    // stats
    nc += curPe;
    if (curPe > nwv) {
      nwv = curPe;
    }

    //对当前队列的处理标志进行清空
    int * pb = curP;
    int i = curPe;
    while (i-- > 0) {
      pending[*(pb++)] = false;
    }

    //对当前队列进行逐个更新
    pb = curP;
    i = curPe;
    while (i-- > 0) {
      updateCellAstar(*pb++);
    }

    //交换队列将下一批节点转移至当前处理队列
    curPe = nextPe;
    nextPe = 0;
    pb = curP;  // swap buffers
    curP = nextP;
    nextP = pb;

    //当前节点已经全部处理完
    if (curPe == 0) {
      curT += priInc;  //将阈值+阈值增量
      curPe = overPe;  //将延迟的队列交换至当前队列
      overPe = 0;
      pb = curP;  // swap buffers
      curP = overP;
      overP = pb;
    }

    //判断起点势能是否被计算过
    if (potarr[startCell] < POT_HIGH) {
      break;
    }
  }

  last_path_cost_ = potarr[startCell];

  if (potarr[startCell] < POT_HIGH) {
    return true;
  } else {
    return false;
  }
}
```

---

### updateCellAstar
**势能更新函数**

```cpp
inline void
NavFn::updateCellAstar(int n)
{
  //取上下左右四个邻节点
  float u, d, l, r;
  l = potarr[n - 1];
  r = potarr[n + 1];
  u = potarr[n - nx];
  d = potarr[n + nx];

  //寻找左右/上下各自最小方向
  float ta, tc;
  if (l < r) {tc = l;} else {tc = r;}
  if (u < d) {ta = u;} else {ta = d;}

  //进行波前更新
  if (costarr[n] < COST_OBS) {  //如果当前点为COST_OBS（障碍物）不更新
    float hf = static_cast<float>(costarr[n]);  //当前点代价
    float dc = tc - ta;  //水平 纵向势能差值
    if (dc < 0) {  //对插值取绝对值，同时保证ta代表最小的那个方向
      dc = -dc;
      ta = tc;
    }

    //计算新势能
    float pot;
	//如果水平 纵向势能差值过大
	//新势能=自身代价+四个邻节点中最小势能
    if (dc >= hf) {  
      pot = ta + hf;
    } else {  
		//如果差值不大，使用两个邻节点差值更新
	    //使用二次插值优化
		//Eikonal方程：(pot - ta)² + (pot - tc)² = hf²
		//精确解：pot = (ta + tc + sqrt(2hf² - (tc - ta)²)) / 2
		//存在sqrt运算成本高，优化成如下多项式近似
      float d = dc / hf; //在if后，d=[0,1]
      float v = -0.2301 * d * d + 0.5307 * d + 0.7040;
      pot = ta + hf * v;
    }
	
    //判断邻节点是否受影响
    if (pot < potarr[n]) { //更新后当前节点变得更优
	  //宏INVSQRT2 = 1 / √2 ≈ 0.707
	  //为什么要除以√2？
	  //观察Eikonal：(pot - ta)² + (pot - tc)² = hf²
	  //假如ta与tc相近，pot = ta + hf / √2
	  //也就是说自身代价对自身势能影响要除以√2再加上周边最小势能
      float le = INVSQRT2 * static_cast<float>(costarr[n - 1]);
      float re = INVSQRT2 * static_cast<float>(costarr[n + 1]);
      float ue = INVSQRT2 * static_cast<float>(costarr[n - nx]);
      float de = INVSQRT2 * static_cast<float>(costarr[n + nx]);

      //当前点到start的直线距离
      int x = n % nx;
      int y = n / nx;
	  //欧式距离乘以单位代价值模拟h(n)
      float dist = hypot(x - start[0], y - start[1]) * static_cast<float>(COST_NEUTRAL);

	  //更新g(n)
      potarr[n] = pot;
	  //计算f(n)=g(n)+h(n)
      pot += dist;
      if (pot < curT) {  //通过势能大小判断优先级
        if (l > pot + le) {push_next(n - 1);}
        if (r > pot + re) {push_next(n + 1);}
        if (u > pot + ue) {push_next(n - nx);}
        if (d > pot + de) {push_next(n + nx);}
      } else {
        if (l > pot + le) {push_over(n - 1);}
        if (r > pot + re) {push_over(n + 1);}
        if (u > pot + ue) {push_over(n - nx);}
        if (d > pot + de) {push_over(n + nx);}
      }
    }
  }
}
```

