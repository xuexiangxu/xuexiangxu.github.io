---
title: 移除元素
date: 2023-08-09 22:10:36
tags: [计算机, 学习]
categories:
- [计算机, 算法]
---

## 移除元素
LeetCode 27

给定一个数组和一个目标值，删除目标值的元素，返回数组的大小

由于数组是连续的，从中间删除一个元素后，后面的元素要向前移动一位，时间复杂度是`O(n)`

### 思路：双指针
创建一个新的数组
- 快指针对应原数组
- 慢指针对应新数组
移动快指针，如果快指针所指向的位置的值不等于target，把该值更新给慢指针指向的位置
```java
public int removeElement(int[] nums, int target) {
    int slow = 0;
    for (int fast = 0; fast < nums.length; fast++) {
        if (nums[fast] != target) {
            nums[slow++] = nums[fast];
        }
    }
    return slow;
}
```
