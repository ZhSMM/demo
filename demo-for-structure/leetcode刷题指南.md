# leetcode刷题指南

### 数据结构

分析问题：递归的思想，自顶向下，从抽象到具体

数据结构的基础存储方式只有两种：

- 数组：顺序存储
- 链表：链式存储

优缺点：

> **数组**由于是紧凑连续存储,可以随机访问，通过索引快速找到对应元素，而且相对节约存储空间。但正因为连续存储，内存空间必须一次性分配够，所以说数组如果要扩容，需要重新分配一块更大的空间，再把数据全部复制过去，时间复杂度 O(N)；而且你如果想在数组中间进行插入和删除，每次必须搬移后面的所有数据以保持连续，时间复杂度 O(N)。

> **链表**因为元素不连续，而是靠指针指向下一个元素的位置，所以不存在数组的扩容问题；如果知道某一元素的前驱和后驱，操作指针即可删除该元素或者插入新元素，时间复杂度 O(1)。但是正因为存储空间不连续，你无法根据一个索引算出对应元素的地址，所以不能随机访问；而且由于每个元素必须存储指向前后元素位置的指针，会消耗相对更多的储存空间。

其他复杂数据结构：

- 队列和栈：
  - 数组实现：扩容和缩容的问题
  - 链表实现：需要更多的空间存储节点指针
- 图的两种表示方法：
  - 邻接表：链表，优点，节省空间，缺点，效率比邻接矩阵低
  - 邻接矩阵：二维数组，优点，判断连通性迅速，缺点，矩阵稀疏时浪费空间
- 散列表：通过散列把键映射到一个大数组中，解决散列冲突的办法
  - 拉链法：操作简单，需要额外的空间存储指针
  - 线性探查法：需要数组特征以便连续寻址，节省空间
- 树：
  - 用数组实现就是堆，因为堆是一个完全二叉树，用数组存储不需要节点指针，操作比较简单
  - 用链表实现就是常见的二叉树

