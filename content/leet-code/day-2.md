---
title: "LeetCode Day 2"
date: 2022-01-08T16:55:13+08:00
draft: false
slug: day-2
---
今天继续LeetCode刷题打卡，今天依旧是数组方面的2道题

## 今日题目

### 题一 [两数之和](https://leetcode-cn.com/problems/two-sum/)
对于这道题我依旧选择使用hash（map）来解题，map真香啊，创建一个map，key为数组值，value为他的位置（下标），当map[target - 当前值] 存在时就证明找到那2个slice成员了。


***应该先判定map中存不存在target - v1，然后再插入key和value，如果反了会出现示例输入[3,3], 6，输出[0，0]，正确输出是[0, 1]***
~~~
func twoSum(nums []int, target int) []int {
    m := make(map[int]int)
    var i, v1, v2 int
    var exsits = true
    for i, v1 = range nums {
        v2, exsits = m[target - v1]
        
        m[v1] = i
        if exsits {
           break
        }
    }
     return []int{v2, i}
}
~~~

### 题二 [合并两个有效数组](https://leetcode-cn.com/problems/merge-sorted-array/)
对于这道题，我选择对nums1[i]和nums2[0]进行比较，如果nums1[i]的值大于nums2[0]，就将nums1[i]值改为nums2[0]，nums[i]放到nums2中，并对nums2进行排序，保证nums2是递增的。

***对于nums2排序（其实就是nums[i]即t的值插入合适的位置），对于这个需要考虑它插入到末尾和nums2[k]==t时值也需要往前压（不往前压会导致当nums2的最后值等于t时，t未能插入nums2）的问题***

~~~
func merge(nums1 []int, m int, nums2 []int, n int)  {
    var i, k ,t int
    for ;i < m && n > 0; i ++{
        if nums1[i] <= nums2[0] {
            continue
        } else {
            t = nums1[i]
            nums1[i] = nums2[0]
            nums2[0] = t

            for k = 1; k < n; k ++ {
                if nums2[k] <= t{
                    nums2[k - 1] = nums2[k] 
                } else if nums2[k] > t {
                    nums2[k - 1] = t
                    break
                }
                if k == n - 1 {
                    nums2[k] = t
                }
            }

        }
    }
    _ = append(nums1[:m], nums2...)
}
~~~

## 今日总结
做第一题时，发现了golang的一个新特性，golang的map还可以查看key是否存在，这也太nice了。


做第二题时，感觉自己思路是正确的，但是解答时却花费较多时间，主要因为对于一些边界条件还是没有思考到位，感觉这个唯有多练才能解决。