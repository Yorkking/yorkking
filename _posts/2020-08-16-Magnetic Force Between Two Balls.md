---
title: Magnetic Force Between Two Balls
tags:
 - 算法
---

## 题目

#### [5489. Magnetic Force Between Two Balls](https://leetcode-cn.com/problems/magnetic-force-between-two-balls/)

难度中等13

In universe Earth C-137, Rick discovered a special form of magnetic force between two balls if they are put in his new invented basket. Rick has `n` empty baskets, the `ith` basket is at `position[i]`, Morty has `m` balls and needs to distribute the balls into the baskets such that the **minimum magnetic force** between any two balls is **maximum**.

Rick stated that magnetic force between two different balls at positions `x` and `y` is `|x - y|`.

Given the integer array `position` and the integer `m`. Return *the required force*.

Constraints:

> n == position.length
> 2 <= n <= 10^5
> 1 <= position[i] <= 10^9
> All integers in position are distinct.
> 2 <= m <= position.length

## 解法

这道题是用二分的方法来解决。因为此题是求一个最大的间隔，可以使用二分的方法来求解。首先是确定上下界：毫无疑问，下界为1。那么上界呢？假设坐标 position 从小到大排序，最小的坐标为 minP, 最大坐标为 maxP，m 个球的情况下，那么最大距离必然小于等于$(maxP-minP)/(m-1)$。所以上下界是确定了。那么如果给定了一个间距 x，怎样能判断该 x 是否满足条件呢？这其实可以直接采用贪心的思想，直接遍历排序后的postion，记录距离大于 x 的个数，如果大于等于 m，那么这个 x 必然是可以的。

代码如下：

```C++
class Solution {
public:
    bool check(vector<int>& position, int dist,int m){
        int cnt = 1;
        int l = position[0];
        for(int i=1;i<position.size();++i){
            if(position[i] - l >= dist){
                cnt++;
                if(cnt >= m) return true;
                l = position[i];
            }
        }
        return cnt >= m;
    }
    int maxDistance(vector<int>& position, int m) {
        sort(position.begin(),position.end());
        int down = 1;
        int up = (position.back() - position[0])/(m-1);

        while(down <= up){
            int mid = down + (up-down)/2;
            if(check(position,mid,m)){
                down = mid+1;
            }else{
                up = mid-1;
            }
        }
        return up;
    }
};
```



