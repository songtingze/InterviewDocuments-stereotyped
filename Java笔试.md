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

## 链表

单链表、双链表、循环链表（解决约瑟夫环问题）。

- 定义

```java
public class ListNode {
    // 结点的值
    int val;
    // 下一个结点
    ListNode next;
    // 节点的构造函数(无参)
    public ListNode() {
    }
    // 节点的构造函数(有一个参数)
    public ListNode(int val) {
        this.val = val;
    }
    // 节点的构造函数(有两个参数)
    public ListNode(int val, ListNode next) {
        this.val = val;
        this.next = next;
    }
}
```

### 移除链表元素

[力扣题目链接](https://leetcode.cn/problems/remove-linked-list-elements/)

- **设置虚拟头结点**:可以解决头部节点无法统一删除的问题。

```java
public ListNode removeElements(ListNode head, int val) {
    if (head == null) {
        return head;
    }
    // 因为删除可能涉及到头节点，所以设置dummy节点，统一操作
    ListNode dummy = new ListNode(-1, head);
    ListNode pre = dummy;
    ListNode cur = head;
    while (cur != null) {
        if (cur.val == val) {
            pre.next = cur.next;
        } else {
            pre = cur;
        }
        cur = cur.next;
    }
    return dummy.next;
}
```

### 设计链表

[力扣题目链接](https://leetcode.cn/problems/design-linked-list/)

设置虚拟头节点来实现以下五个接口。

1. 获取链表第index个节点的数值

2. 在链表的最前面插入一个节点

3. 在链表的最后面插入一个节点

4. 在链表第index个节点前面插入一个节点

5. 删除链表的第index个节点

- 设计单链表。

```java
//单链表
class ListNode {
    int val;
    ListNode next;
    ListNode(){}
    ListNode(int val) {
        this.val=val;
    }
}
class MyLinkedList {
    //size存储链表元素的个数
    int size;
    //虚拟头结点
    ListNode head;

    //初始化链表
    public MyLinkedList() {
        size = 0;
        head = new ListNode(0);
    }

    //获取第index个节点的数值，注意index是从0开始的，第0个节点就是头结点
    public int get(int index) {
        //如果index非法，返回-1
        if (index < 0 || index >= size) {
            return -1;
        }
        ListNode currentNode = head;
        //包含一个虚拟头节点，所以查找第 index+1 个节点
        for (int i = 0; i <= index; i++) {
            currentNode = currentNode.next;
        }
        return currentNode.val;
    }

    //在链表最前面插入一个节点，等价于在第0个元素前添加
    public void addAtHead(int val) {
        addAtIndex(0, val);
    }

    //在链表的最后插入一个节点，等价于在(末尾+1)个元素前添加
    public void addAtTail(int val) {
        addAtIndex(size, val);
    }

    // 在第 index 个节点之前插入一个新节点，例如index为0，那么新插入的节点为链表的新头节点。
    // 如果 index 等于链表的长度，则说明是新插入的节点为链表的尾结点
    // 如果 index 大于链表的长度，则返回空
    public void addAtIndex(int index, int val) {
        if (index > size) {
            return;
        }
        if (index < 0) {
            index = 0;
        }
        size++;
        //找到要插入节点的前驱
        ListNode pred = head;
        for (int i = 0; i < index; i++) {
            pred = pred.next;
        }
        ListNode toAdd = new ListNode(val);
        toAdd.next = pred.next;
        pred.next = toAdd;
    }

    //删除第index个节点
    public void deleteAtIndex(int index) {
        if (index < 0 || index >= size) {
            return;
        }
        size--;
        if (index == 0) {
            head = head.next;
	    return;
        }
        ListNode pred = head;
        for (int i = 0; i < index ; i++) {
            pred = pred.next;
        }
        pred.next = pred.next.next;
    }
}
```

- 设计双链表

```java
//双链表
class ListNode{
    int val;
    ListNode next,prev;
    ListNode() {};
    ListNode(int val){
        this.val = val;
    }
}


class MyLinkedList {  

    //记录链表中元素的数量
    int size;
    //记录链表的虚拟头结点和尾结点
    ListNode head,tail;
    
    public MyLinkedList() {
        //初始化操作
        this.size = 0;
        this.head = new ListNode(0);
        this.tail = new ListNode(0);
        //这一步非常关键，否则在加入头结点的操作中会出现null.next的错误！！！
        head.next=tail;
        tail.prev=head;
    }
    
    public int get(int index) {
        //判断index是否有效
        if(index<0 || index>=size){
            return -1;
        }
        ListNode cur = this.head;
        //判断是哪一边遍历时间更短
        if(index >= size / 2){
            //tail开始
            cur = tail;
            for(int i=0; i< size-index; i++){
                cur = cur.prev;
            }
        }else{
            for(int i=0; i<= index; i++){
                cur = cur.next; 
            }
        }
        return cur.val;
    }
    
    public void addAtHead(int val) {
        //等价于在第0个元素前添加
        addAtIndex(0,val);
    }
    
    public void addAtTail(int val) {
        //等价于在最后一个元素(null)前添加
        addAtIndex(size,val);
    }
    
    public void addAtIndex(int index, int val) {
        //index大于链表长度
        if(index>size){
            return;
        }
        //index小于0
        if(index<0){
            index = 0;
        }
        size++;
        //找到前驱
        ListNode pre = this.head;
        for(int i=0; i<index; i++){
            pre = pre.next;
        }
        //新建结点
        ListNode newNode = new ListNode(val);
        newNode.next = pre.next;
        pre.next.prev = newNode;
        newNode.prev = pre;
        pre.next = newNode;
        
    }
    
    public void deleteAtIndex(int index) {
        //判断索引是否有效
        if(index<0 || index>=size){
            return;
        }
        //删除操作
        size--;
        ListNode pre = this.head;
        for(int i=0; i<index; i++){
            pre = pre.next;
        }
        pre.next.next.prev = pre;
        pre.next = pre.next.next;
    }
}
```

### 反转链表

- 完全反转[力扣题目链接](https://leetcode.cn/problems/reverse-linked-list/)

- 部分反转[力扣题目链接](https://leetcode.cn/problems/reverse-linked-list-ii/ )

其实只需要改变链表的next指针的指向，直接将链表反转 ，而不用重新定义一个新的链表，如图所示:

![](image\206.翻转链表.gif)

- 双指针

```java
// 双指针
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode cur = head;
        ListNode temp = null;
        while (cur != null) {
            temp = cur.next;// 保存下一个节点
            cur.next = prev;
            prev = cur;
            cur = temp;
        }
        return prev;
    }
}
```

- 递归

```java
// 递归 
class Solution {
    public ListNode reverseList(ListNode head) {
        return reverse(null, head);
    }

    private ListNode reverse(ListNode prev, ListNode cur) {
        if (cur == null) {
            return prev;
        }
        ListNode temp = null;
        temp = cur.next;// 先保存下一个节点
        cur.next = prev;// 反转
        // 更新prev、cur位置
        // prev = cur;
        // cur = temp;
        return reverse(cur, temp);
    }
}
```

- 从后向前递归

```java
// 从后向前递归
class Solution {
    ListNode reverseList(ListNode head) {
        // 边缘条件判断
        if(head == null) return null;
        if (head.next == null) return head;
        
        // 递归调用，翻转第二个节点开始往后的链表
        ListNode last = reverseList(head.next);
        // 翻转头节点与第二个节点的指向
        head.next.next = head;
        // 此时的 head 节点为尾节点，next 需要指向 NULL
        head.next = null;
        return last;
    } 
}
```

