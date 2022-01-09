---
title: "LeetCode Day 3"
date: 2022-01-09T20:20:14+08:00
draft: false
slug: day-3
---
今天继续LeetCode刷题打卡，今天依旧是数组方面的2道题。


## 今日题目

### 题一 [两个数组的交集Ⅱ](https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/)
对于这道题目我依旧选择使用map来做，先遍历nums1，将其成员作为key，其成员的出现次数作为map的value。然后nums2将判断他的成员在map中的value，若value > 0，则证明出现成员重合，就将其放入nums3中并且value - 1。

~~~
func intersect(nums1 []int, nums2 []int) []int {
    m := make(map[int]int)
    var nums3 []int
    for _, v := range nums1 {
        m[v] += 1
    }
    for _, v := range nums2 {
        if m[v] > 0 {
            nums3 = append(nums3, v)
            m[v] -= 1
        }
    }
    return nums3
}
~~~

### 题目二 [买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)
对于这道题目，我将最大差储存在maxDelta中，采用遍历这个数组时，先求取当前值-前面的最小值的差，如果差大于maxdelta就更新一下maxdelta，最后再看看min是否需要更新。最后返回maxDelta即可。

~~~
func maxProfit(prices []int) int {
    maxDelta := 0
    min := prices[0]
    t := 0
    
    for i := 1; i < len(prices); i ++ {
        t = prices[i] - min
        if t > maxDelta {
            maxDelta = t 
        }

        if t < 0 {
            min = prices[i]
        }
    }
    return maxDelta
}
~~~

## 今日总结

面对第二题时，我很清晰的记得自己曾经做过，但是却无法立刻回想出上面这个解，还是因为练的和总结少了，继续加强练习。