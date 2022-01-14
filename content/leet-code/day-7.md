---
title: "LeetCode Day 7"
date: 2022-01-14T20:37:48+08:00
draft: false
slug: day-7
---

今天继续leetcode打卡，今天是链表的2道题。

## 今日题目

### 题一 [翻转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

对于这道我们使用2个指针一前一后，最开始时将head的Next的指向nil，然后开始将节点间的指向翻转。

~~~
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func reverseList(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return head
    }

    t1 := head
    t2 := head.Next
    head.Next = nil

    var t *ListNode

    for t2 != nil {
        t = t2.Next
        t2.Next = t1
        t1 = t2
        t2 = t 
    }
    return t1
}
~~~

### 题二 [删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

对于这道题还是使用2个指针，一前一后，如果发现2个指针的Val相等，第二个指针前移，第一个不动，起到删除的作用。

***如果退出循环后，需要保证t1指向t2,防止末尾出现重复未删除的情况***

~~~
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func deleteDuplicates(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return head
    }
    
    t1 := head
    t2 := head.Next

    for t2 != nil {
        if t2.Val == t1.Val {
            t2 = t2.Next
            continue
        }

        t1.Next = t2
        t2 = t2.Next
        t1 = t1.Next
    }

    t1.Next = t2    

    return head
}
~~~

## 今日总结

今天题都是过去做过的，所以做的比较快，但是也表明原来做过的题起到作用了。