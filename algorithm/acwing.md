## 基础算法(一)

### 快排

思想：基于分治

步骤：1.确定分界点 左边界，右边界，中边界，随机边界

​            2.**调整区间**：根据x划分整连个区间，使得第一个区间里的所有的数都<=x，第二个区间里面所有的数都>=x

​                如何优美的做呢？ 利用两个指针 逐渐往中间移动，如果发现有超出条件的情况就**互换位置**，直到i和j相      遇为止

​            3.递归处理左右两端（先分完在递归两边）

**lc 912 排序数组** 

```java
class Solution {
    public int[] sortArray(int[] nums) {
        quicksort(nums,0,nums.length-1);
        return nums;
    }

    public void quicksort(int[] nums,int l,int r){
        if(l>=r) return;
        //确定分界点,以及左右边界
        int x = nums[l],i = l-1,j = r+1;
        //每次交换
        while(i<j){
            do{i++;} while(nums[i]<x);
            do{j--;} while(nums[j]>x);
            //如果两个指针还没有相遇，就交换一下
            if(i<j) swap(nums,i,j);
        }
        //递归处理左右两边(分治了)
        quicksort(nums,l,j);
        quicksort(nums,j+1,r);
    }

    public void swap(int[] nums,int l,int r){
        int temp = nums[l];
        nums[l] = nums[r];
        nums[r] = temp;
    }
}
```

**aw 785 快速排序** 

```java
//AcWing 785
public class QuickSort {
    public static void main(String[] args) throws IOException {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] q = new int[n];
        for(int i=0; i<n; i++){q[i] = sc.nextInt();}

        quickSort(q, 0, q.length-1);
        for(int i=0; i<n; i++){System.out.print(q[i] + " ");}
    }

    private static void quickSort(int[] q,int l,int r) {
        if(l>=r) return;
        int x = q[l],i = l-1,j = r+1;
        while (i<j){
            do {i++;}while (q[i]<x);
            do {j--;}while (q[j]>x);
            if(i<j){
                int t = q[i];
                q[i] = q[j];
                q[j] = t;
            }
        }
        //递归
        quickSort(q,l,j);
        quickSort(q,j+1,r);
    }
}

```

**aw 786第k个数** 

```java
public class NumberK {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int k = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }
        System.out.println(quickSort(arr, 0, arr.length - 1, k));

    }

    public static int quickSort(int[] q,int l,int r,int k){
        if(l>=r) return q[l];
        int x = q[l],i = l-1,j = r+1;
        while (i<j){
            do{i++;}while (q[i]<x);
            do{j--;}while (q[j]>x);
            if (i<j){
                int t = q[i];
                q[i] = q[j];
                q[j] = t;
            }
        }
        int sj = j-l+1;
        if (k>=sj) return quickSort(q,l,j,k);
        //从右边找
        else return quickSort(q,j+1,r,k-sj);
    }
}
```



### 归并排序(nlogn)

思想：分治，但是跟快速排序分的条件不一样，归并是以**中间点**作为分的条件,并且是先递归

步骤：1.找分界点mid=(l+r)/2

​            2.递归排序左边和右边

​            3.归并，把两个有序的数组合并成一个有序的数组（合二为一,**难点**）双指针

**ac 787 归并排序** （注意一切的条件都是**<=**就行了）

```java
import java.util.Scanner;

//归并排序
public class MargeSort {
    static int[] tem = new int[1000010];
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }
        merge_sort(arr,0,n-1);
        for (int i = 0; i < arr.length; i++) {
            System.out.print(arr[i]+" ");
        }
    }

    private static void merge_sort(int[] q,int l,int r) {
        if (l>=r) return;
        //第一步确定中点
        int mid = (l+r)/2;
        //第二步递归排左边和右边
        merge_sort(q,l,mid);
        merge_sort(q,mid+1,r);
        //归并,k表示数组里面已经有多少个数，这里递归完了以后就已经是有序的两个区间了了
        int k = 0,i = l,j = mid+1;
        while (i<=mid && j<=r){
            if (q[i]<=q[j]) tem[k++] = q[i++];
            else tem[k++] = q[j++];
        }
        //处理左半边或者右半边没有循环完的情况
        while (i<=mid) tem[k++] = q[i++];
        while (j<=r) tem[k++] = q[j++];
        //把结果拿回来
        for ( i = l,j = 0; i <=r ; i++,j++) {
            q[i] = tem[j];
        }
    }
}
```



**ac 788 逆序对的数量**

```java
public class Main {
    static int[] temp = new int[1000010];
    static long res = 0;
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) 
           arr[i] = in.nextInt();
        mergeSort(arr,0,arr.length-1);
        System.out.println(res);
    }

    private static void mergeSort(int[] q,int l,int r) {
        if(l>=r) return;
        int mid = (l+r)/2;
        mergeSort(q,l,mid);
        mergeSort(q,mid+1,r);
        int i = l,j = mid+1,k = 0;
        while (i<=mid&&j<=r){
            if(q[i]<=q[j]) temp[k++] = q[i++];
            else {
                temp[k++] = q[j++];
                res+=mid -i +1;
            }
        }
        while (i<=mid) temp[k++] = q[i++];
            while (j<=r) temp[k++] = q[j++];
            for ( i = l,j = 0; i <= r ; i++,j++) q[i] = temp[j];
    }
}
```

记住**res+=mid -i +1;**



### 二分查找(整数二分)

二分的本质是边界
二分法用于查找, 每次都选择**答案所在的区间再次进行查找**, 当区间**长度为 1时**, 就是答案

**模板一**：这个一般是mid在右半边的情况,这里是**先确定的l->mid，是先确定了右边界**

```java
// 区间[l, r]被划分成 [l, mid] 和 [mid+1, r]时使用
int bsearch_1(int l, int r) {
    while (l < r) {
        int mid = l + r >> 1;
        if (check(mid))  // check() 判断 mid是否满足性质
            r = mid; 
        else
            l = mid + 1;
    }
    return l;
}
```

**模板二**：这个一般是mid在左半边的情况,注意这里是**先确定的mid->r,是先确定了左边界** 

```java
// 区间[l, r]被划分成 [l, mid-1] 和 [mid, r]时使用
int bsearch_2(int l, int r) {
    while (l < r) {
        int mid = l + r + 1 >> 2;   // 注意
        if (check(mid))  // check() 判断 mid是否满足性质
            l = mid;
        else
            r = mid - 1;

    }
    return l;
}
```

**ac 789**

```java
public class Ac789 {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        //数组大小
        int n = in.nextInt();
        //查询的次数
        int count = in.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = in.nextInt();
        }
        while (count-->0){
            int x = in.nextInt();
            int l = 0,r = n-1;
            //这个循环结束的时候l和r是相等的情况
            while (l<r){
                int mid = (l+r)/2;
                //check函数 mid>x说明 r = mid不需要补上+1
                if (arr[mid]>=x) r = mid;
                else l = mid+1;
            }
            //说明没有找到x，就是说从左往右看第一个值不等于x
            if (arr[l]!=x) System.out.println("-1 -1");
            else {
                System.out.print(l + " ");
                l = 0;
                r = n-1;
                while (l<r){
                    int mid = (l+r+1)/2;
                    if (arr[mid]<=x) l = mid;
                    else r = mid-1;
                }
                System.out.println(l);
            }
        }
    }
}
```

**力扣版本34**：

```java
class Solution {
    public int[] searchRange(int[] n, int t) {
        if(n.length == 0) return new int[]{-1,-1};
        int l = 0,r = n.length-1;
        while(l<r){
            int mid = (l+r)/2;
            if(n[mid]>=t) r = mid;
            else l = mid+1;
        } 
        int L = l;
        if(n[l]!=t) return new int[]{-1,-1};
         l = 0; r = n.length-1;
         while(l<r){
             int mid = (l+r+1)/2;  
             if(n[mid]<=t) l = mid;
             else r = mid-1; 
            }
         return new int[]{L,l};       
    }
}
```



### 二分查找(浮点数二分)

 详情参照整数二分

**ac 790. 数的三次方根**

```java
import java.util.Scanner;

//ac 790. 数的三次方根
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        double n = in.nextDouble();
        double l = -10000,r = 10000;
        while (r-l>=10e-8){
            double mid = (l+r)/2;
            if (mid*mid*mid>=n) r = mid;
            else l = mid;
        }
        System.out.printf("%.6f",l);
    }
}
```

注意题目让保留6位小数的话最好是while循环里面条件 r-l>=10e-8 



## 基础算法(二)

### 高精度

因为java中有大数的类，所以不用关注~~



###  前缀和

通用公式

```
case 1： 前缀和
对于前缀和它的维护的时间复杂度与数据规模有关，但是对于求某个区间的和的操作的时间复杂度却为O(1)
一维前缀和： 维护： s[i] = s[i-1] + a[i]
求[l, r]区间的和：s[r] - s[l-1]
二维前缀和： 维护： s[i][j] = s[i-1][j] + s[i][j-1] - s[i-1][j-1] + a[i][j]
求[x1, y1] 到 [x2, y2]的和： s[x2][y2] - s[x1-1][y2] + s[x2][y1-1] - s[x1-1][y1-1]
三维前缀和： 维护: s[i][j][k] = s[i-1][j][k] + s[i][j-1][k] + s[i][j][k-1] - s[i-1][j-1][k] - s[i-1][j][k-1] - s[i][j-1][k-1] + s[i-1][j-1][k-1] + a[i][j][k]
求[x1, y1, z1] 到 [x2, y2, z2]的和： s[x2][y2][z2] - s[x1-1][y2][z2] - s[x2][y1-1][z2] - s[x2][y2][z1-1] + s[x1-1][y1-1][z2] + s[x1-1][y2][z1-1] + s[x2][y1-1][z1-1] - s[x1-1][y1-1][z1-1]

case2 : 差分
对于差分，维护时只需要O(1)的时间复杂度，但是求某个位置上元素的值却与数据规模有关
一维差分： 将[l, r]上的所有数+c :b[l] += c , b[r+1] -= c
求a[i]的值： 其实就是求b数组的一位前缀和
二维差分： 将[x1, y1]到[x2, y2]上的数字+c: b[x1][x2]+=c , b[x2+1][y1] -= c , b[x1][y2+1] -=c , b[x2+1][y2+1] +=c
求a[i]的值: 其实就是求b数组的二维前缀和
三维差分： 将[x1, y1, z1]到[x2, y2, z2]上的数字+c: b[x1][y1][z1]+=c , b[x2+1][y1][z1] -=c , b[x1][y2+1][z1] -= c , b[x1][y1][z2+1]-=c , b[x2+1][y2+1][z1] +=c , b[x2+1][y1][z2+1] +=c , b[x1][y2+1][z2+1] +=c , b[x2+1][y2+1][z2+1] -=c
求a[i]的值：其实就是求b数组的三维前缀和
```

**ac 795 前缀和**

```java
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int m = in.nextInt();
        //注意长度
        int[] a = new int[n+1];
        int[] s = new int[n+1];
        for (int i = 1; i <=n ; i++) a[i] = in.nextInt();
        //求前缀和的公式（前缀和的初始化）
        for (int i = 1; i <=n ; i++) s[i] = s[i-1]+a[i];
        while (m-->0){
            int l = in.nextInt();
            int r = in.nextInt();
            //求区间的前缀和公式（区间和的计算）
            System.out.println(s[r]-s[l-1]);
        }
    }
}
```

下标从1开始的两套：

```java
s[i+1] = s[i] + nums[i];
s[r+1]-s[l]==k
```



 **ac 796 子矩阵的和**

```java
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int m = in.nextInt();
        int q = in.nextInt();
        int[][] a = new int[n+1][m+1],s =new int[n+1][m+1];
        for (int i = 1; i <=n; i++)
            for (int j = 1; j <=m; j++)
                a[i][j] = in.nextInt();

        for (int i = 1; i <=n ; i++)
            for (int j = 1;j<=m;j++)
                //求前缀和
                s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + a[i][j];
        int x1, y1, x2, y2;
        while(q-- > 0) {
            x1 = in.nextInt(); y1 = in.nextInt(); x2 = in.nextInt(); y2 = in.nextInt();
            //算部分和
            System.out.println(s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1]);
        }
    }
}
```

要注意的是数组长度都要+1(其实+多少都是可以的只要保证不越界就行了...)，然后for循环的话就是i从1开始，条件是<=



### 差分

**aw 797 差分**

```java
import java.util.Scanner;

//差分
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int m = in.nextInt();
        int[] a = new int[n+100];
        int[] b = new int[n+100];
        for (int i = 1; i <=n; i++) {
            a[i] = in.nextInt();
            //构造差分数组
            b[i] = a[i]-a[i-1];
        }
        while (m-->0){
            int l = in.nextInt(),r = in.nextInt(),c = in.nextInt();
            //在差分数组的l上+c,那么就必须在r+1上-c，保证数据的一致性
            b[l]+= c;
            b[r+1]-=c;
        }
        //重新构造a数组
        for (int i = 1; i <=n ; i++) {
            a[i] = b[i]+a[i-1];
            System.out.print(a[i]+" ");
        }
    }
}
```

差分是前缀和的兄弟，他们的推算公式很相近（背就好）



### 双指针算法

类型：一般可以有两种类型，第一种是两个指针在一个序列（快排），另一种是两个指针在两个序列（归并）

核心思想：

模板：

```java
for(i = 0,i = 0;i<n;i++){
    //更新j
    while(j<i&&check(i,j)) j++;
    //每道题目的具体逻辑
}
```



**ac 799最长连续不重复子序列**

```java
import java.util.Scanner;

//ac 799最长连续不重复子序列
public class Main {
    //每一个数出现的次数
    static int[] s = new int[100010];
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int[] a = new int[n];
        for (int i = 0; i < n; i++) a[i] = in.nextInt();
        int res = 0;
        for (int i = 0,j = 0; i < n; i++) {
            //看看有没有重复的数字
            s[a[i]] ++;
            //有重复的话j就会一直追赶i，追赶上在重新等待
            while (j<i && s[a[i]]>1){
                s[a[j]] --;
                j++;
            }
            res = Math.max(res,i-j+1);
        }
        System.out.println(res);
    }
}
```

学到了查看重复元素的方法！！





### 位运算

模板题目：n的二进制表示中第k位数是几

lowbit操作：返回x的最后一位是1，&:两个都是1，结果就是1，|两个只要有一个结果为1就是1

思路：1.先把第k位移到最后一位 n>>k

​            2.看个位是几

**求n的第k位数字（把n的第k位上的二进制数）：n>>k&1**

**返回n的最后一位：lowbit(n) = n&-n**

```java
public class WeiYunSuan {
    public static void main(String[] args) {
        int n = 10;
        //输出的是n的二进制表示 1010
        for (int i = 3; i >=0 ; i--) System.out.println(n>>i&1);
    }
}
```



**ac 801 二进制中1的个数**

```java
//ac 801二进制中一的个数
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        for (int i = 0; i < n; i++) {
            int x = in.nextInt();
            int res = 0;
            while (x>0){
                //等号后面的操作是返回x的最后一位1，那么减几次1就说明有几个1，res就加几次
                x-=x&-x;
                res++;
            }
            System.out.print(res+" ");
        }
    }
}
```

**记住：x-=x&-x;** 

原码  1010

反码  0101

补码  0110

原码取反+1 = 补码 减去一个数就等于加上这个数的补码





### 离散化(整数)

基本含义：

**ac 802 区间和**

```java

```











### 合并区间









































