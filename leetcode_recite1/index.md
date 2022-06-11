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

### leetcode 300
* 方法1：传统dp + 双指针
* 方法2：暴力构架subsequence，使用二分法进行优化查找

#### 代码实战
```Java
package com.swagger.leetcode.binary_search;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Map;

public class leetcode300 {

    public static int lengthOfLIS(int[] nums){

        // dp + 双指针
//        if (nums.length == 0){
//            return 0;
//        }
//
//        int[] dp = new int[nums.length];
//        Arrays.fill(dp, 1);
//        int res = 1;
//        for (int i = 1; i < nums.length; i++) {
//            for (int j = 0; j < i; j++) {
//                if (nums[j] < nums[i]){
//                    dp[i] = Math.max(dp[j] + 1, dp[i]);
//                }
//                res = Math.max(res, dp[i]);
//            }
//        }
//        return res;


        // 暴力构造subsequence
        ArrayList<Integer> sub = new ArrayList<>();
        sub.add(nums[0]);
        for (int i = 1; i < nums.length; i++) {
            int num = nums[i];
            if (num > sub.get(sub.size() - 1)){
                sub.add(num); // 如果是递增，则加入到递增序列当中
            }else {

                // 更新sub中的element
                int j = 0;
                while (num > sub.get(j)){
                    // 从左开始扫描
                    // 此处还可以不用从左开始扫描，使用二分法查找进行优化，将n方优化成nlogn
                    j++;
                }

                // 比他大才更新
                // 但是这种方法构造出来的subsequence 不是最终的序列
                // 如果要构造最终的序列不能用这种方法
                sub.set(j, num);
            }
        }

        return sub.size();
    }
}
```

## 双指针-two pointer
* 同向双指针 283 move zero；2sum；3sum
* 对向双指针 11 Container with most water；75 sort color；判断回文字符串；字符串反转；中心扩散法
* 快慢指针 142 LinkedList
* quick sort 
* 单调队列和单调栈，sliding window，扫描线

### 283 move zeros 
* 解法1：开辟额外的空间，碰到非0元素append进去，最后末尾添加0
* 解法2：同向双指针，left记录最左0的位置，right 向右找到第一个非0数与left交换，然后移动left，具体实现时忽略了swap操作，直接left控制添加非0数，结尾补0

#### 代码实战
```Java
package com.swagger.leetcode.two_pointers;

public class leetcode283 {
    public static void moveZeroes(int[] nums){
        int left = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0){
                nums[left] = nums[i];
                left ++ ;
            }
        }
        while (left < nums.length){
            nums[left] = 0;
            left++;
        }
    }
}
```




### 11 Container with most water
* 对向双指针法，哪个矮移动哪个，因为宽度肯定是减少的，所以应该保留更高的那个，才有可能找到更大的存水量
* 实施维护res 存储最大的存水量

#### 代码实战
```Java
package com.swagger.leetcode.two_pointers;

public class leetcode11 {
    public static int maxArea(int[] height) {
        int res = 0;
        int left = 0;
        int right = height.length - 1;
        while (left < right){
            int minHeight = Math.min(height[left], height[right]);
            int area = minHeight * (right - left);
            res = Math.max(area, res);

            // 哪边矮就向内移动哪边，因为宽度肯定是减少的，所以应该保留更高的那个，才有可能找到更大的存水量
            if (height[left] < height[right]){
                left ++;
            }else {
                right --;
            }
        }

        return res;
    }
}
```




### 16 3Sum Closest
* 首先进行排序 
* 然后使用三个指针
* 第一个指针a从左往右，为最外层循环
* 第二、三（b、c）个指针在a右侧相向而行，

#### 代码实战
```Java
package com.swagger.leetcode.two_pointers;

import java.util.Arrays;

public class leetcode16 {

    public static void main(String[] args) {
        int[] nums = {0, 2, 1, -3};
        int target = 1;

        int res = threeSumClosest(nums, target);

    }

    public static int threeSumClosest(int[] nums, int target) {
        int res = nums[0] + nums[1] + nums[nums.length - 1];
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 2; i++) {
            int left = i + 1;
            int right = nums.length - 1;
            while (left < right){
                int sum = nums[i] + nums[left] + nums[right];
                if (Math.abs(sum - target) < Math.abs(res - target)){
                    res = sum;
                }
                if (sum > target){
                    right --;
                }else {
                    left ++;
                }
            }
        }

        return res;


    }
}
```






### 142 Linked List Cycle 2
* 快慢指针
* 首先用快慢指针判断是否有环
* 如果有环则将fast = head
* 然后开始同速行驶
* 如果在此相遇，fast的所处的地方即为环的入口

#### 代码实战
```Java
package com.swagger.leetcode.two_pointers;

public class leetcode142 {
    public static ListNode detectCycle(ListNode head){
        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null){
            fast = fast.next.next;
            slow = slow.next;

            if (fast == slow){ // 说明成环了
                // 开始寻找入口的索引
                fast = head;

                // 同速行驶再次相遇时，即为入口的索引
                while (slow != fast){
                    fast = fast.next;
                    slow = slow.next;
                }
                return slow;
            }
        }

        return null;
    }
}
```

## 滑动窗口法-sliding window
* 也是双指针的一种变形，一般为**同向**
* 也有的题目和pq或者是单调栈结合
* 窗口大小可变
* 窗口大小不变

### 模板
一般外层为窗口的右端循环，内层为窗口的左端循环，分为如下三个步骤：
* 1 进：当前遍历的i（窗口右端）进入窗口
* 2 出：当前不符合条件时（窗口左端）退出窗口
* 3 算：计算窗口，更新结果
![](/image_leetcode_recite/pic6.png)


### 209 Minimum Size Subarray sum
* 滑动窗口，外层循环为右边界，内层循环为左边界
* 如果窗口的内容达到了要求，判断size是否更小，更新答案，随后左端右移

#### 代码实战
```Java
package com.swagger.leetcode.slidingWindow;

public class leetcode209 {
    public static int minSubArrayLen(int[] nums, int target){
        // 求满足要求的最小窗口size
        int left = 0;
        int res = Integer.MAX_VALUE;
        int sum = 0;

        // 外层循环为右边界
        for (int i = 0; i < nums.length; i++) {
            sum = sum + nums[i];

            // 内层循环为左边界
            while (sum >= target){ // 如果窗口的内容达到了要求
                res = Math.min(res, i - left + 1); // 判断是否更小， 更新答案

                // 窗口左端右移
                sum = sum - nums[left];
                left ++;
            }
        }

        return res == Integer.MAX_VALUE ? 0 : res;
    }
}
```

### 340 Longest Substring with At Most K Distinct Characters
* HashMap 记录窗口内每个元素出现的次数
* key 为元素，value为出现次数
* 思路和模板一样，计算时判读HashMap的大小是否超出k的范围 

#### 代码实战
```java
package com.swagger.leetcode.slidingWindow;

import java.util.HashMap;

public class leetcode340 {
    public static int lengthOfLongestSubstringKDistinct(String s, int k){
        HashMap<Character, Integer> map = new HashMap<>();
        int left = 0;
        int res = 0;
        for (int i = 0; i < s.length(); i++) {
            // 进
            char cur = s.charAt(i);
            map.put(cur, map.getOrDefault(cur, 0) + 1);

            // 出
            while (map.size() > k){// 判断窗口是否符合大小，如果不符合大小，则进入循环内部
                char c = s.charAt(left);
                // 改变left的字符对应key的value-1
                map.put(c, map.get(c) - 1);
                // 如果已经置0了，则移除map中的对应字符
                if (map.get(c) == 0){
                    map.remove(c);
                }
                // 窗口左端右移
                left++;
            }

            // 算
            res = Math.max(res, i - left + 1);
        }

        return res;
    }
}
```



