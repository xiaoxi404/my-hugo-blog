---
title: "LeetCode Day 5"
date: 2022-01-11T23:03:13+08:00
draft: false
slug: day-5
---
今天被datalab的一道题目困扰了好久，所以leetcode刷的比较晚，但继续LeetCode刷题打卡，今天依旧是数组方面的2道题。


## 今日题目

### 题一 [有效的数独](https://leetcode-cn.com/problems/valid-sudoku/)

对于这道题目我也没想出特别好的方法，只能搞个3个循环3次遍历数独表，对于题目的第三个要求，只要小心处理位置关系即可，利用map来判断是否有重复值出现。

~~~
func isValidSudoku(board [][]byte) bool {
   
    for i := 0; i < 9; i ++ {
        m := getMap()
        for k :=0; k < 9; k ++{
            y := board[i][k]
            if  y == '.' {
                continue
            }
            if _, ok := m[y]; ok  {
                return false
            } else {
                m[y] = true
            }
        }
    }

    for i := 0; i < 9; i ++ {
         m := getMap()
        for k :=0; k < 9; k ++{
            y := board[k][i]
            if y == '.' {
                continue
            }
            if _, ok := m[y]; ok  {
                return false
            } else {
                m[y] = true
            }
        }
        
    }

    for i := 0; i < 9; i ++ {
        z1 := i / 3 * 3
        z2 := i % 3 * 3
        m := getMap()
        for k := 0; k < 9; k++ {
            y := board[z1 + k/3][z2 + k%3]
            if y == '.' {
                continue
            }
            if _, ok := m[y]; ok  {
                return false
            } else {
                m[y] = true
            }
        }
    }

    return true
}

func getMap () map[byte]bool {
    return make(map[byte]bool, 9)
}
~~~

### 题二 [矩阵置零](https://leetcode-cn.com/problems/set-matrix-zeroes/)

对于这道题目我选择先遍历矩阵将值为0的行和列信息记录到map中，然后如果第二次遍历时当前元素的行和列出现在map中，就将其置0.

~~~
func setZeroes(matrix [][]int)  {
    m1 := make(map[int]bool, len(matrix))
    m2 := make(map[int]bool, len(matrix[0]))

    for i := 0; i < len(matrix); i ++ {
        for k := 0; k < len(matrix[0]); k ++ {
            if matrix[i][k] == 0 {
                m1[i] = true
                m2[k] = true 
            }
        }
    }

     for i := 0; i < len(matrix); i ++ {
        for k := 0; k < len(matrix[0]); k ++ {
            if _, ok := m1[i]; ok {
                matrix[i][k] = 0
                continue
            }

            if _, ok := m2[k]; ok {
                matrix[i][k] = 0
            }
        }
    }

}
~~~

## 今日总结

今天的题目思路都比较清晰，但是程序在内存消耗上一直表现不佳，感觉在闲暇时，十分有必要去看看别人的优秀解。

今天做题时看了一下《GO语言学习笔记》的map部分，感觉收益匪浅，感觉有机会应该重新读一下这本书。