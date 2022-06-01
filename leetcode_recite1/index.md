# Leetcode全速通1


## 总览
### 算法：27题
![](/image_leetcode_recite/pic1.png)
### 数据结构：18题
![](/image_leetcode_recite/pic2.png)
### 图：6题
![](/image_leetcode_recite/pic3.png)
### dp：9题
![](/image_leetcode_recite/pic4.png)

## 二分查找-binary search
* 一堆数中"找到指定的数"这类问题
* 一堆数必须要存在于数组中、有序排列（无序的话可以用二分法来当猜答案的方法）
* 所以链表存储就无法使用二分查找了

### 模板
![](/image_leetcode_recite/pic5.png)

### 模板代码
```Java
package com.swagger.leetcode.binary_search;

public class main {
    public static int binarySearch1(int[] nums, int target){
        int start = 0;
        int end = nums.length - 1;
        while (start <= end){
            int mid = start + (end - start)/2;
            if (nums[mid] == target){
                return mid;
            }else if (nums[mid] < target){
                start = mid + 1;
            }else {
                end = mid - 1;
            }
        }
        return -1;
    }
}
```

### leetcode 278
```Java
package com.swagger.leetcode.binary_search;

public class leetcode278 {
    public static int firstBadVersion(int n){
        int left = 1;
        int right = n;
        while (left <= right){
            int mid = left + (right - left)/2;
            if (!isBadVersion(mid)){
                left = mid + 1;
            }
            else {
                right = mid - 1;
            }
        }
        return left; // mid是最后一个good version，所以left = mid + 1 之后直接返回left即可
    }

    // 这个没有实现
    public static boolean isBadVersion(int version){
        return false;
    }
}
```

### leetcode 410
* 二分法去猜最后的答案
* 关键在于valid()中如何判断是猜大了还是猜小了
```Java
package com.swagger.leetcode.binary_search;

import java.util.Arrays;

public class leetcode410 {
    public static int splitArray(int[] nums, int m){
        int sum = Arrays.stream(nums).sum();
        int max = Arrays.stream(nums).max().getAsInt();
        return binary(nums, m, sum, max);
    }

    public static int binary(int[] nums, int m, int high, int low){
        int mid = 0;
        while (low <= high){
            mid = low + (high - low)/2;
            if (valid(nums, m, mid)){
                high = mid - 1;
            }else {
                low = mid + 1;
            }
        }
        return low;
    }

    public static boolean valid(int[] nums, int m, int subArraySum){
        int curSum = 0;
        int count = 1;

        for (int num : nums) {
            curSum += num;
            if (curSum > subArraySum){
                // 第一次分割，不能让curSum超过自己猜的subArraySum
                curSum = 0;
                curSum += num;
                count ++ ; // 分割块数+1 
                
                if (count > m){
                    return false; // 为了让每块和不超过自己猜的subArraySum，导致块数超过m，说明猜小了
                }
            }
        }
        return true; // 整个数组遍历完了，都满足，说明猜大了
    }
}
```

### 同类型题目
* leetcode 1552
* leetcode 1482
* leetcode 1283
* leetcode 1292






