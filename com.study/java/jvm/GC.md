### 一.GC算法


### 二.GC
#### 1)miniorGc--清理年轻代（edgen、survivor)
#### 2)majorGC--清理老年代
#### 3)fullGC--清理整个堆空间（老年代、新生代、永久代）
#### 4)GC触发的条件
````
1)youngGC:当jvm无法为新对象分配空间的时候会触发youngGc.(edgen区已满），每次youngGc后老年代的占用量会上升
2）FullGC:
a.jvm处理youngGc前发现，历史平均每次yongGC后晋升的老年代对象比当前老年代剩余的空间还要大，会触发fullGc
b.system.gc()会触发fullGC
c.为永久代分配内存时，发现内存不足也会触发fullGC
````
