[TOC]

# DP

1. DP定义

2. Base case

3. Induction rule

4. Fill in Order

5. Return what

6. Time && Space

  

## 最大(小)的(删除)子数组(矩阵)和(乘积) 问题

### 剑指 Offer 42. 连续子数组的最大和

[剑指 Offer 42. 连续子数组的最大和](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)

```java
/**剑指 Offer 42. 连续子数组的最大和
     输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。
     要求时间复杂度为O(n)。
     示例1:
     输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
     输出: 6
     解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
     提示：
     1 <= arr.length <= 10^5
     -100 <= arr[i] <= 100

     * Step 1 DP定义
     *      dp[i] = 到i为止的最大子数组的和 Include i
     * Step 2 Base case
     *      dp[0] = nums[0]     子数组不允许为空至少含有一个元素
     * Step 3 Induction rule
     *      取决于以i-1结尾的最大子数组的和大小---> depends on dp[i-1]
     *          dp[i-1]>0
     *              dp[i] = dp[i-1]+nums[i]
     *          dp[i-1]<=0
     *              dp[i] = nums[i]
     *      跟新global max
     *          globalMax = Math.max(globalMax, dp[i])
     * Step 4 Fill in Order
     *      左-->右
     *          因为每一个都depends on 左边的元素 dp[i-1]
     * Step 5 Return what
     *      globalMax
     * Step 6 Time && Space
     *      Time: 遍历数组O(n) 每个元素的操作 O(1)
     *      Space:  dp[n] 数组 O(n) 可以优化为 O(1) 因为只depends on 前面一个元素
     */
// 连续子数组的最大和
public int maxSubArr(int[] nums){

    if(nums==null || nums.length==0){
        return Integer.MIN_VALUE;
    }
    int len = nums.length;
    int dp = nums[0];
    int globalMax = nums[0];
    for(int i=1; i<len; i++){
        dp = Math.max(dp+nums[i], nums[i]);
        globalMax = Math.max(globalMax, dp);
    }
    return globalMax;
}
```

### 删除一次得到子数组最大和

[1186. 删除一次得到子数组最大和](https://leetcode-cn.com/problems/maximum-subarray-sum-with-one-deletion/)

```java
/**1186. 删除一次得到子数组最大和
     给你一个整数数组，返回它的某个 非空 子数组（连续元素）在执行一次可选的删除操作后，所能得到的最大元素总和。
     换句话说，你可以从原数组中选出一个子数组，并可以决定要不要从中删除一个元素（只能删一次哦），（删除后）子数组中至少应当有一个元素，然后该子数组（剩下）的元素总和是所有子数组之中最大的。
     注意，删除一个元素后，子数组 不能为空。
     请看示例：
     示例 1：
     输入：arr = [1,-2,0,3]
     输出：4
     解释：我们可以选出 [1, -2, 0, 3]，然后删掉 -2，这样得到 [1, 0, 3]，和最大。
     示例 2：
     输入：arr = [1,-2,-2,3]
     输出：3
     解释：我们直接选出 [3]，这就是最大和。
     示例 3：
     输入：arr = [-1,-1,-1,-1]
     输出：-1
     解释：最后得到的子数组不能为空，所以我们不能选择 [-1] 并从中删去 -1 来得到 0。
     我们应该直接选择 [-1]，或者选择 [-1, -1] 再从中删去一个 -1。
     提示：
     1 <= arr.length <= 10^5
     -10^4 <= arr[i] <= 10^4

     * Step 1 DP定义
     *      需要两个dp数组
     *          keep[i] = 代表到i位置 include i 的最大子数组和
     *          dp[i] = 代表到i位置为止, include i, 考虑 删 || 不删除 nums[i] 的最大子数组和
     * Step 2 Base case
     *      keep[0] = nums[0]
     *      dp[0] = nums[0]
     *      必须保证至少有一个元素, 所以base case 都是第一个元素
     * Step 3 Induction rule
     *      dp[i] = 考虑是否删除当前元素nums[i]
     *                  删除nums[i]: 必须保证前面的元素都不能删除即=keep[i-1]
     *                  不删除nums[i]: 无论如何dp[i]中都只会删除一个元素, 所以无论dp[i-1]中删除了哪个, 都不会影响连续性 =dp[i-1]+nums[i]
     *                      这里包含了删除前面一个元素和前面都不删的两种情况
     *              考虑 keep[i-1]<=0的情况
     *                  舍弃keep[i-1]直接重新开始 只有nums[i]
     *       最后考虑着三种情况中的最大值
     *       需要先跟新dp 在优化到O(1)空间的情况下, dp depends on keep
     *      keep[i] = keep[i-1]>0?keep[i-1]+nums[i] : nums[i]
     *      更新完两个dp 更新最大值
     *          不更新, 保留所有的nums[0:i]=keep[i], 中间可能删除了一个dp[i]
     *          globalMax = Math.max(globalMax, dp[i], keep[i])
     * Step 4 Fill in Order
     *      左--> 右 因为 dp[i] depends on dp[i-1] keep[i-1]
     *                  keep[i] depends on keep[i-1]
     * Step 5 Return what
     *       globalMax
     * Step 6 Time && Space
     *      Time: O(n)
     *      Space: O(2*n) dp[n] keep[n] 优化--> O(2) dp num
     */
// 允许删除一个元素的最大子数组和
public int maxSubArrWithOneDelete(int[] nums){
    if(nums==null || nums.length==0){
        return Integer.MIN_VALUE;
    }
    int len = nums.length;
    int dp = nums[0];
    int keep = nums[0];
    int globalMax = nums[0];
    for(int i=1; i<len; i++){
        dp = Math.max(keep, Math.max(nums[i], dp+nums[i]));
        keep = Math.max(nums[i], keep+nums[i]);
        globalMax = Math.max(Math.max(dp, keep), globalMax);
    }
    return globalMax;
}
```



### 环形数组最大的子数组和

[918. 环形子数组的最大和](https://leetcode-cn.com/problems/maximum-sum-circular-subarray/)

```
918. 环形子数组的最大和
给定一个由整数数组 A 表示的环形数组 C，求 C 的非空子数组的最大可能和。
在此处，环形数组意味着数组的末端将会与开头相连呈环状。（形式上，当0 <= i < A.length 时 C[i] = A[i]，且当 i >= 0 时 C[i+A.length] = C[i]）
此外，子数组最多只能包含固定缓冲区 A 中的每个元素一次。（形式上，对于子数组 C[i], C[i+1], ..., C[j]，不存在 i <= k1, k2 <= j 其中 k1 % A.length = k2 % A.length）

示例 1：

输入：[1,-2,3,-2]
输出：3
解释：从子数组 [3] 得到最大和 3
示例 2：

输入：[5,-3,5]
输出：10
解释：从子数组 [5,5] 得到最大和 5 + 5 = 10
示例 3：

输入：[3,-1,2,-1]
输出：4
解释：从子数组 [2,-1,3] 得到最大和 2 + (-1) + 3 = 4
示例 4：

输入：[3,-2,2,-3]
输出：3
解释：从子数组 [3] 和 [3,-2,2] 都可以得到最大和 3
示例 5：

输入：[-2,-3,-1]
输出：-1
解释：从子数组 [-1] 得到最大和 -1
 

提示：

-30000 <= A[i] <= 30000
1 <= A.length <= 30000
```

1. DP定义
   1. 需要求解两个dp
      1. dpMax[i] 
         - 正常的最大子数组和, 不考虑环形数组的情况
         - 表示到i为止, include i 的最大子数组和
      2. dpMin[i]
         - 考虑环形数组的情况, 我们希望求得数组中最小的子数组和, 这样去除掉中间连续的最小子数组和, 首尾的部分连在一起就是最大子数组和
         - 表示到i位置 include i的最小子数组和
2. Base case
   1. dpMax[0] = arr[0];
   2. dpMin[0] = arr[0]
   3. globalMax = Integer.MIN_VALUE
   4. globalMin = Integer.MAX_VALUE
3. Induction rule
   1. dpMax[i] = dpMax[i-1]>0?dpMax[i-1]+nums[i]:nums[i];
   2. dpMin[i] = dpMin[i-1]<0?dpMin[i-1]+nums[i]:nums[i];
   3. globalMax = Math.max(globalMax, dpMax[i])
   4. globalMin = Math.min(globalMin, dpMin[i])
4. Fill in Order
   1. 左--> 右
5. Return what
   1. Math.max(globalMax, sum-globalMin);
6. Time && Space
   1. O(n) O(1)

```java
class LargestSubArrayCircular{
    public int LargestSubArrayCircular(int[] nums){
        int curMin = Integer.MAX_VALUE;
        int curMax = Integer.MIN_VALUE;
        int globalMin = Integer.MAX_VALUE;
        int globalMax = Integer.MIN_VALUE;
        int sum = 0;
        for(int i=0; i<nums.length; i++){
            sum += nums[i];
            curMin = curMin<0?curMin+nums[i]:nums[i];
            curMax = curMax>0?curMax+nums[i]:nums[i];
            globalMin = Math.min(curMin, globalMin);
            globalMax = Math.max(curMax, globalMax);
        }
        //全负数 sum-globalMin = 0 但是不能全不选
        if(globalMax<0){
            return globalMax;
        }
        return Math.max(globalMax, sum-globalMin);
    }
}
```



### 乘积最大子数组

[152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

-  思路:  两个dp , 分别维护到i为止包括i乘积最大和最小的子数组
  - 如果i为整数, 希望以i-1结尾的子数组积尽可能大且为正数
    - dpMax[i] = Math.max(dpMax[i-1] * num[i], dpMin[i-1] * num[i], num[i])
  - 如果i为负数, 希望以i-1结尾的子数组积尽可能小且为负数
    - dpMin[i] = Math.min(dpMax[i-1] * num[i], dpMin[i-1] * num[i], num[i])
  - 更新globalMax

```java
public int maxProduct(int[] nums) {
    if(nums==null || nums.length==0){
        return 0;
    }
    int dpMax = nums[0];
    int dpMin = nums[0];
    int globalMax = nums[0];
    for(int i=1; i<nums.length; i++){
        int preMax = dpMax;
        dpMax = Math.max(dpMax*nums[i], Math.max(dpMin*nums[i], nums[i]));
        dpMin = Math.min(preMax*nums[i], Math.min(dpMin*nums[i], nums[i]));
        globalMax = Math.max(globalMax, dpMax);
    }
    return globalMax;
}
```



### 最大子矩阵

[面试题 17.24. 最大子矩阵](https://leetcode-cn.com/problems/max-submatrix-lcci/)

思路 将二维矩阵压缩到一维, 并求出压缩后一维数组的最大子数组和

- 枚举每一行开始
- 将这行开始的每一行压缩进一维数组(按列求和)
- 每压缩完一行, 求一维数组的最大子数组和
- 更新 globalMax globalStart globalEnd currentStart currentEnd
  - currentStart currentEnd每一轮压缩后都要更新为0, 因为一维数组已经改变, globalStart globalEnd记录了全局最优, currentStart currentEnd记录当前最优
  - currentEnd: 当前的index (无需更新)
  - currentStart: 
    - 如果加上index-1结尾的和, 等于之前的
    - 不加 更新为 index
  - globalStart: dp>globalMax
    - globalStart =currentStart
    - globalEnd = currentEnd

```
面试题 17.24. 最大子矩阵
给定一个正整数、负整数和 0 组成的 N × M 矩阵，编写代码找出元素总和最大的子矩阵。

返回一个数组 [r1, c1, r2, c2]，其中 r1, c1 分别代表子矩阵左上角的行号和列号，r2, c2 分别代表右下角的行号和列号。若有多个满足条件的子矩阵，返回任意一个均可。

注意：本题相对书上原题稍作改动

示例：

输入：
[
   [-1,0],
   [0,-1]
]
输出：[0,1,0,1]
解释：输入中标粗的元素即为输出所表示的矩阵
 

说明：

1 <= matrix.length, matrix[0].length <= 200
```



```java
int globalMax, currentStart, currentEnd, globalStart, globalEnd;
int rowHigh, rowLow;
public int[] getMaxMatrix(int[][] matrix) {
    if(matrix==null || matrix[0].length==0 || matrix.length==0){
        return new int[]{0,0,0,0};
    }
    int m = matrix.length;
    int n = matrix[0].length;
    rowHigh = 0;
    rowLow = 0;
    globalMax = matrix[0][0];
    currentStart = 0;
    currentEnd = 0;
    globalStart = 0;
    globalEnd = 0;
    for(int i=0; i<m; i++){
        int[] cur = new int[n];
        for(int j=i; j<m; j++){
            for(int k=0; k<n; k++){
                cur[k] += matrix[j][k];
            }
            findMax(cur, i, j);
        }
    }
    return new int[]{rowHigh, globalStart, rowLow, globalEnd};
}
private void findMax(int[] num, int m, int n){
    currentStart = 0;
    currentEnd = 0;

    int dp = num[0];
    for(int i=1; i<num.length; i++){
        if(dp>0){
            currentEnd = i;
            dp += num[i];
        }else{
            currentStart = i;
            currentEnd = i;
            dp = num[i];
        }
        if(dp>globalMax){
            globalStart = currentStart;
            globalEnd = currentEnd;
            globalMax = dp;
            rowHigh = m;
            rowLow = n;
        }
    }
}
```







### 统计矩阵中全是1的子矩阵

[1504. 统计全 1 子矩形](https://leetcode-cn.com/problems/count-submatrices-with-all-ones/)

- 思路: 将矩阵压缩到一行, 统计压缩的一位数组中连续的1的数量
  - 枚举从每一行开始, 依次将这行开始的每一行依次压缩到一位数组里(对应列的和)
  - 当出现的连续的压缩的行数(j-i+1)的时候就是一个全是1的矩阵
  - 对压缩后的一维数组, 枚举每个(j-i+1)起始的连续的1,2,3,4...列, 依次加一

```java
public int numSubmat(int[][] mat) {
    if(mat==null || mat.length==0){
        return 0;
    }
    int m = mat.length;
    int n = mat[0].length;
    int res = 0;
    //枚举每一行的开始
    for(int i=0; i<m; i++){
        int[] cur = new int[n];
        //枚举每一行压缩到一维数组cur
        for(int j=i; j<m; j++){
            // 当前行压缩进入cur后高度为j-i+1
            int target = j-i+1;
            //将第j行的每一个元素压缩进cur
            for(int k=0; k<n; k++){
                cur[k]+=mat[j][k];
            }
            // 计算压缩数组中又多少个连续的=target的组合
            res += find(cur, target);
        }
    }
    return res;
}
private int find(int[] num, int target){
    int res = 0;
    for(int i=0; i<num.length; i++){
        if(num[i]==target){
            int j=i;
            while(j<num.length && num[j]==target){
                res++;
                j++;
            }
        }
    }
    return res;
}
```

### 最大子矩阵乘积

- 思路: 将二维矩阵按行压缩到一维数组, 求出一维数组的最大子数组乘积
  - 枚举每一行开始压缩
  - 依次将这一行下面所有的行压缩进一个一维数组(按列乘积)
  - 得到的一维数组求最大乘积子数组

![image-20210802194007968](AlgorithmClub.assets/image-20210802194007968.png)

```java
@Test
public void testProdMaxMatrix(){
    double[][] matrix ={{1,5,-1},
                        {0,-1.5,1},
                        {1,1,1}};
    double res = maxMatrixProd(matrix);
    System.out.println(res);
}
//最大乘积子矩阵
public double maxMatrixProd(double[][] matrix){
    if(matrix==null || matrix.length==0 || matrix[0].length==0){
        return 0.0;
    }
    double globalMax = 0.0;
    int m=matrix.length, n=matrix[0].length;
    for(int i=0; i<m; i++){
        double[] cur = new double[n];
        Arrays.fill(cur, 1.0);
        for(int j=i; j<m; j++){
            for(int k=0; k<n; k++){
                cur[k] *= matrix[j][k];
            }
            globalMax = Math.max(globalMax, maxProd(cur));
        }
    }
    return globalMax;
}
private double maxProd(double[] num){
    double dpMax = num[0];
    double dpMin = num[0];
    double max = num[0];
    for(int i=1; i<num.length; i++){
        double preMax = dpMax;
        dpMax = Math.max(dpMax*num[i], Math.max(dpMin*num[i], num[i]));
        dpMin = Math.min(preMax*num[i], Math.min(dpMin*num[i], num[i]));
        max = Math.max(max, dpMax);
    }
    return max;
}



```

### 剑指 Offer II 040. 矩阵中最大的矩形

[剑指 Offer II 040. 矩阵中最大的矩形](https://leetcode-cn.com/problems/PLYXKQ/)

- 思路: 二维数组压缩到一维数组, 求一维数组最大子数组和

![image-20210802195807963](AlgorithmClub.assets/image-20210802195807963.png)

```java
public int maximalRectangle(String[] matrix) {
    if(matrix==null || matrix.length==0){
        return 0;
    }
    int globalMax = matrix[0].charAt(0)-'0';
    int m = matrix.length;
    int n = 0;
    for(String cur : matrix){
        n = Math.max(n, cur.length());
    }
    for(int k=0; k<m; k++){
        int[] cur = new int[n];
        for(int i=k; i<m; i++){
            String str = matrix[i];
            for(int j=0; j<str.length(); j++){
                cur[j] += str.charAt(j)-'0';
            }
            int target = i-k+1;
            globalMax = Math.max(findMax(cur,target )*target, globalMax);
        }
    }
    return globalMax;
}

private int findMax(int[] num, int target){
    int dp = num[0];
    int max = 0;
    int i = 0, j=0;
    while(i<num.length){
        if(num[i]==target){
            j = i;
            while(j<num.length && num[j]==target){
                j++;
            }
            max = Math.max(max, j-i);
            i = j;
        }else{
            i++;
        }
    }
    return max;
}
```





## 正则表达式

### 10. 正则表达式匹配

- 思路:
  - dp\[ i ][ j ] 表示s的前i个字符和p的前j个字符是否匹配
    - p[j] = '*'
      - p[j-1:j]删除用 p[j-2]和s[i]匹配
      - 如果s[i]和p[j-1]匹配, 
        - 可以用'*'匹配s[i] 
        - 删除掉s[i] 在用p[j]和s[i-1]继续匹配 因为'*'可以表示多个p[j-1]
    - s[i] 和 p[j]必须匹配
      - 字符相等
      - p[j] = '.'

 [10. 正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)

```
10. 正则表达式匹配
给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

'.' 匹配任意单个字符
'*' 匹配零个或多个前面的那一个元素
所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

 
示例 1：

输入：s = "aa" p = "a"
输出：false
解释："a" 无法匹配 "aa" 整个字符串。
示例 2:

输入：s = "aa" p = "a*"
输出：true
解释：因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
示例 3：

输入：s = "ab" p = ".*"
输出：true
解释：".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。
示例 4：

输入：s = "aab" p = "c*a*b"
输出：true
解释：因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。
示例 5：

输入：s = "mississippi" p = "mis*is*p*."
输出：false
 

提示：

0 <= s.length <= 20
0 <= p.length <= 30
s 可能为空，且只包含从 a-z 的小写字母。
p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。
保证每次出现字符 * 时，前面都匹配到有效的字符
```



```java
public boolean isMatch(String s, String p) {
    int m = s.length();
    int n = p.length();
	// f[i][j] s的第i个和p的第j个字符是否匹配
    boolean[][] f = new boolean[m + 1][n + 1];
    f[0][0] = true;
    for (int i = 0; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            //p第j个字符为*
            if (p.charAt(j - 1) == '*') {
                //删除p[j-1,j]这两个字符再去跟s[:i]匹配
                f[i][j] = f[i][j - 2];
                //如果s[i]==p[j-1] 
                if (matches(s, p, i, j - 1)) {
                    //*可以匹配s[i], 删掉s[i]保留*再跟前面匹配
                    f[i][j] = f[i][j] || f[i - 1][j];
                }
            } else {
                //p第j个字符不是* s[i]必须和p[j]
                if (matches(s, p, i, j)) {
                    f[i][j] = f[i - 1][j - 1];
                }
            }
        }
    }
    return f[m][n];
}

public boolean matches(String s, String p, int i, int j) {
    if (i == 0) {
        return false;
    }
    //是'.'一定匹配
    if (p.charAt(j - 1) == '.') {
        return true;
    }
    //字符是否相等
    return s.charAt(i - 1) == p.charAt(j - 1);
}

```





# 单调栈

- [316. 去除重复字母](https://leetcode-cn.com/problems/remove-duplicate-letters/) (困难)
- [321. 拼接最大数](https://leetcode-cn.com/problems/create-maximum-number/) (困难)
- [402. 移掉 K 位数字](https://leetcode-cn.com/problems/remove-k-digits/) (中等)
- [1081. 不同字符的最小子序列](https://leetcode-cn.com/problems/smallest-subsequence-of-distinct-characters/) （中等）

### 402. 移掉 K 位数字

- 思路: 单调栈, 从第一位字符开始依次入栈, 
  - 如果当前元素>栈顶元素 删除当前元素
  - 一直删除栈顶元素直到栈顶>当前元素 || 栈空 || 可删除元素为0
  - 如果所有元素都入栈但是k>0, 从栈顶持续出栈直到k=0
  - 删除栈底连续的0
  - 如果此时栈空 返回 "0" 
  - 将栈中元素从栈底到栈顶append到StringBuffer 返回sb

[402. 移掉 K 位数字](https://leetcode-cn.com/problems/remove-k-digits/)

![image-20210804010911925](Algorithm.assets/image-20210804010911925.png)

```java
public String removeKdigits(String num, int k) {
    Deque<Character> stack = new LinkedList<>();
    int index = 0;
    for(char cur : num.toCharArray()){
        while(k>0 && !stack.isEmpty() && stack.peekLast()>cur){
            stack.pollLast();
            k--;
        }
        stack.offerLast(cur);
    }

    while(!stack.isEmpty() && k>0){
        stack.pollLast();
        k--;
    }
    while(!stack.isEmpty() && stack.peekFirst()=='0'){
        stack.pollFirst();
    }
    StringBuffer sb = new StringBuffer();
    while(!stack.isEmpty()){
        sb.append(stack.pollFirst());
    }
    return sb.length()==0? "0":sb.toString();
}
```





# 链表

## 翻转链表

### 23. 合并K个升序链表

- 思路
  - 将list[]两个一组进行合并, 然后合并后继续两个一组合并剩下的链表

[23. 合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

```
给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

 

示例 1：

输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
示例 2：

输入：lists = []
输出：[]
示例 3：

输入：lists = [[]]
输出：[]
 

提示：

k == lists.length
0 <= k <= 10^4
0 <= lists[i].length <= 500
-10^4 <= lists[i][j] <= 10^4
lists[i] 按 升序 排列
lists[i].length 的总和不超过 10^4

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/merge-k-sorted-lists
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
```

```java
public ListNode mergeKLists(ListNode[] lists) {
    return merge(lists, 0, lists.length-1);
}

private ListNode merge(ListNode[] lists, int l, int r){
    //区间空返回null
    if(l>r){
        return null;
    }
    //区间只有一个元素 返回这个元素
    if(l==r){
        return lists[l];
    }
    int index = l;
    for(int i=l; i<=r; ){
        if(i+1<=r){
            lists[index++] = mergeTwoSortedList(lists[i],lists[i+1]);
            i+=2;
        }else{
            lists[index++] = lists[i];
            break;
        }
    }
    return merge(lists, l, index-1);
}


private ListNode mergeTwoSortedList(ListNode l1, ListNode l2){
    if(l1==null){
        return l2;
    }
    if(l2==null){
        return l1;
    }
    ListNode dummy = new ListNode(0);
    ListNode cur = dummy;
    while(l1!=null && l2!=null){
        if(l1.val<=l2.val){
            cur.next = l1;
            l1 = l1.next;
        }else{
            cur.next = l2;
            l2 = l2.next;
        }
        cur = cur.next;
    }
    cur.next = l1==null?l2:l1;
    return dummy.next;
}
```





### 24. 两两交换链表中的节点

- 思路: 新的头结点为head.next,  剩下的递归反转后接在head.next 新的头结点.next = head

[24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

![image-20210803154224601](Algorithm.assets/image-20210803154224601.png)



```java
//递归
public ListNode swapPairs(ListNode head) {
    if(head==null || head.next==null){
        return head;
    }        
    ListNode newHead = head.next;
    head.next = swapPairs(newHead.next);
    newHead.next = head;
    return newHead;
}
//迭代
public ListNode swapPairs(ListNode head) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode cur = dummy;
    while(cur.next!=null && cur.next.next!=null){
		ListNode node1 = cur.next;
        ListNode node2 = cur.next.next;
        cur.next = node2;
        node1.next = node2.next;
        node2.next = node1;
        cur = node1;
    }
    return dummy.next;
}
```



### 25. K 个一组翻转链表

- 思路
  - 计算链表结点数量n
  - 需要翻转 n/k次
  - 将k个结点翻转后, 返回链表的 head tail tail.next

[25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

![image-20210803154757135](Algorithm.assets/image-20210803154757135.png)

```java
public ListNode reverseKGroup(ListNode head, int k) {
    int num = 0;
    ListNode cur = head;
    while(cur!=null){
        num++;
        cur = cur.next;
    }
    int numRever = num/k;
    cur = head;
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    cur = head;
    ListNode temp = dummy;
    while(numRever>0){
        ListNode[] curRev = reverseK(cur,k);
        temp.next = curRev[1];
        temp = curRev[0];
        cur = curRev[2];
        numRever--;
    }
    temp.next = cur;
    return dummy.next;
}

private ListNode[] reverseK(ListNode node, int k){
    ListNode prev=null, cur=node, next=null;
    while(k>0){
        next = cur.next;
        cur.next = prev;
        prev = cur;
        cur = next;
        k--;
    }
    return new ListNode[]{node, prev, cur};
}
```



### 92. 反转链表 II

- 思路
  - 先遍历得到left位置的结点
  - 从这个位置开始翻转, 返回翻转完的head tail tai.next
  - 连接起来

[92. 反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

![image-20210803160212294](Algorithm.assets/image-20210803160212294.png)



```java
public ListNode reverseBetween(ListNode head, int left, int right) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode cur = dummy;
    int index = 1;
    while(index<left){
        cur = cur.next;
        index++;
    }
    ListNode curRev = reverse(cur.next, right-left+1);
    cur.next = curRev;
    return dummy.next;

}

private ListNode reverse(ListNode node, int n){
    ListNode prev = null, cur = node, next = null;
    while(n>0){
        next = cur.next;
        cur.next = prev;
        prev = cur;
        cur = next;
        n--;
    }
    node.next = cur;
    return prev;

}
```









# Tree

## 1740. 找到二叉树中的距离

- 思路: 找到两个结点的最深公共祖先,
  - left  right 表示p q 到父节点中间的结点数量(包括p q, 不包括祖先结点
  - find表示是否已经找到了祖先结点
  - p q分在祖先的两边, return left+right
  - p q 分在祖先的一边 那么一定有一个结点为另一个结点的祖先 return 下面那个结点返回的值
  - 如果当前还没到祖先结点, find=false 从下往上返回的时候, 如果返回了其中一个结点的值, 需要将这个值+1表示经过的结点数量+1
  - 如果已经找到了祖先结点, 直接返回left+right 并且标记 find=true 后续直接返回传上来的值即可

[1740. 找到二叉树中的距离](https://leetcode-cn.com/problems/find-distance-in-a-binary-tree/)

![image-20210803015332333](AlgorithmClub.assets/image-20210803015332333.png)



# String Manipulation

## 字符串比较

### 30. 串联所有单词的子串

[30. 串联所有单词的子串](https://leetcode-cn.com/problems/substring-with-concatenation-of-all-words/)

```java
Map<String ,Integer> allWords;
public List<Integer> findSubstring(String s, String[] words) {
    List<Integer> res = new ArrayList<>();
    allWords = new HashMap<>();
    for(String str : words){
        allWords.put(str, allWords.getOrDefault(str,0)+1);
    }
    int wordNum = words.length;
    int wordLen = words[0].length();
    int len = s.length();
    // len-i >= wordNum*wordLen
    for(int i=0; i<=len-wordNum*wordLen; i++){
        if(match(s,i,wordLen, wordNum)){
            res.add(i);
        }
    }
    return res;
}

private boolean match(String s, int i, int wordLen, int wordNum){
    Map<String ,Integer> curWords = new HashMap<>();
    while(wordNum>0){
        String cur = s.substring(i, i+wordLen);
        curWords.put(cur, curWords.getOrDefault(cur,0)+1);
        i = i+wordLen;
        wordNum--;
    }
    if(curWords.size()!=allWords.size()){
        return false;
    }
    for(Map.Entry<String,Integer> entry:curWords.entrySet()){
        String cur = entry.getKey();
        int cnt = entry.getValue();
        if(!allWords.containsKey(cur)||allWords.get(cur)!=cnt){
            return false;
        }
    }
    return true;

}
```







# TOP300

## 4. 寻找两个正序数组的中位数

- 思路 : 数组已经排序了, 类似于合并两个升序链表, 当合并到中间位置的时候求出
  - 如果两个数组的长度之和为 从1开始
  - 奇数 pos = (m+n)/2+1
  - 偶数 pos = (m+n)/2 (m+n)/2+1 这两个位置的数的平均数

[4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

```java
给定两个大小分别为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的 中位数 。

示例 1：

输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
示例 2：

输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
示例 3：

输入：nums1 = [0,0], nums2 = [0,0]
输出：0.00000
示例 4：

输入：nums1 = [], nums2 = [1]
输出：1.00000
示例 5：

输入：nums1 = [2], nums2 = []
输出：2.00000
 

提示：

nums1.length == m
nums2.length == n
0 <= m <= 1000
0 <= n <= 1000
1 <= m + n <= 2000
-106 <= nums1[i], nums2[i] <= 106
 

进阶：你能设计一个时间复杂度为 O(log (m+n)) 的算法解决此问题吗？


```

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    if(nums1==null && nums2==null){
        return 0.0;
    }
    if(nums1.length==0 && nums2.length==0){
        return 0.0;
    }
    int m = nums1.length;
    int n = nums2.length;

    boolean isEven = (m+n)%2==0;
    //对于奇数想要找到的是(m+n)/2+1
    //对于偶数想要找到的是(m+n)/2
    int target = isEven?(m+n)/2:(m+n)/2+1;
    int i=0, j=0;
    int cnt = 0;
    int first = Integer.MAX_VALUE;
    while(i<m && j<n){
        cnt++;
        int cur = 0;
        if(nums1[i]<=nums2[j]){
            cur = nums1[i];
            i++;
        }else{
            cur = nums2[j];
            j++;
        }
        if(cnt==target){
            first = cur;
            if(!isEven){
                return first;
            }
        }else if(cnt==target+1){
            return (first+cur)*1.0/2;
        }
    }
    while(i<m){
        cnt++;
        if(cnt==target){
            first = nums1[i];
            if(!isEven){
                return first;
            }
        }else if(cnt==target+1){
            return (first+nums1[i])*1.0/2;
        }
        i++;
    }
    while(j<n){
        cnt++;
        if(cnt==target){
            first = nums2[j];
            if(!isEven){
                return first;
            }
        }else if(cnt==target+1){
            return (first+nums2[j])*1.0/2;
        }
        j++;
    }
    return 0.0;
}
```





正则表达式去匹配IP地址

https://www.cnblogs.com/xiaoxiao075/p/10351122.html
