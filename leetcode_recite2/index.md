# Leetcode全速通2


## DFS / BFS 
### BFS 模板
![](/image_leetcode_recite/pic7.png)

### DFS 模版
![](/image_leetcode_recite/pic8.png)
* backtracking 和 DFS 有些许相似
* backtraking 是更加广义上的一个算法
* DFS 是backtracking在图数结构中的具体应用形势

### 102 Binary Tree Level Order Traversal
* BFS，层序遍历
* 注意每层的遍历开始时都要重新new一个level数组
* 遍历本层的内容时，需要提前将队列大小存储，并以此大小循环，因为后面队列大小的实时变化的

#### 代码实战
```Java
package com.swagger.leetcode.bfs_dfs;

import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

public class leetcode102 {
    public static List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>();

        // 根节点入队
        if (root != null) {
            q.offer(root);
        }

        // 如果队列没有空则进入循环，最后队列空了，说明已经没有子节点了
        while (!q.isEmpty()){
            // 获取当前队列大小，也就是本层元素的个数
            int size = q.size();
            // 每次都new一个level， 用以存储本层的所有元素
            List<Integer> level = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                // 出队，且向本层level中添加元素
                TreeNode cur = q.poll();
                level.add(cur.val);

                // 左右孩子入队，但其实本层不会遍历到，因为size为队列初始大小
                if (cur.left != null){
                    q.offer(cur.left);
                }
                if (cur.right != null){
                    q.offer(cur.right);
                }
            }
            res.add(level);
        }

        return res;
    }
}
```



### 200 Number of Islands
* DFS
* 遍历每个点的时候置0
* DFS的返回条件是是否达到了边界或者碰到的点为0
* 如果没有返回，则向四周进行DFS

#### 代码实战
```Java
package com.swagger.leetcode.bfs_dfs;

public class leetcode200 {
    public static int numIslands(char[][] grid) {
        if (grid.length == 0) {
            return 0;
        }
        int M = grid.length;
        int N = grid[0].length;
        int res = 0;

        for (int i = 0; i < M; i++) {
            for (int j = 0; j < N; j++) {
                if (grid[i][j] == '1') {
                    res++;
                    dfs(grid, i, j);
                }
            }
        }
        return res;
    }

    public static void dfs(char[][] grid, int row, int col) {
        if (row < 0 || row >= grid.length || col < 0 || col >= grid[0].length || grid[row][col] == '0') {
            return; // 如果超出了这个范围 或者已经碰到0了 就 退出
        }

        grid[row][col] = '0'; // 遍历过的点，置为0

        // 向四周扩散
        dfs(grid, row - 1, col);
        dfs(grid, row + 1, col);
        dfs(grid, row, col - 1);
        dfs(grid, row, col + 1);
    }
}
```

### 490 The Maze
* 注意要记录当前的点是否被遍历过
* 停止条件为是否被遍历过，或者是否为最终点
* 移动时注意检测边缘和中间的阻挡墙壁

#### 代码实战
```Java
package com.swagger.leetcode.bfs_dfs;

public class leetcode490 {

    

    public boolean hasPath(int[][] maze, int[] start, int[] destination) {
        int M = maze.length;
        int N = maze[0].length;
        // 记录当前位置是否被访问过
        boolean[][] visited = new boolean[M][N];
        return dfs(maze, visited, start, destination);
    }

    private boolean dfs(int[][] maze, boolean[][] visited, int[] cur, int[] destination) {
        int M = maze.length;
        int N = maze[0].length;

        // 两种停止条件
        if (visited[cur[0]][cur[1]]) { // 如果已经visited了
            return false;
        }
        if (cur[0] == destination[0] && cur[1] == destination[1]) {// 如果已经到达目标点了
            return true;
        }

        // 此点标记为visited
        visited[cur[0]][cur[1]] = true;

        for (int[] dir : dirs) {
            int x = cur[0], y = cur[1];
            // 移动
            while (x + dir[0] >= 0 && y + dir[1] >= 0 &&
                    x + dir[0] < M && y + dir[1] < N &&
                    maze[x + dir[0]][y + dir[1]] == 0) {
                x = x + dir[0];
                y = y + dir[0];
            }
            // 移动之后开始继续向下递归
            if (dfs(maze, visited, new int[]{x, y}, destination)) {
                return true;
            }
        }
        return false;
    }
}

```


