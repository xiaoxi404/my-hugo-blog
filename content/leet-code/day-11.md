---
title: "LeetCode Day 11"
date: 2022-01-18T22:10:23+08:00
draft: false
slug: day-11
---

今天继续leet-code打卡，今天依旧是二叉树的两道题。

## 今日题目

### 题一[翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

对于这道题使用递归是最简单的，但是也可以通过前序遍历，让每一个节点的左右子树都被翻转即可。

递归法：
~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func invertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return root
    } 
    root.Left, root.Right = invertTree(root.Right), invertTree(root.Left)
    return root
}
~~~

遍历翻转法：
~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func invertTree(root *TreeNode) *TreeNode {
    var stack Stack
    t := root
    for root != nil || !stack.isEmpty() {
        for root != nil {
            root.Left, root.Right = root.Right, root.Left
            stack.push(root.Right)
            root = root.Left
            continue
        }
        
        root = stack.pop()

    }
    return t
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

## 题二 [路径总和](https://leetcode-cn.com/problems/path-sum/)

对于这道题目依旧是递归最为简单。通过stack来做的话，就是stack中不光要记录当前节点的指针，还要记录他单钱的路径和，当他满足targetSum时，return true。

递归法：
~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func hasPathSum(root *TreeNode, targetSum int) bool {
    if root == nil {
        return false
    }
    if root.Left == nil && root.Right == nil{
        if root.Val == targetSum {
            return true
        }else {
            return false
        }
    }
    return hasPathSum(root.Left, targetSum - root.Val) || hasPathSum(root.Right, targetSum - root.Val)
}
~~~

遍历法：
~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func hasPathSum(root *TreeNode, targetSum int) bool {
    var stack Stack
    t := 0
    for root != nil || !stack.isEmpty() {
        for root != nil {
            t = t +  root.Val
            stack.push(Node{t, root})
            root = root.Left
            continue
        }

        node := stack.pop()
        t = node.Val
        if t == targetSum && node.Add.Left == nil && node.Add.Right == nil{
            return true
        }
        root = node.Add.Right
    }
    return false
}

type Node struct {
    Val int
    Add *TreeNode
}

type Stack struct {
    s []Node
}

func (this *Stack) pop() Node {
    x := this.s[len(this.s) - 1]
    this.s = this.s[:(len(this.s) - 1)]
    return x
}

func (this *Stack) push(x Node) {
    this.s = append(this.s, x)
}

func (this *Stack) isEmpty() bool {
    return len(this.s) == 0
}
~~~

## 今日总结

今天在做第一题时，思路不对，花费超长的时间，还没做出来，最后只能选择递归来做，然后通过递归写出遍历翻转的方法，感觉二叉树还是要多练。

还有就是发现使用递归和遍历解决问题，他们的内存消耗和时间消耗几乎一样，可能正是因为递归也是用stack来实现的，所以感觉以后做题可以优先考虑递归来做，然后反向推出遍历的解法。