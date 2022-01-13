---
title: "LeetCode Day 6"
date: 2022-01-13T19:27:20+08:00
draft: false
slug: day-6
---

昨天因为一些原因没有完成打卡，所以今天我做了2天的量，这次分别是字符串的3道题和链表的3道题。

## 今日题目

### 题一 [字符串中的第一个唯一字符](https://leetcode-cn.com/problems/first-unique-character-in-a-string/)

对于这道题我依旧选择使用map来做，字符作为key，索引作为value，将字符串中的重复字符的value置为-1，最后返回value中的最小值。

~~~
func firstUniqChar(s string) int {
    ss := []byte(s)
    m := make(map[byte]int, len(ss))

    for i, v := range ss {
        if _, ok := m[v]; ok {
            m[v] = -1
            continue
        }

        m[v] = i
    }

    i := len(ss)

    for _, v := range m {
        if v == -1 {
            continue
        }

        if v < i {
            i = v
        }
    }

    if i == len(ss) {
        return -1
    }

    return i

}
~~~

### 题二 [赎金信](https://leetcode-cn.com/problems/ransom-note/)

这道题目依旧使用map来做，先通过rs创建一个map，map的key为字符，value为key的出现次数，然后遍历ms，若ms的字符在map中存在，value --，若value == 0 时 delete这个键值对。如果map的长度变为0后，证明rs可以由ms中的字符组成。如果map的长度不为0，证明rs不可以由ms中的字符组成。

~~~
func canConstruct(ransomNote string, magazine string) bool {
    rs := []byte(ransomNote)
    ms := []byte(magazine)

    m := make(map[byte]int)

    for _, v := range rs {
        m[v] ++
    }

    for _, v := range ms {
        i, ok := m[v]

        if ok {
            m[v] --
        } else {
            continue
        }

        if i == 1 {
            delete(m, v)
        }

        if len(m) == 0{
            return true
        }
    }

    return false
}
~~~

### 题三 [有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/)

对于这道题，我选择2个map，key为字符，value为字符出现次数，最后比较2个map，若2个map一致就return true，反之return false。

~~~
func isAnagram(s string, t string) bool {
    ss := []byte(s)
    ts := []byte(t)
    
    if len(ss) != len(ts) {
        return false
    }

    m1 := make(map[byte]int)

    m2 := make(map[byte]int)

    for _, v := range ss {
        m1[v] ++ 
    }

    for _, v := range ts {
        m2[v] ++ 
    }

    for k, v := range m1 {
        if m2[k] != v {
            return false
        } 
    }

    return true
}
~~~

### 题四 [环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

对于这道题我的想法是储存每一个遍历过的节点地址，下一个节点地址重复出现后，就证明出现了重复，return true。

~~~
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func hasCycle(head *ListNode) bool {
    if head == nil {
        return false
    }
    m := make(map[*ListNode]bool)
    
    for ; head.Next != nil; {
        m[head] = true

        if _, ok :=  m[head.Next]; ok {
            return true
        } 
         
        head = head.Next 
    }
    return false
}
~~~

### 题五 [合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

对于这道题就是采用3个指针，一个指针在list1上探路，一个指针在list2上探路，哪个指针的值小，第三个指针的next指向它。

***这道题目需要小心处理开始节点***

~~~
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func mergeTwoLists(list1 *ListNode, list2 *ListNode) *ListNode {
    if list1 == nil {
        return list2
    }
    
    if list2 == nil {
        return list1
    }

    var p1, p2 *ListNode

    if list1.Val >= list2.Val {
        p1 = list2
        list2 = list2.Next
    } else {
        p1 = list1
        list1 = list1.Next
    }

    p2 = p1

    for ;list1 != nil || list2 != nil ; {
        if list1 == nil {
            p2.Next = list2
            break
        }

        if list2 == nil {
            p2.Next = list1
            break
        }

        if list1.Val >= list2.Val {
            p2.Next = list2
            list2 = list2.Next
            p2 = p2.Next
        } else {
            p2.Next = list1
            list1 = list1.Next
            p2 = p2.Next
        }

    }

    return p1

}
~~~

### 题六 [移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)

对于这道我们需要设置一个哑巴节点在head前面，然后遍历删除指定元素节点。

~~~
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func removeElements(head *ListNode, val int) *ListNode {
    if head == nil {
        return head
    }

    var Node ListNode
    Node.Next = head

    p1 := &Node
    p2 := head

    for p2 != nil {
        
        if p2.Val == val {
            p2 = p2.Next
            p1.Next = p2
            continue
        }

        p1 = p1.Next
        p2 = p2.Next
    }
    return Node.Next
}
~~~

## 今日总结

今天的刷题都比较顺利，链表的题做的都不错，感觉以前刷的链表题起了作用。

做题时对于map使用更加熟练了，但感觉也比较依赖map。