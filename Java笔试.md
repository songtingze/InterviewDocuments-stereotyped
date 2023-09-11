# Java-笔试

## 数组

### 二分查找

[力扣题目链接](https://leetcode.cn/problems/binary-search/)

取中间值时需要考虑可能溢出：mid = left + ((right - left) >> 1)

- 左闭右闭 (记住这个就好)

  两端值都得考虑

```java
class Solution {
    public int search(int[] nums, int target) {
        // 避免当 target 小于nums[0] nums[nums.length - 1]时多次循环运算
        if (target < nums[0] || target > nums[nums.length - 1]) {
            return -1;
        }
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] == target)
                return mid;
            else if (nums[mid] < target)
                left = mid + 1;
            else if (nums[mid] > target)
                right = mid - 1;
        }
        return -1;
    }
}
```

- 左闭右开

  右端值不考虑，可以为nums.length

### 移除元素

[力扣题目链接](https://leetcode.cn/problems/remove-element/)

用数组值替换来代替移除操作，采用双指针，左侧目标值替换右侧非目标值。

- 快慢指针法

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        // 快慢指针
        int slowIndex = 0;
        for (int fastIndex = 0; fastIndex < nums.length; fastIndex++) {
            if (nums[fastIndex] != val) {
                nums[slowIndex] = nums[fastIndex];
                slowIndex++;
            }
        }
        return slowIndex;
    }
}
```

- 相向双指针法

```java
//相向双指针法
class Solution {
    public int removeElement(int[] nums, int val) {
        int left = 0;
        int right = nums.length - 1;
        while(right >= 0 && nums[right] == val) right--; //将right移到从右数第一个值不为val的位置
        while(left <= right) {
            if(nums[left] == val) { //left位置的元素需要移除
                //将right位置的元素移到left（覆盖），right位置移除
                nums[left] = nums[right];
                right--;
            }
            left++;
            while(right >= 0 && nums[right] == val) right--;
        }
        return left;
    }
}
```

### 有序数组的平方

[力扣题目链接](https://leetcode.cn/problems/squares-of-a-sorted-array/)

倒序考虑，最大值只在最两侧。

- 双指针法

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int right = nums.length - 1;
        int left = 0;
        int[] result = new int[nums.length];
        int index = result.length - 1;
        while (left <= right) {
            if (nums[left] * nums[left] > nums[right] * nums[right]) {
                // 正数的相对位置是不变的， 需要调整的是负数平方后的相对位置
                result[index--] = nums[left] * nums[left];
                ++left;
            } else {
                result[index--] = nums[right] * nums[right];
                --right;
            }
        }
        return result;
    }
}
```

### 长度最小的子数组

[力扣题目链接](https://leetcode.cn/problems/minimum-size-subarray-sum/)

- 滑动窗口

  连续子数组的常见思路，动态确定子数组的起点和终点。

  只用一个for循环，这个循环的索引一定是表示 滑动窗口的终止位置。

  在本题中实现滑动窗口，主要确定如下三点：

  - 窗口内是什么？
  - 如何移动窗口的起始位置？
  - 如何移动窗口的结束位置？

```java
class Solution {

    // 滑动窗口
    public int minSubArrayLen(int s, int[] nums) {
        int left = 0;
        int sum = 0;
        int result = Integer.MAX_VALUE;
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];
            while (sum >= s) {
                result = Math.min(result, right - left + 1);
                sum -= nums[left++];
            }
        }
        return result == Integer.MAX_VALUE ? 0 : result;
    }
}
```

### 螺旋矩阵II

[力扣题目链接](https://leetcode.cn/problems/spiral-matrix-ii/)

- 控制循环-循环不变量原则

  求解本题依然是要坚持循环不变量原则。

  模拟顺时针画矩阵的过程:

  - 填充上行从左到右
  - 填充右列从上到下
  - 填充下行从右到左
  - 填充左列从下到上

  由外向内一圈一圈这么画下去。

  每条边都采用**`左闭右开`**的原则，从而保证内部循环判断条件统一。

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int start = 0, end = n - 1;
        int[][] matrix = new int[n][n];
        int i = 1;
        while (i < n*n - 1) {
            for (int j = start; j < end; j ++) {
                matrix[start][j] = i;
                i ++;
            }
            for (int j = start; j < end; j ++) {
                matrix[j][end] = i;
                i ++;
            }
            for (int j = end; j > start; j --) {
                matrix[end][j] = i;
                i ++;
            }
            for (int j = end; j > start; j--) {
                matrix[j][start] = i;
                i ++;
            }
            start ++;
            end --;
        }
        if (i == n*n) {
            matrix[start][end] = i;
        }
        return matrix;
    }
}
```

