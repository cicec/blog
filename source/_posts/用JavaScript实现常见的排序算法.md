---
title: 用JavaScript实现常见的排序算法
date: 2018-02-10 21:56:00
tags: 前端相关
---

##前戏

- 复习了一些比较常见的排序算法，用JS实现，带一些实现思路。
- 无图，无脑贴代码。。

## 比较排序

### 冒泡排序

1. 比较相邻的元素，若第二个比第一个大，则交换位置。
2. 对每一对相邻元素作比较，一轮交换完成后，最大的元素应该排在最后，即排序完成。
3. 对剩余待排序元素重复步骤1~2，直到所有元素都已排序完成。

``` js
function bubbleSort(paramArr) {
    const arr = paramArr.slice()
    for (let i = 0; i < arr.length; i++) {
        for (let j = 0; j < arr.length - i; j++) {
            if (arr[j] > arr[j + 1]) {
                const temp = arr[j]
                arr[j] = arr[j + 1]
                arr[j + 1] = temp
            }
        }
    }
    return arr
}
```

### 选择排序

1. 从未排序序列中选择最小的元素放在已排序序列的末尾。
2. 重复步骤1，直到所有元素都已被放入已排序序列。

``` js
function selectionSort(paramArr) {
    const arr = paramArr.slice()
    for (let i = 0; i < arr.length; i++) {
        let minIndex = 0
        for (let j = 1; j < arr.length - i; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j
            }
        }
        arr.push(arr[minIndex])
        arr.splice(minIndex, 1)
    }
    return arr
}
```

### 插入排序

1. 取出第一个元素，这个元素可以认为已经被排序。
2. 取出下一个元素，从后向前依次对比，如果已排序元素大于新元素，则将该元素后移一位。
3. 重复步骤2，直到找到已排序元素小于等于新元素的位置，将新元素插入。
4. 重复步骤2~3，直到所有元素都插入完毕。

``` js
function insertionSort(paramArr) {
    const arr = paramArr.slice()
    for (let i = 1; i < arr.length; i++) {
        for (let j = i; j > 0; j--) {
            if (arr[j - 1] > arr[j]) {
                const temp = arr[j]
                arr[j] = arr[j - 1]
                arr[j - 1] = temp
            } else {
                break
            }
        }
    }
    return arr
}
```

### 快速排序

1. 在待排序元素中选择一个元素作为基准。
2. 将所有小于基准的元素都移到基准的左边，所有大于基准的元素都移到基准的右边。操作结束后，基准就已经处在最终排序后它的位置。
3. 对左右两个子集重复步骤1~2，直到所有的子集只剩下一个元素。

这里说一下代码细节：

1. 将序列中最后一个数作为基准数。
2. 用storeIndex表示移动元素放置的位置，最开始指向序列起点。
3. 从前向后遍历，移动小于等于基准元素的元素到数组的开头，每次移动 storeIndex 自增 1。
4. 循环结束后将storeIndex位置上的元素与基准数交换，此时一轮排序完成。

``` js
function quickSort(paramArr) {
    const swap = (arr, index1, index2) => {
        const temp = arr[index1]
        arr[index1] = arr[index2]
        arr[index2] = temp
    }

    const partition = (arr, start, end) => {
        let storeIndex = start
        const standardIndex = end
        for (let i = start; i < end; i++) {
            if (arr[i] <= arr[standardIndex]) {
                swap(arr, storeIndex, i)
                storeIndex += 1
            }
        }
        swap(arr, storeIndex, end)
        return storeIndex
    }

    const sort = (arr, start, end) => {
        if (start > end) return []
        const storeIndex = partition(arr, start, end)
        return [
            ...sort(arr, start, storeIndex - 1),
            arr[storeIndex],
            ...sort(arr, storeIndex + 1, end)
        ]
    }

    return sort(paramArr.slice(), 0, paramArr.length - 1)
}
```

## 非比较排序

### 计数排序

懒。。介绍就抄维基的啦。

1. 找出待排序的数组中最大和最小的元素
2. 统计数组中每个值为 i 的元素出现的次数，存入数组 C 的第i项
3. 对所有的计数累加（从 C 中的第一个元素开始，每一项和前一项相加）
4. 反向填充目标数组：将每个元素i放在新数组的第 C[i] 项，每放一个元素就将 C[i] 减去1

``` js
function countingSort(arr) {
    const bucketArr = []
    const sortedArr = []

    for (let i = 0; i < arr.length - 1; i++) {
        if (!bucketArr[arr[i]]) {
            bucketArr[arr[i]] = 1
        } else {
            bucketArr[arr[i]] += 1
        }
    }

    for (let i = 0; i < bucketArr.length - 1; i++) {
        if (bucketArr[i]) {
            for (let j = 0; j < bucketArr[i]; j++) {
                sortedArr.push(i)
            }
        }
    }

    return sortedArr
}
```

### 桶排序

1. 设置一个定量的数组当作空桶子。
2. 寻访序列，并且把项目一个一个放到对应的桶子去。
3. 对每个不是空的桶子进行排序。
4. 从不是空的桶子里把项目再放回原来的序列中。

这里对每个桶中的元素使用插入排序

``` js
function bucketSort(arr, step) {
    const bucket = []
    const sortedArr = []

    for (let i = 0; i < arr.length; i++) {
        const index = Math.floor(arr[i] / step)
        if (!bucket[index]) {
            bucket[index] = []
            bucket[index].push(arr[i])
        } else {
            bucket[index].push(arr[i])
            for (let j = bucket[index].length - 1; j > 0; j--) {
                if (bucket[index][j - 1] > bucket[index][j]) {
                    const temp = bucket[index][j]
                    bucket[index][j] = bucket[index][j - 1]
                    bucket[index][j - 1] = temp
                } else {
                    break
                }
            }
        }
    }

    for (let i = 0; i < bucket.length; i++) {
        if (bucket[i]) {
            for (let j = 0; j < bucket[i].length; j++) {
                sortedArr.push(bucket[i][j])
            }
        }
    }

    return sortedArr
}
```

## 结语

还有一些比较常见的如基数排序、堆排序这里没有写（其实是已经忘干净了。。可能以后会再开一篇吧）

惯例贴链接：

- [排序算法 - 维基百科](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95#%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%E5%88%97%E8%A1%A8)
- [排序算法的可视化](https://visualgo.net/bn/sorting)
- [bubkoo](http://bubkoo.com/tags/algorithm/)
