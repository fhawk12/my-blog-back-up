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
int binary_search(int l, int r, int t)
{
    while (l < r)
    {
        int mid = l + (r - l) / 2;
        if (f(mid) == t)
            return mid;
        if (mid < t)
        {
            l = mid + 1;
        }
        else
        {
            r = mid;
        }
    }

    return -1;
}
```

### Upper Bound

```cpp
int upper_bound(int l, int r, int t)
{
    while (l < r)
    {
        int mid = l + (r - l) / 2;
        if (f(mid) <= t)
        {
            l = mid + 1;
        }
        else
        {
            r = mid;
        }
    }

    return l;
}
```

### Lower Bound

```cpp
int LCS(string s, string t)
{
    int n = s.size(), m = t.size();
    vector<vector<int>> dp(n + 1, vector<int>(m + 1));
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            if (s[i] == t[j])
            {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            }
            else
            {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }

    return dp[n][m];
}
```

## LCS(Longest Common Subsequence)

[1035. Uncrossed Lines](https://leetcode.com/problems/uncrossed-lines/)

```cpp
int LCS(string s, string t)
{
    int n = s.size(), m = t.size();
    vector<vector<int>> dp(n + 1, vector<int>(m + 1));
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            if (s[i] == t[j])
            {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            }
            else
            {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }

    return dp[n][m];
}
```

## Stack

### monotonic stack

[1130. 叶值的最小代价生成树](https://leetcode.cn/problems/minimum-cost-tree-from-leaf-values/)  
[2517. 礼盒的最大甜蜜度](https://leetcode.cn/problems/maximum-tastiness-of-candy-basket/)

```cpp
void monotonicStack(vector<int> nums)
{
    stack<int> st;
    for (int x : nums)
    {
        while (!st.empty() && x < nums.top())
        {
            nums.top();
        }
        st.push(x);
    }
}
```

// TODO add graph template shortest path

## Graph

### Shortest Path

#### Dijkstra
