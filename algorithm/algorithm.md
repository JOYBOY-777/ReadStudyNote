# 代码随想录

## 数组

### lc 27移动元素

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int j = 0;
        for(int i = 0;i<nums.length;i++){
            if(nums[i]!=val){
                nums[j] = nums[i];
                j++;
            }
        }
        return j;
    }
}
```

思路：双指针移动，不相等就互换位置覆盖之前的元素，相等的话就等着被替换，同时更新下标



### lc 209 长度最小的子数组（双指针，滑动窗口）

```java
class Solution {
    public int minSubArrayLen(int t, int[] n) {
        int l = 0;
        int sum = 0;
        Integer res = Integer.MAX_VALUE;
        for(int r = 0;r<n.length;r++){
            sum += n[r];
            while(sum>=t){
                res = Math.min(res,r-l+1);
                //看一下他到底够不够，够的话就一直减
                sum  -= n[l++];
            }
        }
        return res == Integer.MAX_VALUE?0:res;
    }
}
```

 

### lc 977有序数组的平方

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        for(int i = 0;i<nums.length;i++){
            nums[i] = nums[i]*nums[i];
        }
        Arrays.sort(nums);
        return nums;
    }
}
```



### lc 59 螺旋矩阵||

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] res = new int[n][n];
        int loop = n/2,mid = n/2;
        int startX = 0,startY = 0;
        int offset = 1,count = 1;
        while(loop>0){
            int i = startX,j = startY;
            for(;j<startY+n-offset;j++){
                res[startX][j] = count++;
            }
            for(;i<startX+n-offset;i++){
                res[i][j] = count++;
            }
            for(;j>startY;j--){
                res[i][j] = count++;
            }
            for(;i>startX;i--){
                res[i][j] = count++;
            }
            loop--;
            startX+=1;
            startY+=1;
            offset+=2;
        }
        if(n%2==1) res[mid][mid] = count;
        return res;
    }
}
```

背就好了





## 链表

### lc 203 移除链表元素

**直接弄一个虚拟的头结点出来就好了**

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if(head == null){
            return head;
        }
       ListNode fake  = new ListNode(-1,head);
       ListNode pre = fake;
       ListNode cur = head;
       while(cur!=null){
           if(cur.val==val){
               pre.next = cur.next;
           }else{
               pre = cur;
           }
           cur = cur.next;
       } 
       return fake.next;
    }
}
```



### lc 707 设计链表

```java
class ListNode{
    int val;
    ListNode next;
    ListNode(){};
    ListNode(int val){
        this.val = val;
    }
}

class MyLinkedList {
    int size;
    ListNode head;
    public MyLinkedList() {
        size = 0;
        head = new ListNode(0);
    }
    
    public int get(int index) {
        if(index<0 || index>=size) return -1;
        ListNode cur = head;
        for(int i = 0;i<=index;i++) cur = cur.next;
        return cur.val;
    }
    
    public void addAtHead(int val) {
        addAtIndex(0,val);
    }
    
    public void addAtTail(int val) {
        addAtIndex(size,val);
    }
    
    public void addAtIndex(int index, int val) {
        if(index>size) return;
        if(index<0) index = 0;
        size++;
        ListNode pre = head;
        for(int i = 0;i<index;i++) pre = pre.next;
        ListNode add = new ListNode(val);
        add.next = pre.next;
        pre.next = add;

    }
    
    public void deleteAtIndex(int index) {
        if(index<0 || index>=size) return;
        size--;
        ListNode pre = head;
        for(int i = 0;i<index;i++) pre = pre.next;
        pre.next = pre.next.next;
    }
}

```

这个可以对比移除链表的元素那个题，都是利用虚拟节点来删除元素



### lc 206 反转链表

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode temp = null;
        ListNode cur = head;
        ListNode pre = null;
        while(cur!=null){
            temp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = temp;
        }
        return pre;
    }
}
```



### lc 24 两两交换链表中的节点

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode fake = new ListNode(0);
        fake.next = head;
        ListNode pre = fake;
        while(pre.next!=null && pre.next.next!=null){
            ListNode temp = head.next.next;
            pre.next = head.next;
            head.next.next = head;
            head.next = temp;
            pre = head;
            head = head.next;
        }
        return fake.next;
    }
}
```



### lc 19.删除链表的倒数第N个节点

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
    //双指针
    ListNode dummy=new ListNode(0,head);
    ListNode first=dummy;
    ListNode second=head;
    for(int i=0;i<n;i++){
        second=second.next;
    }
    while(second!=null){
        first=first.next;
        second=second.next;
    }
    first.next=first.next.next;
    ListNode ans=dummy.next;
    return ans;
    }
}
```



### 面试题 02.07. 链表相交

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode a = headA;
        ListNode b = headB;
        while(a!=b){
            a = a!=null?a = a.next:headB;
            b = b!=null?b = b.next:headA;
        }
        return a;
    }
}
```

相当于指针要遍历两个链表，如果相遇就返回a，就是起始公共的位置



### lc 141 环形链表

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head==null || head.next==null){
            return false;
        }
        ListNode slow = head;
        ListNode fast = head.next;
        while(slow!=fast){
            if(fast==null || fast.next==null){
            return false;
        }
            //我慢走一步
            slow = slow.next;
            //我快走两步
            fast = fast.next.next;
        }
        return true;
    }
}
```



### lc 142 环形链表2

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode cur = head;
        Set<ListNode> set = new HashSet();
        while(cur!=null){
            if(set.contains(cur)) return cur;
            else set.add(cur);
            cur = cur.next;
        }
        return null;
    }
}
```



## hash表

### lc 242 有效的字母异位词

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        int[] record = new int[26];
        for(char c: s.toCharArray()) {
            record[c-'a']+=1;
        }
         for(char c: t.toCharArray()) {
            record[c-'a']-=1;
        }
         for( int c: record){
             if(c!=0){
                 return false;
             }
         }
         return true;
    }
}
```

这道题很巧妙的运用了hash表的概念，先让数组上的值+1，然后在-1,最后不是0的就不是字母异位词



### lc 349 两个数组的交集

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        if(nums1==null||nums1.length==0||nums1==null||nums2.length==0){
            return new int[0];
        }

        Set<Integer> set = new HashSet();
        Set<Integer> res = new HashSet();

        for(int n:nums1){
            set.add(n);
        }

         for(int n:nums2){
             if(set.contains(n)){
                 res.add(n);
             }
        }

        int[] resArr = new int[res.size()]; 
        int index = 0;
        for(int n: res){
            resArr[index++] = n;
        }
        return resArr;
    }   
}
```

就是用set集合去重之后，如果遍历第二个数组，如果第二个数组里面的数在第一个数组里面，那么就加入进去，最后在把结果弄成数组用 resArr[index++] = n;



### lc 202 快乐数

```java
class Solution {
    public boolean isHappy(int n) {
        Set<Integer> set = new HashSet();
        while(n!=1 && !set.contains(n)){
            set.add(n);
            n = getNextNumber(n);
        }
        return n==1;
    }

    public int getNextNumber(int n){
        int res = 0;
        while(n>0){
            int temp = n%10;
            res += temp*temp;
            n = n/10;
        }
        return res;
    }
}
```

这道题就是 一直获取下一个数怎么计算这个数比较的重要，如果放在hash表中有重复或者等于1都直接跳出，如果最后的数等于1就是快乐数



### lc 383 赎金信

```java
class Solution {
    public boolean canConstruct(String r, String m) {
        int[] arr = new int[26];
        for(char c:m.toCharArray()){
            arr[c-'a'] += 1;
        }

        for(char c:r.toCharArray()){
            if(arr[c-'a']>0){
                arr[c-'a'] -=1;
            }else{
                return false;
            }
        }
        return true;
    }
}
```

这个题就是不用第一个数字强制键1，在这个数组遍历的时候用上循环控制就好



### lc 560 合为k的子数组

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        Map<Integer,Integer> map = new HashMap();
        map.put(0,1);
        int count = 0;
        int sum = 0;
        for(int n: nums){
            sum+=n;
            if(map.containsKey(sum-k)){
                count += map.get(sum-k);
            }

            map.put(sum,map.getOrDefault(sum,0)+1);
        }
        return count;
    }
}
```

一定要理解整道题的思路，不要傻背，要不下次铁忘！！





## 字符串

### lc 344 反转字符串

```java
class Solution {
    public void reverseString(char[] s) {
        int l = 0;
        int r = s.length-1;
        while(l<r){
            char temp = s[l];
            s[l] = s[r];
            s[r] = temp; 
            l++;
            r--;
        }
    }
}
```

这个就是用双指针，实际上就是快速排序的一部分，只不过lr的取值范围不一样



### lc 541 反转字符串||

```java
class Solution {
    public String reverseStr(String s, int k) {
        char[] c = s.toCharArray();
        for(int i = 0;i<c.length;i+=k*2){
            int l = i;
            int r = Math.min(c.length-1,l+k-1);
            while(l<r){
                char temp = c[l];
                c[l] = c[r];
                c[r] = temp;
                l++;
                r--;
            } 
        }
        return new String(c);
    }
}
```

这个题就是双指针在循环的里面，然后r指针的位置时在循环里面决定的，循环的条件也是每隔两个循环,然后再交换位置即可



### 剑指offer 05 替换空格

```java
class Solution {
    public String replaceSpace(String s) {
        if(s==null){
            return null;
        }
        StringBuffer sb = new StringBuffer();
        for(char c: s.toCharArray()){
            if(c == ' '){
                sb.append("%20");
            }else{
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```

此题就是替换操作，用String的增强类StringBuffer，进行字符串的拼接



### lc 151 翻转字符串里面的单词

```java
class Solution {
    public String reverseWords(String s) {
        StringBuffer res = new StringBuffer();
        int l = s.length()-1;
        int r = s.length()-1;
        while(l>=0){
            while(l>=0 && s.charAt(l) == ' '){
                l--;
            }
            r = l;
            if(r<0) break;
            while(l>=0 && s.charAt(l) != ' '){
                l--;
            }
            res.append(s.substring(l+1,r+1));
            res.append(" ");
        }
        return res.substring(0,res.length()-1).toString();
    }
}
```

从后往前遍历，然后截取单词，放进buffer里面



### 剑指offer 58左旋转字符串

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        StringBuffer head = new StringBuffer(s.substring(n,s.length()));
        StringBuffer tail = new StringBuffer(s.substring(0,n));
        head.append(tail);
        return head.toString();
    }
}
```



### lc 28 实现strStr()

```java
class Solution {
    public int strStr(String ss, String pp) {
        if(pp.isEmpty()) return 0;
        int n = ss.length(),m = pp.length();
        ss = " "+ss;
        pp = " "+pp;
        char[] s = ss.toCharArray();
        char[] p = pp.toCharArray();
        int[] next = new int[m+1];
        for(int i = 2,j = 0;i<=m;i++){
            while(j>0 && p[i]!=p[j+1]) j = next[j];
            if(p[i]==p[j+1]) j++;
            next[i] = j;
        }
        for(int i = 1,j = 0;i<=n;i++){
            while(j>0 && s[i]!=p[j+1]) j = next[j];
            if(s[i]==p[j+1])j++;
            if(j==m) return i-m;
        }
        return -1;
    }
}
```

这个题纯纯是背的，简单理解一下

 

### lc 459重复的子字符串

```java
class Solution {
    public boolean repeatedSubstringPattern(String s) {
        if(s.isEmpty()) return false;
        int m = s.length();
        s = " "+ s;
        char[] p = s.toCharArray();
        int[] next = new int[m+1];
        for(int i = 2,j = 0;i<=m;i++){
            while(j>0 && p[i]!=p[j+1]) j = next[j];
            if(p[i]==p[j+1]) j++;
            next[i] = j;
        }
        if(next[m]>0 && m%(m-next[m]) == 0) return true;
        return false;
    }
}
```

关键点就是if(next[m]>0 && m%(m-next[m]) == 0) return true; 数组长度%（数组长度-最长相等子序列的长度）可以被整除





## 栈与队列

### lc 232 用栈实现队列

```java
class MyQueue {
    Deque<Integer> in;
    Deque<Integer> out;

    public MyQueue() {
        in = new ArrayDeque();
        out = new ArrayDeque();
    }
    
    public void push(int x) {
        in.push(x);
    }
    
    public int pop() {
        MoveEelement();
        return out.pop();

    }
    
    public int peek() {
        MoveEelement();
        return out.peek();
    }
    
    public boolean empty() {
        return in.isEmpty() && out.isEmpty();
    }

    private void MoveEelement(){
        if(!out.isEmpty()) return;
        while(!in.isEmpty()){
            out.push(in.pop());
        }
    }
}
```

就是有两个栈来维护，在pop(),peek()操作的时候，优先检查一下，输出栈是否为null了，如果是空的就把in栈里面的元素导入到out栈中去



### lc 225 用队列实现栈

```java
class MyStack {
    Deque<Integer> que;
    public MyStack() {  
        que = new ArrayDeque();
    }
    
    public void push(int x) {   
        que.addLast(x);
    }
    
    public int pop() {
        int size = que.size()-1;
        while(size-- >0) {
            que.addLast(que.peekFirst());
            que.pollFirst();
        }
        return que.pollFirst();
    }
    
    public int top() {
        return que.peekLast();
    }
    
    public boolean empty() {
        return que.isEmpty();
    }
}
```

即使在pop的时候把队列中的头部（注意要减一个）在放到队列的尾部，这样的顺序就保持一致了



### lc 20 有效的括号

```java
class Solution {
    public boolean isValid(String s) {
        Deque<Character> steak = new ArrayDeque();
        char[] cs = s.toCharArray();
        for(char c: cs ){
            if(c=='(') steak.push(')');
            if(c=='{') steak.push('}');
            if(c=='[') steak.push(']');

            if(c==')'||c==']' ||c=='}'){
                if(steak.isEmpty()||c!=steak.pop()){
                    return false;
                } 
            }
        }
        return steak.isEmpty();
    }
}
```

消消乐



### lc 1047 删除字符串中的所有相邻重复项

```java
class Solution {
    public String removeDuplicates(String s) {
        Deque<Character> stack = new ArrayDeque();
        char[] arr = s.toCharArray();
        for(char c:arr){
            if(stack.isEmpty()||c!=stack.peek()){
                stack.push(c);
            }else{
                stack.pop();
            }
        }
        String str = "";
        while(!stack.isEmpty()) str = stack.pop() + str;
        return str;
    }
}
```

用peek先不删，一样在删，最后转变为字符串



### lc 150 逆波兰表达式求值

```java
class Solution {
    public int evalRPN(String[] t) {
        Deque<Integer> stack = new ArrayDeque();
        for(String s: t){
            if(s.equals("+")||s.equals("-")||s.equals("*")||s.equals("/")){
                int a = stack.pop();
                int b = stack.pop();
                if("+".equals(s)) stack.push(a+b);
                else if("-".equals(s)) stack.push(b-a);
                else if("*".equals(s)) stack.push(a*b);
                else if("/".equals(s)) stack.push(b/a);
            }else stack.push(Integer.valueOf(s));
        }
        return stack.pop();
    }   
}
```

利用栈，没有遇到运算符之前把常规元素加入栈，遇到了之后就进行运算操作，最后访问栈顶就行了



### lc 239 滑动窗口最大值

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        ArrayDeque<Integer> deque = new ArrayDeque();
        int n = nums.length;
        //数组的大小是活动窗口能滑动的次数,可以存放滑动窗口滑动n次的值
        int[] res = new int[n-k+1];
        int index = 0;
        for(int i = 0;i<n;i++){
            //防止下标超出，保证队列头结点在i-k+1的范围内
            while(!deque.isEmpty() && deque.peek()<i-k+1) deque.poll();
            //保证是单调队列
            while(!deque.isEmpty() && nums[deque.peekLast()]<nums[i]) deque.pollLast();
            deque.offer(i);
            //如果下标合理的话每滑动一步的话就要把队列里面下标对应的值放到res数组里面
            if(i-k+1>=0) res[index++] = nums[deque.peek()];
        }
        return res;
    }
}
```



## 二叉树

### lc 树的前序中序后续遍历（递归）

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList();
        order(root,res);
        return res;
    }

    public void order(TreeNode root,List res){
        if(root == null) return;
        order(root.left,res);
        res.add(root.val);
        order(root.right,res);
    }
}
```

递归模板



### lc 树的前序中序后续遍历（非递归）

```java
//前序遍历
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList();
        if(root==null) return res;
        Deque<TreeNode> steak = new ArrayDeque();
        //根节点入栈
        steak.push(root);
        while(!steak.isEmpty()){
            //处理根节点所以要出栈
            TreeNode node = steak.pop();
            //加入结果集
            res.add(node.val);
            //处理右面
            if(node.right!=null) steak.push(node.right);
            //处理左面
            if(node.left!=null) steak.push(node.left);
        }
        return res;
    }
}
```

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/algorithm/%E7%AE%97%E6%B3%95gif/%E4%BA%8C%E5%8F%89%E6%A0%91%E5%89%8D%E5%BA%8F%E9%81%8D%E5%8E%86.gif?raw=true)

```java
//中序遍历
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList();
        if(res==null) return res;
        Deque<TreeNode> steak = new ArrayDeque();
        while(root!=null || !steak.isEmpty()){
            //一直遍历直到没有左节点的情况，并加入到栈中
            if(root!=null){
                steak.push(root);
                root = root.left;
            }else{
                //否则就出栈，加入到结果集，在看这个节点有没有右边的节点，并在上面的代码中加入到栈中
                TreeNode node = steak.pop();
                res.add(node.val);
                root = node.right;
            }
        }
        return res;
    }
}

//模板二：推荐
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList();
        Deque<TreeNode> stack = new ArrayDeque();
        while(root!=null || !stack.isEmpty()){
            //找到最左边的
            while(root!=null){
             stack.push(root);
             root = root.left;
            }
            //出栈
             root = stack.pop();
             res.add(root.val);
            //更新root遍历他的右边
             root = root.right;
        }
         return res;
    }
}
```

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/algorithm/%E7%AE%97%E6%B3%95gif/%E4%BA%8C%E5%8F%89%E6%A0%91%E4%B8%AD%E5%BA%8F%E9%81%8D%E5%8E%86.gif?raw=true)

```java
//后序遍历
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList();
        if(root == null) return res;
        Deque<TreeNode> steak = new ArrayDeque();
        steak.add(root);
        while(!steak.isEmpty()){
            TreeNode node = steak.pop();
            //从后往前加
            res.add(0,node.val);
            //正常顺序不用相反
            if(node.left!=null) steak.push(node.left);
            if(node.right!=null) steak.push(node.right);
        }
        return res;
    }
}
```



### lc 102 二叉树的层序遍历

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList();
        Queue<TreeNode> q = new LinkedList();
        if(root != null) q.offer(root);
        while(!q.isEmpty()){
            int size = q.size();
            List <Integer> level = new ArrayList();
            for(int i = 0;i<size;i++){
                TreeNode cur = q.poll();
                level.add(cur.val);
                if(cur.left!=null) q.offer(cur.left);
                if(cur.right!=null) q.offer(cur.right);
            }
            res.add(level);
        }
        return res;
    }
}
```

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/algorithm/%E7%AE%97%E6%B3%95gif/%E4%BA%8C%E5%8F%89%E6%A0%91%E5%B1%82%E5%BA%8F%E9%81%8D%E5%8E%86.gif?raw=true)



### 107 二叉树的层序遍历||

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList();
        Queue<TreeNode> q = new LinkedList();
        if(root != null) q.offer(root);
        while(!q.isEmpty()){
            int size = q.size();
            List <Integer> level = new ArrayList();
            for(int i = 0;i<size;i++){
                TreeNode cur = q.poll();
                level.add(cur.val);
                if(cur.left!=null) q.offer(cur.left);
                if(cur.right!=null) q.offer(cur.right);
            }
            res.add(0,level);
        }
        return res;
    }
}
```

注意在添加整体的时候add的时候有0，就是倒序添加



### 199 二叉树的左视图

```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new ArrayList();
        if(root==null) return res;
        Queue<TreeNode> que = new LinkedList();
        que.offer(root);
        while(!que.isEmpty()){
            int size = que.size();
            for(int i = 0;i<size;i++){
                TreeNode node = que.poll();
                if(i==size-1) res.add(node.val);
                if(node.left!=null) que.offer(node.left);
                if(node.right!=null) que.offer(node.right);
            }
        }
        return res;
    }
}
```

控制下标然后根据不同的下标元素进行相应的插入



### 637 二叉树的层平均值

```java
class Solution {
    public List<Double> averageOfLevels(TreeNode root) {
        List<Double> res = new ArrayList();
        Queue<TreeNode> que = new LinkedList();
        if(root==null) return res;
        que.offer(root);
        while(!que.isEmpty()){
            int size = que.size();
            double sum = 0.0;
            for(int i = 0;i<size;i++){
                TreeNode node = que.poll();
                sum+=node.val;
                if(node.left!=null) que.offer(node.left);
                if(node.right!=null) que.offer(node.right);
            }
            res.add(sum/size);
        }
        return res;
    }
}
```

注意：取循环里面元素的总和的时候可以在外面定义一个初始值sum = 0，然后再循环里面累加就行



### 429 N叉树的层序遍历

```java
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        List<List<Integer>> resList = new ArrayList();
        Queue<Node> que = new LinkedList();
        if(root != null) que.offer(root);
        while(!que.isEmpty()){
            int size = que.size();
            List <Integer> res = new ArrayList();
            for(int i = 0;i<size;i++){
                Node node = que.poll();
                res.add(node.val);
                List<Node> ch = node.children;
                if(ch==null||ch.size()==0) continue;
                for(Node n:ch) que.offer(n);
            }
            resList.add(res);
        }
        return resList;
    }
}
```

遍历孩子节点在依次加入队列即可



 ### 515 在每个树行中找最大值

```java
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> res = new ArrayList();
        Deque<TreeNode> que = new ArrayDeque();
        if(root == null) return res;
        que.offer(root);
        while(!que.isEmpty()){
            int size = que.size();
            int max = Integer.MIN_VALUE;
            for(int i = 0;i<size;i++){
                TreeNode node = que.poll();
                max = Math.max(max,node.val);
                if(node.left!=null) que.offer(node.left);
                if(node.right!=null) que.offer(node.right);
            }
            res.add(max);
        }
        return res;
    }
}
```

还是一样的模板题，就是维护一个最大值的下线，如果有比这个最大值还要大的数就直接替换，成为新的最大值



### 116 填充每个节点的下一个右侧节点指针

```java
class Solution {
    public Node connect(Node root) {
        LinkedList<Node> que = new LinkedList();
        if(root == null) return root;
        que.add(root);
        while(!que.isEmpty()){
            int size = que.size();
            Node node = que.get(0);
            for(int i = 1;i<size;i++){
                node.next = que.get(i);
                node = que.get(i);
            }
            for(int i = 0;i<size;i++){
                 node = que.remove();
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return root;
    }
}
```

用了LinkedList来充当队列，然后利用api的get来拿到队列中的值，用Queue并不行



### 117 填充每个节点的下一个右侧节点指针||

```java
class Solution {
    public Node connect(Node root) {
        LinkedList<Node> que = new LinkedList();
        if(root == null) return root;
        que.add(root);
        while(!que.isEmpty()){
            int size = que.size();
            Node node = que.get(0);
            for(int i = 1;i<size;i++){
                node.next = que.get(i);
                node = que.get(i);
            }
            for(int i = 0;i<size;i++){
                 node = que.remove();
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return root;
    }
}
```

一样的代码...



### 104 二叉树的最大深度

```java
class Solution {
    public int maxDepth(TreeNode root) {
        LinkedList<TreeNode> que = new LinkedList();
        if(root==null) return 0;
        que.add(root);
        int deep = 0;
        while(!que.isEmpty()){
            deep++;
            int size= que.size();
            for(int i = 0;i<size;i++){
               TreeNode node = que.remove();
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return deep;
    }
}
```

实际上最大深度就是层数



### 111 二叉树的最小深度

```java
class Solution {
    public int minDepth(TreeNode root) {
        LinkedList<TreeNode> que = new LinkedList();
        if(root==null) return 0;
        que.add(root);
        int deep = 0;
        while(!que.isEmpty()){
            deep++;
            int size = que.size();
            for(int i = 0;i<size;i++){
                TreeNode node = que.remove();
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
                if(node.left==null&&node.right==null) return deep;
            }
        }
        return deep;
    }
}
```

当只有左孩子右孩子没有的时候就说明到达了最小深度



### lc 226 反转二叉树

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        LinkedList<TreeNode> que = new LinkedList();
        if(root==null) return root;
        que.add(root);
        while(!que.isEmpty()){
            TreeNode node = que.remove();
            TreeNode mid = node.right;
            node.right = node.left;
            node.left = mid;
            if(node.left!=null) que.add(node.left);
            if(node.right!=null) que.add(node.right);
        }
        return root;
    }
}
```

利用二叉树的层序遍历遍历每一个节点，然后在互换位置



### lc 101对称二叉树

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        LinkedList<TreeNode> que = new LinkedList();
        if(root==null) return false;
        que.add(root.left);
        que.add(root.right);
        while(!que.isEmpty()){
            TreeNode left = que.remove();
            TreeNode right = que.remove();
            if(left==null && right==null) continue;
            if(left==null || right==null || left.val!=right.val) return false;
            que.add(left.left);
            que.add(right.right);
            que.add(left.right);
            que.add(right.left);
        }
        return true;
    }
}
```

利用队列，一次比较两个节点，空也算是一种对称的情况，节点的加入顺序是外层向内层加入，然后在比较，有空节点或者节点值不一样的情况就返回false



### 222 完全二叉树的节点个数

```java
class Solution {
    public int countNodes(TreeNode root) {
        LinkedList<TreeNode> que = new LinkedList();
        if(root==null) return 0;
        que.add(root);
        int count = 0;
        while(!que.isEmpty()){
            int size = que.size();
            for(int i = 0;i<size;i++){
                count++;
                TreeNode node = que.remove();
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return count;
    }
}
```

这个题就是把负责计数的count放在遍历队列元素节点的for循环里面就行了



### 110 平衡二叉树

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return getHeight(root) != -1;
    }

    public int getHeight(TreeNode root){
        if(root==null) return 0;
        int lh = getHeight(root.left);
        if(lh==-1) return -1;
        int rh = getHeight(root.right);
        if(rh==-1) return -1;
        return Math.abs(lh-rh)>1?-1:Math.max(lh,rh)+1;
    }
}
```

理解 return Math.abs(lh-rh)>1?-1:Math.max(lh,rh)+1：就是当前节点为根节点的二叉树所在的高度为左右节点高度的最大值+1，这个1是根节点的高度默认为1，可以理解为当前节点为根节点所在二叉树的高度



### 257 二叉树的所有路径

```java
 class Solution {
     List<String> res = new ArrayList();
     LinkedList<String> path = new LinkedList();
    public List<String> binaryTreePaths(TreeNode root) {
       dfs(root);
       return res;
    }

    public void dfs(TreeNode root){
        path.add(String.valueOf(root.val));
        if(root.left==null&&root.right==null){
            res.add(String.join("->",path));
            return;
        }
        if(root.left!=null){
            dfs(root.left);
            path.removeLast();
        }
        if(root.right!=null){
            dfs(root.right);
            path.removeLast();
        }
    }
}
```

利用队列保存路径，并在递归的但层逻辑上回溯，还得掌握好**递归的使用**



### 404 左叶子之和

递归：

```java
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if(root==null) return 0;
        int leftValue = sumOfLeftLeaves(root.left);
        int rightValue = sumOfLeftLeaves(root.right);
        int value = 0;
        if(root.left!=null && root.left.left==null && root.left.right==null) 
            value += root.left.val;
        int sum = leftValue+rightValue+value;
        return sum;    
    }
}
```

得查看一下root有没有求和的条件



迭代：

```java
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        Deque<TreeNode> steak = new ArrayDeque();
        if(root==null) return 0;
        steak.push(root);
        int res = 0;
        while(!steak.isEmpty()){
            TreeNode node = steak.pop();
            if(node.left!=null && node.left.left==null && node.left.right==null)
            res+=node.left.val;
            if(node.left!=null) steak.push(node.left);
            if(node.right!=null) steak.push(node.right);
        }
        return res;
    }
}
```



### 513 找树左下角的值

我的傻办法：

```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        List<Integer> res = new ArrayList();
        LinkedList<TreeNode> que = new LinkedList();
        if(root==null) return 0;
        que.add(root);
        while(!que.isEmpty()){
            int size = que.size();
            for(int i = 0;i<size;i++){
                TreeNode node = que.remove();
                if(i==0) res.add(node.val);
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return res.get(res.size()-1);
    }
}
```



其实不用开一个list集合的：

```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        LinkedList<TreeNode> que = new LinkedList();
        if(root==null) return 0;
        que.add(root);
        int res = 0;
        while(!que.isEmpty()){
            int size = que.size();
            for(int i = 0;i<size;i++){
                TreeNode node = que.remove();
                if(i==0) res = node.val;
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return res;
    }
}
```

每一次赋值，刷新res的值就可以了



### 112 路径总和

搜索某一路径，需要返回值

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if(root==null) return false;
        return check(root,targetSum-root.val);
    }

    public boolean check(TreeNode root,int sum){
        //遇到左右节点为空并且合为空，表示当前节点已经是末尾
        if(root.left==null && root.right==null && sum==0) return true;
        //左节点不为空继续递归，知道找到符合的节点
        if(root.left!=null){
            sum-=root.left.val;
            if(check(root.left,sum)) return true;
            sum+=root.left.val;
        }
        //右点不为空继续递归，知道找到符合的节点
        if(root.right!=null){
            //递归之前的状态
            sum-=root.right.val;
            if(check(root.right,sum)) return true;
            //恢复递归之前的状态，回溯
            sum+=root.right.val;
        }
        return false;
    }
}
```

回溯就是递归前修改的状态在递归后修改回来，来保持上一个状态



### 113 路径总和 ||

搜索整棵树，不需要返回值

```java
class Solution {
    List<List<Integer>> res = new ArrayList();
    LinkedList<Integer> path = new LinkedList();
    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
        dfs(root,targetSum);
        return res;
    }

    public void dfs(TreeNode root,int targetSum){
        if(root == null) return;
        //递归之前的状态
        path.add(root.val);
        targetSum -= root.val;
        if(root.left == null && root.right == null && targetSum == 0) 
        res.add(new LinkedList(path));
        dfs(root.left,targetSum);
        dfs(root.right,targetSum);
        //递归之后的状态还原
        path.removeLast();
    }
}
```

这也是回溯，感觉树还有dfs回溯密不可分













# 剑指offer

### 剑指03 数组中重复的数字

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        Set<Integer> set = new HashSet();
        for(int i = 0;i<nums.length;i++){
            if(set.contains(nums[i])) return nums[i];
            set.add(nums[i]);
        }
        return -1;
    }
}
```

就是用一个hashset来保存元素，利用里面没有重复元素的特点



### 剑指04 二维数组中的查找

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if(matrix==null || matrix.length==0||matrix[0].length==0) return false;
        int r = matrix.length,c = matrix[0].length;
        for(int i = 0;i<r;i++){
            for(int j = 0;j<c;j++){
                if(matrix[i][j]==target) return true;
            }
        }
        return false;
    }
}
```

暴力解法，值得注意的是二维数组arr[0].length的长度是表示列的元素个数



### 剑指06 从头到尾打印链表

```java
class Solution {
    public int[] reversePrint(ListNode head) {
       List<Integer> res = new ArrayList();
       while(head!=null){
            res.add(0,head.val);
            head = head.next;
       }
       int[] arr = new int[res.size()];
       for(int i = 0;i<res.size();i++)
           arr[i] = res.get(i);
       return arr;
    }
}
```

利用ArrayList倒序加入



### 剑指07 重建二叉树





































# lc TOP 100

























































