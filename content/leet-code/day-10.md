---
title: "LeetCode Day 10"
date: 2022-01-17T20:37:57+08:00
draft: false
slug: day-10
---

今天继续leetcode打卡，今天是二叉树的三道题。

## 今日题目

### 题一 [二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

这道题目我们需要用到队列，首先将根节点放入队列，当一个节点输出时，将他的非nil的左右子树的根节点放入队列。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func levelOrder(root *TreeNode) [][]int {
    var out [][]int
    queue := Constructor()
    var i int

    if root == nil {
        i = 0
    } else {
        i = 1
        queue.Push(root)
    }

    for i != 0 {
        j := 0
        tout := make([]int, 0, i)
        for i != 0 {
            root := queue.Pop()
            tout = append(tout, root.Val)
            i--
            if root.Left != nil {
                queue.Push(root.Left)
                j ++
            }
            if root.Right != nil {
                queue.Push(root.Right)
                j ++
            }
        }
        i = j
        out = append(out, tout)
    }

    return out
}

type ListNode struct {
    Val *TreeNode
    Next *ListNode
}

type MyQueue struct {
    Front *ListNode
    Rear *ListNode
}


func Constructor() MyQueue {
    var Queue MyQueue
    return Queue
}


func (this *MyQueue) Push(x *TreeNode)  {
    Node := ListNode{Val: x,}

    if this.Rear == nil {
        this.Rear = &Node
        this.Front = &Node
    } else {
        this.Rear.Next = &Node
        this.Rear = &Node
    }
    
}


func (this *MyQueue) Pop() *TreeNode {
    x := this.Front.Val
    if this.Front == this.Rear {
        this.Rear = nil
        this.Front = nil
    } else {
        this.Front = this.Front.Next
    }
    
    return x
}
~~~

### 题二 [二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

我们可以将题一的每层数据append到slice改成计数器，即可计算出二叉树的最大深度。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func maxDepth(root *TreeNode) int {
    var out int
    queue := Constructor()
    var i int

    if root == nil {
        i = 0
    } else {
        i = 1
        queue.Push(root)
    }

    for i != 0 {
        j := 0
        tout := make([]int, 0, i)
        for i != 0 {
            root := queue.Pop()
            tout = append(tout, root.Val)
            i--
            if root.Left != nil {
                queue.Push(root.Left)
                j ++
            }
            if root.Right != nil {
                queue.Push(root.Right)
                j ++
            }
        }
        i = j
        out ++
    }

    return out
}



type ListNode struct {
    Val *TreeNode
    Next *ListNode
}

type MyQueue struct {
    Front *ListNode
    Rear *ListNode
}


func Constructor() MyQueue {
    var Queue MyQueue
    return Queue
}


func (this *MyQueue) Push(x *TreeNode)  {
    Node := ListNode{Val: x,}

    if this.Rear == nil {
        this.Rear = &Node
        this.Front = &Node
    } else {
        this.Rear.Next = &Node
        this.Rear = &Node
    }
    
}


func (this *MyQueue) Pop() *TreeNode {
    x := this.Front.Val
    if this.Front == this.Rear {
        this.Rear = nil
        this.Front = nil
    } else {
        this.Front = this.Front.Next
    }
    
    return x
}
~~~

### 题三 [对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

对于这道题目，我们只需要将这个二叉树看作一左一右的2个二叉树来比较即可，不过他们的遍历方向不同。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isSymmetric(root *TreeNode) bool {
    rootL := root.Left
    rootR := root.Right

    var stackL Stack
    var stackR Stack

    for rootL != nil || !stackL.isEmpty() || rootR != nil || !stackR.isEmpty() {
        for rootL != nil && rootR != nil{
            if rootL.Val != rootR.Val {
                return false
            }
            stackL.push(rootL)
            stackR.push(rootR)
            rootL = rootL.Left
            rootR = rootR.Right
        }
        if rootL != nil || rootR != nil {
            return false
        }

        rootL = stackL.pop().Right
        rootR = stackR.pop().Left

    }
    return true
}

type Stack struct {
    s []*TreeNode
}

func (this *Stack) pop() *TreeNode {
    x := this.s[len(this.s) - 1]
    this.s = this.s[:(len(this.s) - 1)]
    return x
}

func (this *Stack) push(x *TreeNode) {
    this.s = append(this.s, x)
}

func (this *Stack) isEmpty() bool {
    return len(this.s) == 0
}
~~~

## 今日总结

今天做题感觉对于层序遍历有了更深刻的了解，感觉到目前为止10天刷题，还是有成效的。