# 70.爬楼梯

### 原题
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。
每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？
注意：给定 n 是一个正整数。

示例 1：
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶

示例 2：
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶

来源：力扣（LeetCode）
[链接](https://leetcode-cn.com/problems/climbing-stairs)：https://leetcode-cn.com/problems/climbing-stairs

### 解法：动态规划

```java
	public int climbStairs(int n) {
        int[] count = new int[n + 1];
        count[0] = 1;
        count[1] = 1; // 边界条件
        for(int i = 2; i <= n; i++)
            count[i] = count[i - 1] + count[i - 2]; // 转移方程
        return count[n];
    }
```

思路分析：

* 题目要求共有多少种方法可以到达楼顶。每一个阶梯都可以选择走两个阶梯或者1个阶梯，看起来是可以通过回溯来解决的计数问题，但其中存在很多重复计算比如1->2->3与1->3两种方式走到第三个阶梯，之后的走法在回溯中都会重复计算。这样的问题 是典型的动态规划的问题。
* 动态规划第一步：确定状态，找子问题及完成问题的最后一步。最后一步是，达到最后一个阶梯有多少种方法。要到达最后一个阶梯，可以从倒数第二个台阶走两阶，也可以从倒数第一个台阶走一阶。所以到达最后一个台阶的方法数，就转化为求两个子问题，到达倒数第一，二个台阶有多少方法，然后再相加。所以状态就是到达第i个台阶有多少种方法。用`count[i]`表示到达第`i`个台阶的方法总数。
* 动态规划第二步：写出状态转移方程。从上面的描述可以直接得出来 转移方程为`count[i] = count[i - 1] + count[i - 2]`。
* 动态规划第三步：边界条件，也就是找到无法通过转移方程得到的结果。大部分情况都是结合状态的实际意义来确定的。在本题中`count[0]=0,count[1]`的意义就是第0阶台阶有一种方法达到，就是不跳；第一阶台阶有一种方法就是从0阶跳一阶。
* 动态规划第四步：确定计算方向，这个看状态方程与边界条件。在本题中显然是从小台阶得到大台阶。
* 时间负责度为$O(n)$，空间复杂度也为$O(n)$。

运行结果：
* 执行用时 :0 ms, 在所有 Java 提交中击败了100.00%的用户
* 内存消耗 :36.1 MB, 在所有 Java 提交中击败了5.17%的用户