---
title: "LeetCode Day 12"
date: 2022-01-20T23:32:19+08:00
draft: false
slug: day-12
---

昨天因为在折腾手机刷机，所以没有完成昨天打卡任务，然后今天做了昨天和今天的5道二叉树题目。

## 今日题目

### 题一 [二叉搜索树中的搜索](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

对于这道题目利用二叉树的性质，判断要找的节点在哪个子树上即可，递归查找，知道找到or碰到nil。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func searchBST(root *TreeNode, val int) *TreeNode {
     if root == nil || root.Val == val{
         return root
     } else if root.Val < val{
         return searchBST(root.Right, val)
     }
     return searchBST(root.Left, val)
}
~~~

## 题二 [二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

本题与上题类似，他也采用递归的方法，找到合适的位置插入，返回自己的地址。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func insertIntoBST(root *TreeNode, val int) *TreeNode {
    if root == nil {
        TreeNode := TreeNode{Val: val,}
        root = &TreeNode
    }else if root.Val < val {
        root.Right = insertIntoBST(root.Right, val)
    }else if root.Val > val {
        root.Left = insertIntoBST(root.Left, val)
    }
    return root
}
~~~

### 题三 [验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

对这颗二叉树进行中序遍历，序列需要单调递增。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isValidBST(root *TreeNode) bool {
    var stack Stack
    var t = -(1 << 31) - 1
    for root != nil || !stack.isEmpty() {
        for root != nil {
            stack.push(root)
            root = root.Left
        }

        root = stack.pop()

        if root.Val > t {
            t = root.Val
        } else {
            return false
        }

        root = root.Right
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

### 题四 [两数之和IV](https://leetcode-cn.com/problems/two-sum-iv-input-is-a-bst/)

这道使用使用map做即可，前面有类似题，只不过数据类型换成了二叉树。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func findTarget(root *TreeNode, k int) bool {
    var stack Stack
    m := make(map[int]bool)

    for root != nil || !stack.isEmpty() {
        for root != nil {
            stack.push(root)
            root = root.Left
        }

        root = stack.pop()

        if _, ok := m[k - root.Val]; ok {
            return true
        }

        m[root.Val] = true

        root = root.Right
    }

    return false
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

### 题五 [二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

因为给定值肯定在二叉树中，所以只要根节点不都大于or小于p，q的值，那么那肯定在该根节点的左右子树甚至就是根节点。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val   int
 *     Left  *TreeNode
 *     Right *TreeNode
 * }
 */

func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {

    for root != nil {
        if root.Val > p.Val && root.Val > q.Val {
            root = root.Left
            continue
        }

        if root.Val < p.Val && root.Val < q.Val {
            root = root.Right
            continue
        }

        break
    }
    return root
}
~~~

## 今日总结

感觉自己对于二叉树的还是不太熟悉，还需要加强学习，依旧需要继续打卡。