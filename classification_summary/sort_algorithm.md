## Python 实现常见排序算法

>注：
1. 本文只包含内排序，只在计算机内存中进行进行排序的算法。当待排序数据太大，不能在内存中记录排序数据时需要访问外存，则为外排序。
2. 本文中默认实现升序排序。
3. 时间复杂度以元素移动次数为基准，其中的n为待排序序列中元素的个数。
4. 稳定的排序算法：排序后不会改变值相等的元素的相对位置，则为稳定的排序算法。
5. 原址排序：基本上不需要额外空间，只需要少量的临时辅助内存。

### 写在前面的总结
排序算法分类：
----- | -----
特性   |   排序算法
不稳定的排序算法 | 选择，快速、堆、Shell
适合较大n的排序算法 | 快速、归并、堆、基数
比较次数与初始有序度无关的排序算法 | 选择、二路归并、基数
最佳、最差、平均情况下时间复杂度一样的排序算法 | 选择、归并、堆、Shell、基数

排序算法的时间复杂度：
![sort complexity]('images/sort_complexity')


***

### 插入排序
每个新元素与前面已排序的子序列进行比较，将其插入子序列中正确的位置。
1. 初始时，将第一个元素视为已排序的子序列，从第二个元素到末尾为未排序元素；
2. 遍历未排序元素，将其（每次标志为key）与已排序子序列中的值从后往前（从大到小）一一比较，将已排序子序列中所有比key大的数往后挪一位。直到遇到第一个比key小的元素，将key插入这个元素后。
3. 遍历完完成排序。

```python
def insertion_sort(nums):
    n = len(nums)
    for i in range(1, n):
        key = nums[i]
        j = i - 1
        while j >= 0 and nums[j] > key:
            nums[j + 1] = nums[j]
            j -= 1
        nums[j + 1] = key
```
1. **稳定性**：仅当排序子序列中的元素比key大时才将其后移，而相等时会将key插入这个元素后，因此是稳定的。
2. **时间复杂度**： 插入排序外层循环总会执行n-1次，完成遍历。内层循环的执行次数取决于序列的无序程度，序列中的“逆序对（与期望序列顺序相反的元素对）”的数目决定了内层循环中的交换次数。
	- 最佳情况：序列本身有序，元素不需要进行移动，会进行n-1次比较（外层循环的执行次数），此时为**O(n)**;
	- 最差情况：序列本身逆序，内层循环每次需要执行i次，i的累计和（1，2，3，...，n-1）约为(n^2)/2，此时为**O(n^2)**;
	- 平均情况：平均情况下有序子序列中有一半与将要进行比较的元素呈逆序对，时间复杂度约为(n^2)/4，此时为**O(n^2)**。
3. 适用于基本有序的序列。


### 冒泡排序
从后往前冒泡：列表前面部分为已排序序列，每次像泡泡一样，将未排序部分最小的元素依次交换到列表前已排序子序列的最后；  
从前往后冒泡：列表后面部分为已排序序列，每次像泡泡一样，将未排序部分最大的元素依次交换到列表后已排序子序列的最前面。

```python
def buble_sort(nums):
    n = len(nums)
    for i in range(0, n):
        for j in range(n - 1, i + 1, -1):
        	if nums[j] < nums[j - 1]:
        		nums[i], nums[j] = nums[j], nums[i]
```
1. **稳定性**：仅当后一个元素比前一个元素小的时候才交换，因此当两元素相等时不会交换，保存相对位置不变，是稳定的。
2. **时间复杂度**： 内层循环总会执行i次，i的累计和约为(n^2)/2，因此冒泡排序在最佳、平均、最差情况下的时间复杂度均为**O(n^2)**。

**改进版冒泡排序**：在内层循环中加一个标记，用于记录这一次循环中有没有进行交换，若此次循环中未进行交换，则说明整个序列已经有序，这时可以提前跳出循环。此时的最佳时间复杂度为**O(n)**，即有序序列进入一次内层循环就发现不需要交换，跳出循环，算法结束。
```python
def bubble_sort(nums):
    n = len(nums)
    for i in range(0, n):
        exchange_flag = True        # 标记是否进行了交换
        for j in range(n - 1, i + 1, -1):
            if nums[j] < nums[j - 1]:
                nums[i], nums[j] = nums[j], nums[i]
                exchange_flag = False
        if exchange_flag == True:    # 若未进行交换，退出循环
            break
```

### 选择排序
选择排序每次将第i小的元素放到位置i处，可以将其看作冒泡排序的改进，冒泡排序是每次将dii小的元素交换到位置i，选择排序是直接找出这个元素并插入位置i。
```python
def selection_sort(nums):
    n = len(nums)
    for i in range(0, n - 1):
        min_idx = i     # 记录第i小的元素下标
        for j in range(i + 1, n):
            if nums[j] < nums[min_idx]:
                min_idx = j
        nums[i], nums[min_idx] = nums[min_idx], nums[i]
```
1. **稳定性**：寻找剩余元素中最小元素的部分是从前往后遍历，因此若两数相等，后面的一个最终会被视为当前最小并被放入排序子序列中，因此选择排序是不稳定的。
2. **时间复杂度**：在任何情况下都会运行完两重循环，因此其最佳、平均和最差时间复杂度相等，均为**O(n^2)**。


### 归并排序
分而治之：将待排序列表分成片段，先处理好各个片段，再将这些片段合并。我们通常说的归并排序是二路归并，即每次将待排序序列分成两半。
1. 分割：递归地将整个序列分成两半，最后得到一系列长度为1的小片段；
2. 合并：对于每一组小片段，将它们一一合并知道组合成完整序列。合并过程中完成对子序列的排序。

```python
def merge_sort(nums):
    n = len(nums)
    if n <= 1: return nums  # 分割至长度为1
    list1 = nums[:n//2]
    list2 = nums[n//2:]
    return merge(merge_sort(list1), merge_sort(list2))


def merge(list1, list2):
    merged_list = []
    idx1, idx2 = 0, 0
    while idx1 < len(list1) and idx2 < len(list2):
        if list1[idx1] <= list2[idx2]:
            merged_list.append(list1[idx1])
            idx1 += 1
        else:
            merged_list.append(list2[idx2])
            idx2 += 1
    if idx1 < len(list1):
        merged_list += list1[idx1:]
    if idx2 < len(list2):
        merged_list += list2[idx2:]
    return merged_list
```
1. **稳定性**：每趟归并逐个比较，出现两个值相同的元素先复制前面的那个，因此是稳定的。
2. **时间复杂度**：将长度为n的序列递归分割成长度为1的n个子序列，递归的深度为`log(n)`。第一层递归可以认为是对一个长度为n的序列排序，第二层是对两个长度为n//2的序列进行排序，最深一层递归是对n个长度为1的序列进行排序，每一层排序过程为遍历子序列并进行比较，均需要O(n)步。因此总的时间复杂度为**O(nlognn)**。该时间代价不依赖于待排序数组中值的相对顺序，因此其最佳、平均、最差时间复杂度相等。
3. **空间复杂度**：附加大小为n的存储空间，空间复杂度为O(n)。

**归并排序的改进**：
1. 设定一个阈值threshhold，当子序列长度小于等于这个序列时不再划分，而是使用插入排序。
2. 当左半边子序列的最右值（最大值）小于右子序列最左值（最小值）时，说明左子序列的值均小于右子序列，此时不需要比较可以直接合并。


### 快速排序
快速排序的思想可以参考二叉搜索树BST的结构特点：二叉树的每个父节点的左子树的所有结点均小于它，其所有右子树结点均大于它。BST隐式实现了分治法，对其左右子树分别处理。  
快速排序每次选择一个轴值(pivot)，并将比pivot小的元素均移动到它左边，比它大的元素均移动到它右边，这样完成一次划分(partition)。之后再分别对左边部分和右边部分进行这样的划分操作，直到子序列的元素个数只有一个或零个，此时整个序列排序完成。

```python
def quick_sort(nums:list):
    '''快速排序
    
    使用快速排序算法将待排序序列升序排列
    :param nums: 待排序序列
    :return: 原址排序，无返回值
    '''
    n = len(nums)
    quick_sort_recursive(nums, 0, n-1)


def quick_sort_recursive(nums:list, start:int, end:int):
    '''递归地对序列进行快速排序
    
    使用快速排序算法将待排序序列升序排列
    :param nums: 待排序子序列
    :param start: 待排序子序列的起始坐标
    :param end: 待排序子序列的结尾坐标
    :return: 原址排序，无返回值
    '''
    if start >= end: return    # 只剩0或1个元素时不用进行排序
    pivot_index = find_pivot(start, end)   # 找出轴值下标
    nums[pivot_index], nums[end] = nums[end], nums[pivot_index] # 将轴值交换到数组最后
    partition_index = partition(nums, start, end, nums[end])   # 完成一次划分并找出右半部分起始位置坐标
    nums[partition_index], nums[end] = nums[end], nums[partition_index] # 将轴值交换回分割两部分的位置
    quick_sort_recursive(nums, start, partition_index-1)     # 对左子序列排序
    quick_sort_recursive(nums, partition_index+1, end)    # 对右子序列排序


def find_pivot(start:int, end:int) -> int:
    '''找出快速排序的轴值：这里使用中间元素作为轴值

    :param start: 待排序子序列的起始坐标
    :param end: 待排序子序列的结尾坐标
    :return: 返回轴值的坐标
    '''
    return (start + end) // 2


def partition(nums:list, left:int, right:int, pivot:int) -> int:
    '''完成一次快速排序的划分操作
    
    :param nums: 待排序子序列
    :param left: 待排序子序列的起始坐标
    :param right: 待排序子序列的结尾坐标
    :param pivot: 待排序子序列的轴值坐标
    :return: 返回划分后右半部分的起始位置
    '''
    while left < right:
        while left < right and nums[left] < pivot:
            left += 1
        while left < right and  nums[right] >= pivot:
            right -= 1
        nums[left], nums[right] = nums[right], nums[left]
    return left
```

简化版（非原址排序，空间复杂度**O(nlogn)**）:
```python
def quick_sort(nums: list):
    '''快速排序

    使用快速排序算法将待排序序列升序排列
    :param nums: 待排序序列
    :return: 原址排序，无返回值
    '''
    n = len(nums)
    if n <= 1:
        return nums    # 只剩0或1个元素时不用进行排序
    pivot = nums[(n // 2)]   # 找出轴值
    left, right = [], []    # 左右子序列
    nums.remove(pivot)      # 从原序列中移除轴值
    for x in nums:          # 遍历序列并将其中的元素分到左右子序列中
        if x >= pivot:
            right.append(x)
        else:
            left.append(x)
    return quick_sort(left) + [pivot] + quick_sort(right)
```
1. **稳定性**：频繁交换，不稳定。
2. **时间复杂度**：find_pivot函数常数时间复杂度。partition函数运行一遍left和right会向中间移动直至相遇，其时间复杂度为**O(s)**,s为子序列元素个数。整个快速排序算法的时间复杂度：  
	- 最佳情况：每次划分时轴值都将序列划分为相等的两部分，此时一共需要logn次划分，总时间代价为**O(nlogn)**；
	- 最差情况：每次划分都极不均匀（倒序时），一部分没有元素，另一部分n-1个元素，此时每个子问题的规模只减少1，此时总时间代价为(1~n累加)为**O(n^2)**。此时快排并不比冒泡优秀，但这种假设的情况非常极端，不太可能出现。
	- 平均情况：假设每种排列等概率出现，每种排列的时间开销之和除以总的排列数(n!)，最终得到平均开销为**O(nlogn)**。

**快速排序的改进**：
1. 快排的时间复杂度可以通过改进常数因子得到优化，最明显的改进之处为find_pivot函数，因为轴值的选择对序列划分影响很大。 使用“三者取中法”，每次随机找三个值，将其中中间大小的一个作为轴值，这样会提高均匀划分的概率。
2. 分治思想的排序在处理大数据集量时效果比较好，小数据集性能差些。因此可以在数组长度较小时（一般是小于等于9时）使用插入排序（快速排序的划分已经让数组基本有序，使用插入排序有接近线性的时间复杂度）。
3. 快速排序本质上是递归的，当需要存储的信息不多时可以使用栈来模拟递归调用。


### 堆排序

### Shell排序

### 基数排序

>参考：  
《数据结构与算法分析（C++版）（第三版）》
《算法导论（原书第三版）》