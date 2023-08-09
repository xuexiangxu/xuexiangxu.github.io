---
title: 二分法
date: 2023-08-09 21:40:52
tags: [计算机, 学习]
categories:
- [计算机, 算法]
---
## 二分法
LeetCode 704

易错点：
1. 在写while循环时，判断条件是**`left < right`，还是`left <= right`
2. 在更新边界时，是`right = mid`还是`right = mid - 1`

区间：[left, right)
由于是左闭右开区间，最左边不可能等于最右边，所以判断条件应该是`left < right`
```java
while (left < right) {
    int mid = (left + right) / 2;
    if (nums[mid] > target) {
        right = mid;
    } else if (nums[mid] < target) {
        left = mid + 1;
    } else {
        return mid;
    }
}
```