---
title: "Algorithm"
date: 2023-04-30T09:24:52+08:00
draft: false
tags: [algorithm]
categories: [algorithm]
---

## Binary Search
[1498. Number of Subsequences That Satisfy the Given Sum Condition](https://leetcode.com/problems/number-of-subsequences-that-satisfy-the-given-sum-condition/)  
[1964. Find the Longest Valid Obstacle Course at Each Position](https://leetcode.com/problems/find-the-longest-valid-obstacle-course-at-each-position/)

### Single Target
```cpp
int binary_search(int l, int r, int t){
    while(l < r){
         int mid = l+(r-l)/2;
         if(f(mid) == t) return mid;
         if(mid < t){
            l = mid+1;
         } else {
            r = mid;
         }
    }

    return -1;
}
```

### Upper Bound
```cpp
int upper_bound(int l, int r, int t){
    while(l < r){
        int mid = l+(r-l)/2;
        if(f(mid) <= t){
            l = mid+1;
        } else {
            r = mid;
        }
    }

    return l;
}
```

### Lower Bound
```cpp
int lower_bound(int l, int r, int t){
    while(l < r){
        int mid = l+(r-l)/2;
        if(f(mid) < t){
            l = mid+1;
        } else {
            r = mid;
        }
    }

    return l;
}

```
