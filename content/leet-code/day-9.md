---
title: "LeetCode Day 9"
date: 2022-01-17T11:12:35+08:00
draft: false
slug: day-9
---

昨天因为题目做完后已经太晚了，所以今天才写好博客，昨天是3道二叉树题。

## 今日题目

### 题一 [二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

对于这题我们使用slice来模拟堆。

遇到一个节点先输出，将其右子树节点压入栈，继续遍历左子树。

左子树遍历完后，从栈顶弹出上个右子树节点，然后对它进行前序遍历。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func preorderTraversal(root *TreeNode) []int {
    var stack Stack
    var out []int

    for root != nil || !stack.isEmpty() {
        for root != nil {
            out = append(out, root.Val)
            stack.push(root.Right)
            root = root.Left
            continue
        }

        root = stack.pop()

    }
    return out
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

### 题二 [二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

遇到一个节点，将它压入栈，然后遍历他的左子树。

当左子树遍历结束后，栈弹出一个节点，输出它。

然后中序遍历它的右子树。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func inorderTraversal(root *TreeNode) []int {
    var stack Stack
    var out []int

    for root != nil || !stack.isEmpty() {
        
        for root != nil {
            stack.push(root)
            root = root.Left
            continue
        }

        root = stack.pop()
        out = append(out, root.Val)
        root = root.Right
    }
    return out
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

### 题三 [二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

遇到一个节点，将它压入栈，然后遍历他的左子树。

当左子树遍历结束后，栈弹出一个节点，判断弹出节点的右子树是否已经遍历or没有右子树。如果是，就输出它，并将它置为nil。如果不是，将它放回，然后后序遍历它的右子树。

~~~
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */

func postorderTraversal(root *TreeNode) []int {
    var stack Stack
    var out []int
    //m := make(map[*TreeNode]bool)
    var lastPassNode *TreeNode
    for root != nil || !stack.isEmpty() {
        
        for root != nil {
            stack.push(root)
            root = root.Left
            continue
        }

        root = stack.pop()
        if root.Right == lastPassNode || root.Right == nil {
            out = append(out, root.Val)
            lastPassNode = root
            root = nil
        } else {
            stack.push(root)
            root = root.Right
        }

    }
    return out
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

今天主要是对于二叉树的遍历的题，前序和中序较为简单，但是后序就复杂些，需要2次访问根节点。

初学二叉树，做题还不够熟练，还需勤加练习。