---
title: "LeetCode Day 8"
date: 2022-01-15T22:31:44+08:00
draft: false
slug: dqy-8
---

今天继续leetcode打卡，今天是链表和队列相关的2道题。

## 今日题目

### 题一 [有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

这道题我们需要用到栈stack，我们使用slice来模拟栈，压栈append(stack, v)，出栈stack = stack[:len(stack) -1]。

~~~
func isValid(s string) bool {
    ss := []byte(s)

    stack := make([]byte,0, len(ss))

    for _, v := range ss {
        if len(stack) == 0 {
            stack = append(stack, v)
            continue
        }


        if v == ')' && stack[len(stack) - 1] == '(' {
            stack = stack[:len(stack) -1]
            continue
        }

        if v == ']' && stack[len(stack) - 1] == '[' {
            stack = stack[:len(stack) -1]
            continue
        }

        if v == '}' && stack[len(stack) - 1] == '{' {
            stack = stack[:len(stack) -1]
            continue
        }

        stack = append(stack, v)
    }

    return len(stack) == 0
    
}
~~~

### 题二 [用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

对于这道我们选择先使用链表list来模拟实现栈stack，然后将队列用一个输入栈，一个输出栈，一个栈状态bool变量来表示。输入前确保所有元素都在输入栈，输入压入输入栈。输出前确保所有元素都在输出栈，输出从输出栈中弹出。

~~~
type ListNode struct {
    Val int
    Next *ListNode
}

type Stack struct {
    p *ListNode
    size int
}

func Newstack() *Stack {
    var stack *Stack
    return stack
}

func (this *Stack) push(x int) {
    var Node = &ListNode{x, this.p}
    this.size ++
    this.p = Node
}

func (this *Stack) pop() int {
    var x = this.p.Val
    this.size --
    this.p = this.p.Next
    return x
}


func (this *Stack) isEmpty() bool {
    return this.size == 0
}

type MyQueue struct {
    inputStack Stack
    outputStack Stack
    isOutput bool
}

func Constructor() MyQueue {
    var Queue MyQueue
    return Queue
}


func (this *MyQueue) Push(x int)  {
    if this.isOutput {
        for !this.outputStack.isEmpty() {
            this.inputStack.push( this.outputStack.pop() )
        }
        this.isOutput = !this.isOutput
    }
    this.inputStack.push(x)
}


func (this *MyQueue) Pop() int {
    if !this.isOutput {
        for !this.inputStack.isEmpty() {
            this.outputStack.push( this.inputStack.pop() )
        }
        this.isOutput = !this.isOutput
    }
    
    return this.outputStack.pop()
}


func (this *MyQueue) Peek() int {
    if !this.isOutput {
        for !this.inputStack.isEmpty() {
            this.outputStack.push( this.inputStack.pop() )
        } 
        this.isOutput = !this.isOutput
    }
    x := this.outputStack.pop()
    this.outputStack.push(x)
    return x
}


func (this *MyQueue) Empty() bool {
    return this.inputStack.isEmpty() && this.outputStack.isEmpty()
}


/**
 * Your MyQueue object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * param_2 := obj.Pop();
 * param_3 := obj.Peek();
 * param_4 := obj.Empty();
 */
~~~

## 今日总结

今天题目主要是关于stack和queue结构的基础题，因为看来浙江大学的数据结构的视频，所以做起来得心应手，也证明学习这个视频是有效的，明天继续学习它。