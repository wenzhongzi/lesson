# BFS

BFS（广度优先搜索）使用队列按层扩展。**无权图或所有边权相同**时，首次到达一个节点的层数就是从起点出发的最少边数。

## 棋盘最少步数

需要 `<queue>`、`<string>`、`<utility>`、`<vector>`。矩形地图中 `'#'` 是障碍，其他格子可走；四方向每次走一步，坐标从 0 开始。

```cpp
vector<vector<int>> gridDistances(const vector<string>& grid, int sx, int sy) {
    int n = static_cast<int>(grid.size());
    if (n == 0) return {};
    int m = static_cast<int>(grid[0].size());
    vector<vector<int>> dis(n, vector<int>(m, -1));
    if (sx < 0 || sx >= n || sy < 0 || sy >= m || grid[sx][sy] == '#')
        return dis;
    const int dx[] = {-1, 1, 0, 0}, dy[] = {0, 0, -1, 1};
    queue<pair<int, int>> q;
    dis[sx][sy] = 0;
    q.push({sx, sy});
    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();
        for (int d = 0; d < 4; ++d) {
            int nx = x + dx[d], ny = y + dy[d];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
            if (grid[nx][ny] == '#' || dis[nx][ny] != -1) continue;
            dis[nx][ny] = dis[x][y] + 1;  // 入队时就标记
            q.push({nx, ny});
        }
    }
    return dis;
}
```

`dis == -1` 同时表示未访问或不可达，不再重复维护 `vis` 和节点中的步数。先复制队首，再 `pop`，之后扩展邻居；不要在 `pop` 后继续使用队首引用。

起点距离为 0。走马步时把方向数组改为 8 个马步方向，并遍历全部方向。一般图用邻接表代替方向数组。

棋盘时间和空间均为 `O(nm)`；一般图时间 `O(V+E)`。存在不同边权时，普通 BFS 不保证最短路，不能直接套用。
