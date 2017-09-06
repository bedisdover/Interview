# 1.2 排序算法

### 分类

* 内部排序
	在排序过程 ，所有数据都容纳在内存之中，适用数据量不太大的情形

* 外部排序
	内存无法容纳所有数据，还需使用外存，适用于数据量很大的情形

	
### 内排序

* 选择排序
	* 直接选择排序
	* 堆排序
* 插入排序
	* 简单插入排序
	* 折半插入排序
	* 希尔排序
* 交换排序
	* 冒泡排序
	* 快速排序
* 归并排序
* 基数排序


### 直接选择排序

首先在 n 个记录中选出关键码最小(最大)的记录，然后与第一个记录(最后第 n 个记录)交换位置，再在其余的 n-1 个记录中选关键码最小(最大)的记录，然后与第二个记录(第 n-1 个记录)交换位置，直至选择了 n - 1 个记录。

稳定性：不稳定


### 堆排序

将待排序的序列构造成一个大顶堆.此时,整个序列的最大值就是堆顶的根结点.将它移走(其实就是将其与堆数组的末尾元素交换, 此时末尾元素就是最大值),然后将剩余的n-1个序列重新构造成一个堆,这样就会得到n个元素的次大值。如此反复执行,便能得到一个有序序列了。

稳定性：不稳定


### 简单插入排序

将一个记录插入到已经排好序的有序表中, 从而得到一个新的, 记录数增1的有序表

稳定性：稳定


### 折半插入排序

稳定性: 稳定


### 希尔排序

1 )取一增量(间隔 gap < n ),按增量分组,对每组使用直接插入排序或其他方法进行排序。

2 )减少增量(分的组减少,但每组记录增多)。直至增量为 1 ,即为一个组时。

稳定性：不稳定


### 冒泡排序
 
1 )从头到尾做一遍相邻两元素的比较,有颠倒则交换,记下交换的位置。一趟结束,一个或多个最大(最小)元素定位。

2 )去掉已定位的的元素,重复 1 ,直至一趟无交换。

稳定性：稳定


### 快速排序

1 )在 n 个对象中,取一个对象(如第一个对象—基准 pivot ),按该对象的关键码把所有小于等于该关键码的对象分划在它的左边。大于该关键码的对象分划在它的右边。

2 ) 对左边和右边(子序列)分别再用快排序。

稳定性：不稳定


### 归并排序

假设初始序列含有n个记录,则可以看成n个有序的子序列,每个子序列的长度为1,然后两两归并,得到(不小于n/2的最小整数)个长度为2或1的有序子序列,再两两归并,...如此重复,直至得到一个长度为n的有序序列为止,这种排序方法称为2路归并排序。

稳定性: 稳定
