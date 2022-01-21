---
title: "LeetCode Day 13"
date: 2022-01-21T23:28:03+08:00
draft: false
slug: day-13
---

今天开始leet-code的数据结构的基础部分刷题。

## 今日题目

### 题一 [只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

对于这道题目我们选择使用map，如果一个数已经出现过，从map中删去，最后返回map中唯一的数

~~~
func singleNumber(nums []int) int {
    m := make(map[int]bool)
    for _, v := range nums {
        if _, ok := m[v]; ok {
            delete(m, v)
        } else {
            m[v] = true
        }
    }
    var t int
    for k, _ := range m {
        t = k
    }
    return t
}
~~~

### 题二 [多数元素](https://leetcode-cn.com/problems/majority-element/)

对于这道题目直接对slice进行排序，取slice中间数return。

~~~
func majorityElement(nums []int) int {

    for i := 0; i < len(nums) - 1; i++ {
        for j := i + 1; j <len(nums); j++ {
            if nums[i] > nums[j] {
                nums[i], nums[j] = nums[j], nums[i]
            }
        }
    }

    return nums[len(nums)/2]
}
~~~

### 题三 [三数之和](https://leetcode-cn.com/problems/3sum/)

首先对slice排序，然后建立一个map，key为slice的元素，value为地址，最后开始在slice中寻找任意2个元素，判断他们的相加的相反数在不在map中，就证明他们三个相加等于一

***注意：跳过slice中的重复元素，避免出现重复三元组。***

~~~
func threeSum(nums []int) [][]int {
    out := [][]int{}

    if len(nums) < 3 {
        return out 
    }
    m := make(map[int]int, len(nums))

   

    for i := 0; i < (len(nums) - 1); i ++ {
        for j := i + 1; j < len(nums); j ++ {
            if nums[i] > nums[j] {
                nums[i], nums[j] = nums[j], nums[i]
            }
        }
    }

    for i,v := range nums {
        m[v] = i 
    }

    for i := 0; i < (len(nums) - 1); i ++ {
        if i != 0 && nums[i] == nums[i - 1] {
            continue
        }
        for j := i + 1; j < len(nums); j ++ {
            if j != i + 1 && nums[j] == nums[j - 1] {
                continue
            }
            if v , ok := m[- nums[i] - nums[j]]; ok {
                if v > j {
                    out = append(out, []int{nums[i], nums[j], nums[v]})
                }
            }
        }
    }

    return out
}
~~~

## 今日总结

面对leetcode数据结构的基础题，感觉自己能够做出来，但是时间和空间复杂度都很高，感觉还是需要加速学习算法