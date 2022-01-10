---
title: "LeetCode Day 4"
date: 2022-01-10T20:13:09+08:00
draft: false
slug: day-4
---
今天继续Leet-Code刷题打卡，今日依旧是数组方面的2道题。

## 今日题目

### 题一 [重塑矩阵](https://leetcode-cn.com/problems/reshape-the-matrix/)
这道题目较为简单，直接遍历数组，k/c等于重塑矩阵的行。

~~~
func matrixReshape(mat [][]int, r int, c int) [][]int {
    if len(mat) * len(mat[0]) != r*c {
        return mat
    }
    
    var s = make([][]int, r)
    k := 0

    for i := range mat {
        for j := range mat[i] {
            s[k/c] = append(s[k/c], mat[i][j])
             k ++
        }
    }

    return s
}
~~~

### 题目二 [杨辉三角](https://leetcode-cn.com/problems/pascals-triangle/)
三角每一行的首和末尾为1，中间数等于上一行的同一位置的数和上一行前一位置的数相加。

~~~
func generate(numRows int) [][]int {
    s := make([][]int, numRows)
    
    for i := range s {
         
        for k := 0; k < i + 1; k++ {
            if k == 0 || k == i {
                s[i] = append(s[i], 1)
                continue
            }

            s[i] = append(s[i], s[i-1][k-1] + s[i-1][k])
            
        } 

    }
    return s
}
~~~

## 今日总结
因为今日题目较为简单，无总结。