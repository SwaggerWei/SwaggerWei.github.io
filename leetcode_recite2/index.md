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


