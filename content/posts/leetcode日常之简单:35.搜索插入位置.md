---
title: "leetcode日常之简单:35.搜索插入位置"
date: 2021-01-08
draft: false
tags: ["leetcode","算法"]
categories: ["leetcode","算法"]
---

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。


你可以假设数组中无重复元素。


**示例 1:**

```markdown
输入: [1,3,5,6], 5
输出: 2
```

**示例 2:**

```markdown
输入: [1,3,5,6], 2
输出: 1
```

**示例 3:**

```markdown
输入: [1,3,5,6], 7
输出: 4
```

**示例 4:**

```markdown
输入: [1,3,5,6], 0
输出: 0
```

---
**Golang代码:**

```golang
func searchInsert(nums []int, target int) int {
    p := 0
    for k, v := range nums {
        if v == target {
            p = k
            break
        }
        if v < target {
            p = k + 1
        }
        if v > target {
            break
        }
    }
    return p
}
```