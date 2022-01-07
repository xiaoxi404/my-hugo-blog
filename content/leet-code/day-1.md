---
title: "LeetCode Day 1"
date: 2022-01-07T20:47:17+08:00
draft: false
slug: day-1
---
leetcode刷题第一天，这次我选择跟随leetcode的[数据结构学习计划](https://leetcode-cn.com/study-plan/data-structures/)来刷题，计划学习时间为14天，每天2道题，今天是第一天，如果后面没有期末考试，继续坚持打卡。

## 今日题目
### 题一 [存在重复元素](https://leetcode-cn.com/problems/contains-duplicate/)
这道题思路较为清晰，使用map来做，map[int]bool，没出现的值value默认为false，出现了我们改成true。
~~~
func containsDuplicate(nums []int) bool {
    m := make(map[int] bool)
    for _, i := range nums {
        if m[i] {
            return true
        } else {
            m[i] = true
        }
    }
    return false
}
~~~

### 题二 [最大子数组和](https://leetcode-cn.com/problems/maximum-subarray/)
思路：保证i当前的子数组的和大于0，一旦小于0了，就要舍弃前面的子数组。因为小于0的子数组和下一个数组成员相加后只会使新的子数组和变小，不如不要它，然后不断和max比较。注意点：max的初始值应该要为nums[0]，如果设个0，可能在全为负数组时出现问题，而且nums[0]一定<=最大子数组和。
~~~
func maxSubArray(nums []int) int {
    max := nums[0]
    m := 0

    for _, v := range nums {
        m += v

        if m > max {
            max = m
        }

        if m < 0 {
            m = 0
        }
    }
    return max
}
~~~

## 今日总结
还是经常忘记golang的一些的特性，如 i := range nums， i不是nums中的值而是位置，因为这个问题卡了半天实在不应该。

面对第二题明明已经做过一次，却还是没有直接秒杀，还是在于过去没有总结，这个要改变。