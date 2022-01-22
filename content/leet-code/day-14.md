---
title: "LeetCode Day 14"
date: 2022-01-22T23:34:13+08:00
draft: false
slug: day-14
---

今天继续leetcode打卡，今天是3道数组题。

## 今日题目

### 题一 [颜色分类](https://leetcode-cn.com/problems/sort-colors/)

对于这道因为只有3种元素，2次循环，第一次将0赶到前面，第二次再将1赶到0的后面即可。

~~~
func sortColors(nums []int)  {
    j := 0

    for i, v := range nums {
        if v > 0 {
            continue
        }
        nums[j], nums[i] = nums[i], nums[j]
        j ++
    }

    for i := j; i < len(nums); i ++ {
        if nums[i] == 2 {
            continue
        }
        nums[j], nums[i] = nums[i], nums[j]
        j ++
    }
}
~~~

### 题二 [合并分区](https://leetcode-cn.com/problems/merge-intervals/submissions/)

将分区按照开始值从小到大开始排序，然后一步一步合并后面的分区（如果前一分区的结束值大于后面分间的起始值，则合并）。

~~~
func merge(intervals [][]int) [][]int {

    for i := 0; i < len(intervals) - 1; i ++ {
        for j := i + 1; j <len(intervals); j ++ {
            if intervals[i][0] > intervals[j][0] {
                intervals[i], intervals[j] = intervals[j], intervals[i]
            }
        }
    }

    out := [][]int{}

    for i := 0; i < len(intervals); i ++ {
        t := i
        for j := i + 1; j < len(intervals) && intervals[t][1] >= intervals[j][0]; j ++ {
            if intervals[t][1] < intervals[j][1] {
                intervals[t][1] = intervals[j][1]
            }
            i ++
        }
        out = append(out, intervals[t])
    }
    return out
}
~~~

### 题三 [设计哈希映射](https://leetcode-cn.com/problems/design-hashmap/)

这道题目我是看leetcode的题解的，通过key%base来实现地址的映射，key与value储存在list中。

~~~
const base = 769

type entry struct {
    key int
    value int
}

type MyHashMap struct {
   data []list.List
}


func Constructor() MyHashMap {
    return MyHashMap{make([]list.List, base)}
}

func (this *MyHashMap) Hash(key int) int {
    return key % base
}

func (this *MyHashMap) Put(key int, value int)  {
    h := this.Hash(key)
    for e := this.data[h].Front(); e != nil; e = e.Next() {
        if et := e.Value.(entry); et.key == key {
            e.Value = entry{key, value}
            return
        }
    }
    this.data[h].PushBack(entry{key, value})
}


func (this *MyHashMap) Get(key int) int {
    h := this.Hash(key)
    for e := this.data[h].Front(); e != nil; e = e.Next() {
        if et := e.Value.(entry); et.key == key {
            return et.value
        }
    }
    return -1
}


func (this *MyHashMap) Remove(key int)  {
    h := this.Hash(key)
    for e := this.data[h].Front(); e != nil; e = e.Next() {
        if e.Value.(entry).key == key {
            this.data[h].Remove(e)
        }
    }
}


/**
 * Your MyHashMap object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Put(key,value);
 * param_2 := obj.Get(key);
 * obj.Remove(key);
 */
~~~

## 今日总结

今天刷题的一大收获就是发现leetcode可以使用container中的数据结构来做，今天的时间和空间复杂度都很不理想，继续努力刷题。