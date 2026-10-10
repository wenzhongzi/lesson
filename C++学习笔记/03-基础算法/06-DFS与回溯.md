# DFS 与回溯

DFS（深度优先搜索）先沿一条分支深入，再返回处理其他分支。回溯用于枚举选择：做选择 → 递归 → 撤销选择，使下一条分支从相同状态开始。

## 是否撤销访问标记

| 任务 | 标记含义 | 返回时撤销 |
| --- | --- | --- |
| 枚举迷宫简单路径、排列、皇后方案 | 当前路径中已经使用 | 是 |
| 遍历图、统计连通块 | 整次搜索中已经访问 | 否 |

不是所有 DFS 都需要回溯。递归终止条件、合法性判断和标记含义应先确定。

## 迷宫：统计不重复经过格子的路径

需要 `<functional>`、`<string>`、`<vector>`。地图 `'#'` 表示障碍，其他格子可走，坐标从 0 开始。只适合较小棋盘，简单路径数量可能非常大。

```cpp
long long countMazePaths(const vector<string>& grid,
                         int sx, int sy, int tx, int ty) {
    int n = static_cast<int>(grid.size());
    if (n == 0 || grid[0].empty()) return 0;
    int m = static_cast<int>(grid[0].size());
    auto valid = [&](int x, int y) {
        return x >= 0 && x < n && y >= 0 && y < m && grid[x][y] != '#';
    };
    if (!valid(sx, sy) || !valid(tx, ty)) return 0;
    vector<vector<bool>> used(n, vector<bool>(m, false));
    const int dx[] = {-1, 1, 0, 0}, dy[] = {0, 0, -1, 1};
    function<long long(int, int)> dfs = [&](int x, int y) -> long long {
        if (x == tx && y == ty) return 1;
        long long count = 0;
        for (int d = 0; d < 4; ++d) {
            int nx = x + dx[d], ny = y + dy[d];
            if (!valid(nx, ny) || used[nx][ny]) continue;
            used[nx][ny] = true;
            count += dfs(nx, ny);
            used[nx][ny] = false;
        }
        return count;
    };
    used[sx][sy] = true;  // 起点也必须标记，防止再次走回起点
    return dfs(sx, sy);
}
```

要求地图为矩形，答案能用 `long long` 表示。此算法统计所有简单路径，不能用于高效求最短路径；等权最短路径用 [BFS](07-BFS.md)。

## 排列与组合

排列在每层枚举未使用元素；组合让下一层从 `i+1` 开始，避免把同一组数按不同顺序重复枚举。需要 `<functional>`、`<vector>`。

```cpp
// 枚举 1..n 的全部排列；要求 n >= 0，结果规模需能放入内存。
vector<vector<int>> permutations(int n) {
    vector<vector<int>> result;
    vector<int> path;
    vector<bool> used(n + 1, false);
    function<void()> dfs = [&] {
        if (static_cast<int>(path.size()) == n) {
            result.push_back(path);
            return;
        }
        for (int i = 1; i <= n; ++i) {
            if (used[i]) continue;
            used[i] = true;
            path.push_back(i);
            dfs();
            path.pop_back();
            used[i] = false;
        }
    };
    dfs();
    return result;
}
```

```cpp
// 从 1..n 中选 k 个数；要求 n >= 0、0 <= k <= n。
vector<vector<int>> combinations(int n, int k) {
    vector<vector<int>> result;
    vector<int> path;
    function<void(int)> dfs = [&](int start) {
        if (static_cast<int>(path.size()) == k) {
            result.push_back(path);
            return;
        }
        int need = k - static_cast<int>(path.size());
        for (int i = start; i <= n - need + 1; ++i) {
            path.push_back(i);
            dfs(i + 1);
            path.pop_back();
        }
    };
    dfs(1);
    return result;
}
```

组合中的上界剪枝保证剩余数字足够填满位置。排列也可用[标准库排列函数](../01-C++基础/02-字符串与常用STL.md)。

## 洪水填充：统计连通块

下面统计四方向相连的 `'W'` 区域。需要 `<functional>`、`<string>`、`<vector>`。若题目允许斜向相连，应改为八个方向。

```cpp
int countWaterComponents(const vector<string>& grid) {
    int n = static_cast<int>(grid.size());
    if (n == 0 || grid[0].empty()) return 0;
    int m = static_cast<int>(grid[0].size());
    vector<vector<bool>> vis(n, vector<bool>(m, false));
    const int dx[] = {-1, 1, 0, 0}, dy[] = {0, 0, -1, 1};
    function<void(int, int)> dfs = [&](int x, int y) {
        if (x < 0 || x >= n || y < 0 || y >= m) return;
        if (vis[x][y] || grid[x][y] != 'W') return;
        vis[x][y] = true;  // 永久标记，不撤销
        for (int d = 0; d < 4; ++d) dfs(x + dx[d], y + dy[d]);
    };
    int count = 0;
    for (int x = 0; x < n; ++x)
        for (int y = 0; y < m; ++y)
            if (grid[x][y] == 'W' && !vis[x][y]) {
                ++count;
                dfs(x, y);
            }
    return count;
}
```

要求矩形地图。时间与访问数组空间均为 `O(nm)`，递归栈最坏也可达 `O(nm)`；大地图应改用显式栈或队列。

## N 皇后

逐行放置，每行放一个皇后。列相同、`row-col` 相同或 `row+col` 相同都会冲突。需要 `<functional>`、`<vector>`。

```cpp
// 返回方案，方案中的列号从 0 开始；只适合较小 n。
vector<vector<int>> nQueens(int n) {
    if (n < 0) return {};
    vector<vector<int>> result;
    vector<int> path(n);
    vector<bool> col(n, false), diag1(2 * n + 1, false), diag2(2 * n + 1, false);
    function<void(int)> dfs = [&](int row) {
        if (row == n) {
            result.push_back(path);
            return;
        }
        for (int c = 0; c < n; ++c) {
            int d1 = row - c + n, d2 = row + c;
            if (col[c] || diag1[d1] || diag2[d2]) continue;
            path[row] = c;
            col[c] = diag1[d1] = diag2[d2] = true;
            dfs(row + 1);
            col[c] = diag1[d1] = diag2[d2] = false;
        }
    };
    dfs(0);
    return result;
}
```

八皇后有 92 个方案。原笔记要求“只输出前三个方案，再输出总数”时，可在找到方案的递归终点计数并仅输出前三个，避免保存全部方案；题目使用 1 基列号时，输出加 1。
