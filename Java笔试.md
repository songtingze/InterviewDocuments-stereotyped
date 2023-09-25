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

### 两两交换链表中的节点

[力扣题目链接](https://leetcode.cn/problems/swap-nodes-in-pairs/)

***一定要画图！***

***这个题最好设置一个虚拟头节点！***

- 递归法

```java
// 递归版本
class Solution {
    public ListNode swapPairs(ListNode head) {
        // base case 退出提交
        if(head == null || head.next == null) return head;
        // 获取当前节点的下一个节点
        ListNode next = head.next;
        // 进行递归
        ListNode newNode = swapPairs(next.next);
        // 这里进行交换
        next.next = head;
        head.next = newNode;

        return next;
    }
} 
```

- 常规法

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode l1 = new ListNode(0,head);
        ListNode l2 = l1;
        while (l2.next != null && l2.next.next != null) {
            ListNode first = l2.next;
            ListNode second = l2.next.next;
            ListNode third = l2.next.next.next;
            l2.next = second;
            first.next = third;
            second.next = first;
            l2 = l2.next.next;
        }
        return l1.next;q
        // ListNode l = new ListNode(0, head);
        // ListNode cur = l;
        // while (cur != null && cur.next != null && cur.next.next != null) {
        //     ListNode first = cur;
        //     ListNode second = cur.next;
        //     ListNode third = cur.next.next;
        //     cur = cur.next.next.next;
        //     first.next = third;
        //     third.next = second;
        //     second.next = cur;
        //     cur = second;
        // }
        // return l.next;
    }
}
```

### 删除链表的倒数第N个节点

[力扣题目链接](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

- 快慢指针法：快指针先走n+1步，然后快慢指针一起走，当快指针到链表结尾时，慢指针位于链表倒数第N个节点的上一个节点，然后删除对应节点。

```java
public ListNode removeNthFromEnd(ListNode head, int n){
    ListNode dummyNode = new ListNode(0);
    dummyNode.next = head;

    ListNode fastIndex = dummyNode;
    ListNode slowIndex = dummyNode;

    //只要快慢指针相差 n 个结点即可
    for (int i = 0; i < n  ; i++){
        fastIndex = fastIndex.next;
    }

    while (fastIndex.next != null){
        fastIndex = fastIndex.next;
        slowIndex = slowIndex.next;
    }

    //此时 slowIndex 的位置就是待删除元素的前一个位置。
    //具体情况可自己画一个链表长度为 3 的图来模拟代码来理解
    slowIndex.next = slowIndex.next.next;
    return dummyNode.next;
}
```

### 链表相交 ——面试题

[力扣题目链接](https://leetcode.cn/problems/intersection-of-two-linked-lists-lcci/)

- 哈希表检索：将链表A存入哈希表，然后遍历链表B，找到相同的节点返回。

  时间复杂度：O(n+m)  空间复杂度：O(n)

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        Set<ListNode> visited = new HashSet<ListNode>();
        ListNode temp = headA;
        while (temp != null) {
            visited.add(temp);
            temp = temp.next;
        }
        temp = headB;
        while (temp != null) {
            if (visited.contains(temp)) {
                return temp;
            }
            temp = temp.next;
        }
        return null;
    }
}
```

- 快慢指针法：获得链表A和链表B的长度差，让长的链表先走相差的步数，从而使得两个链表同步进行。

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode curA = headA;
        ListNode curB = headB;
        int lenA = 0, lenB = 0;
        while (curA != null) { // 求链表A的长度
            lenA++;
            curA = curA.next;
        }
        while (curB != null) { // 求链表B的长度
            lenB++;
            curB = curB.next;
        }
        curA = headA;
        curB = headB;
        // 让curA为最长链表的头，lenA为其长度
        if (lenB > lenA) {
            //1. swap (lenA, lenB);
            int tmpLen = lenA;
            lenA = lenB;
            lenB = tmpLen;
            //2. swap (curA, curB);
            ListNode tmpNode = curA;
            curA = curB;
            curB = tmpNode;
        }
        // 求长度差
        int gap = lenA - lenB;
        // 让curA和curB在同一起点上（末尾位置对齐）
        while (gap-- > 0) {
            curA = curA.next;
        }
        // 遍历curA 和 curB，遇到相同则直接返回
        while (curA != null) {
            if (curA == curB) {
                return curA;
            }
            curA = curA.next;
            curB = curB.next;
        }
        return null;
    }

}
```

- 数学：同步遍历链表A与链表B，如果遍历到链表尾则续上另一个链表，则当节点相同时就是交叉点。

  

  链表 headA和 headB 的长度分别是 m和 n。假设链表 headA 的不相交部分有 a 个节点，链表 headB的不相交部分有 b 个节点，两个链表相交的部分有 c 个节点，则有 a+c=m，b+c=n。

  

  指针 pA 会遍历完链表 headA，指针 pB 会遍历完链表 headB，两个指针不会同时到达链表的尾节点，然后指针 pA移到链表 headB 的头节点，指针 pB 移到链表 headA 的头节点，然后两个指针继续移动，在指针 pA 移动了 a+c+b 次、指针 pB 移动了 b+c+a 次之后，两个指针会同时到达两个链表相交的节点，该节点也是两个指针第一次同时指向的节点，此时返回相交的节点。


```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {
            return null;
        }
        ListNode pA = headA, pB = headB;
        while (pA != pB) {
            pA = pA == null ? headB : pA.next;
            pB = pB == null ? headA : pB.next;
        }
        return pA;
    }
}
```

### 环形链表II

[力扣题目链接](https://leetcode.cn/problems/linked-list-cycle-ii/)

- 哈希表检索：遍历链表，如果哈希表没有则存入，否则返回该节点。

  时间复杂度：O(n)  空间复杂度：O(n)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        HashSet<ListNode> set = new HashSet<>();
        while (head != null) {
            if (!set.contains(head)) {
                set.add(head);
            }else {
                return head;
            }
            head = head.next;
        }
        return null;
    }
}
```

- 快慢指针法：

  **首先判断是否有环。**

  可以使用快慢指针法，分别定义 fast 和 slow 指针，从头结点出发，fast指针每次移动两个节点，slow指针每次移动一个节点，如果 fast 和 slow指针在途中相遇 ，说明这个链表有环。

  **然后如果有环找到环的入口。**

  从头结点出发一个指针，从相遇节点 也出发一个指针，这两个指针每次只走一个节点， 那么当这两个指针相遇的时候就是 环形入口的节点。

  ![](image\142.环形链表II（求入口）.gif)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {// 有环
                ListNode index1 = fast;
                ListNode index2 = head;
                // 两个指针，从头结点和相遇结点，各走一步，直到相遇，相遇点即为环入口
                while (index1 != index2) {
                    index1 = index1.next;
                    index2 = index2.next;
                }
                return index1;
            }
        }
        return null;
    }
}
```

## 二叉树

### **递归**

1. **确定递归函数的参数和返回值：** 确定哪些参数是递归的过程中需要处理的，那么就在递归函数里加上这个参数， 并且还要明确每次递归的返回值是什么进而确定递归函数的返回类型。
2. **确定终止条件：** 写完了递归算法, 运行的时候，经常会遇到栈溢出的错误，就是没写终止条件或者终止条件写的不对，操作系统也是用一个栈的结构来保存每一层递归的信息，如果递归没有终止，操作系统的内存栈必然就会溢出。
3. **确定单层递归的逻辑：** 确定每一层递归需要处理的信息。在这里也就会重复调用自己来实现递归的过程。

### 先序遍历、后序遍历、中序遍历

- 先序遍历

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<Integer>();
        getPreorder(root, list);
        return list;
    }
    public void getPreorder(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        list.add(root.val);
        getPreorder(root.left, list);
        getPreorder(root.right, list);
    }
}
```

- 后序遍历

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        getPostorder(root, list);
        return list;
    }
    public void getPostorder(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        getPostorder(root.left, list);
        getPostorder(root.right, list);
        list.add(root.val);
    }
}
```

- 中序遍历

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        getInorder(root, list);
        return list;
    }
    public void getInorder(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        getInorder(root.left, list);
        list.add(root.val);
        getInorder(root.right, list);
    }
}
```

