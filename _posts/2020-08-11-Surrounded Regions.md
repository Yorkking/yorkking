---
title: Surrounded Regions
tags:
 - 算法
---

## 题目

#### [130. Surrounded Regions](https://leetcode-cn.com/problems/surrounded-regions/)

难度中等282

Given a 2D board containing `'X'` and `'O'` (**the letter O**), capture all regions surrounded by `'X'`.

A region is captured by flipping all `'O'`s into `'X'`s in that surrounded region.

**Example:**

```
X X X X
X O O X
X X O X
X O X X
```

After running your function, the board should be:

```
X X X X
X X X X
X X X X
X O X X
```

**Explanation:**

Surrounded regions shouldn’t be on the border, which means that any `'O'` on the border of the board are not flipped to `'X'`. Any `'O'` that is not on the border and it is not connected to an `'O'` on the border will be flipped to `'X'`. Two cells are connected if they are adjacent cells connected horizontally or vertically.

## 解答

这题思路不难，应该是一道典型的`DFS` 算法的题目，`dfs` 算法就是写起来麻烦，想起之前面试时写 dfs 真的写到怀疑人生……

思路就是：除了和四条边上的 `O` 相连的 `O`，其余的都直接变为 `X`。所以对四条边上的 `O` 进行 dfs 遍历，把与之相连的全部标记或直接修改为其他符号比如 `.`。最后在遍历整个数组，`.` 的就修改为 `O`，其余变为 `X` 。

代码如下：

```C++
class Solution {
public:
    void dfs(vector<vector<char>>& board, int x, int y){
        board[x][y] = '.';
        for(int i=0;i<4;i++){
            int xx = x + dx[i];
            int yy = y + dy[i];
            if(xx >=0 && yy >=0 && xx <m && yy <n && board[xx][yy] == 'O'){
                dfs(board,xx,yy);
            }
        }
    }
    void solve(vector<vector<char>>& board) {
        m =board.size();
        if(m<=0) return;
        n = board[0].size();
        if(n<=0) return;
        for(int i=0;i<m;++i){
            if(board[i][0] == 'O'){
                dfs(board,i,0);
            }
            if(board[i][n-1] == 'O'){
                dfs(board,i,n-1);
            }
        }
        for(int i=0;i<n;++i){
            if(board[0][i] == 'O'){
                dfs(board,0,i);
            }
            if(board[m-1][i] == 'O'){
                dfs(board,m-1,i);
            }
        }
        for(int i=0;i<m;++i){
            for(int j=0;j<n;++j){
                if(board[i][j] == '.') board[i][j] = 'O';
                else board[i][j] = 'X';
            }
        }
    }
    int dx[4] = {0,0,-1,1};
    int dy[4] = {-1,1,0,0};
    int m;
    int n;
};
```

