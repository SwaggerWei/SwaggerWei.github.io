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
* 工具类leetcode490 中应该还有一个 dirs 的数组，代表移动方向，但是用hugo 打包好push到github时发现build失败，不知道为啥提示错误
* dirs数组补充
![](/image_leetcode_recite/pic10.png)
* github build 错误信息提示
![](/image_leetcode_recite/pic11.png)

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





### 297 Serialize and Deserialize Binary Tree
* 序列化的时候还是套用模板，先看返回条件，再计算，在看如何选择路线向下递归
* 反序列化的时候建树时，首先将String中的内容存入到Queue当中，然后套用模版进行递归

#### 代码实战
```Java
package com.swagger.leetcode.bfs_dfs;

import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;

public class leetcode297 {
    public static String serialize(TreeNode root) {
        if (root == null) return "#";
        // preOrder 进行serialize
        return root.val + "," + serialize(root.left) + "," + serialize(root.right);
    }

    public static TreeNode deserialize(String data) {
        // 逗号隔开
        Queue<String> q = new LinkedList<>(Arrays.asList(data.split(",")));
        return helper(q);
    }

    private static TreeNode helper(Queue<String> q) {
        String s = q.poll();

        // 如果为#，则此点没有节点
        if (s.equals("#")) return null;

        // 新建节点
        TreeNode root = new TreeNode(Integer.valueOf(s));

        // 先左，后右向下递归
        root.left = helper(q);
        root.right = helper(q);

        return root;
    }

    public static void main(String[] args) {
        TreeNode node1 = new TreeNode(1);
        TreeNode node2 = new TreeNode(2);
        TreeNode node3 = new TreeNode(3);
        TreeNode node4 = new TreeNode(4);
        TreeNode node5 = new TreeNode(5);

        node1.left = node2;
        node1.right = node5;
        node2.left = node3;
        node2.right = node4;

        String s = serialize(node1);
        System.out.println(s);

        TreeNode res_root = deserialize(s);
        System.out.println(res_root.val);


    }
}
```

### 124 Binary Tree Maximum Path Sum

### DFS BFS 总结
* BFS：对于解决最短或最少问题特别有效，而且寻找深度小，但缺点是内存耗费量大（需要开大量的数组单元用来存储状态，取决于树本身的形状，扁平还是长条）
* DFS：对干解決谝历和求所有问题有效．对干问题搜索深度小的时候外理谏度讯谏

#### DFS 优缺点
* 扁平型tree内存开销较小
* 能处理子节点较多或树层次过深的情况 （相比BFS)
* 一般用于解决连通性问题（是否有解）
* 只能寻找有解，但无法最快寻找到最优解，寻找最优解同样要遍历所有路径

#### 数据运用和复杂度
![](/image_leetcode_recite/pic9.png)
