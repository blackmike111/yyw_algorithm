# 231. 2的幂

### 原题
给定一个整数，编写一个函数来判断它是否是 2 的幂次方。

示例 1:
输入: 1
输出: true
解释: 2^0 = 1

示例 2:
输入: 16
输出: true
解释: 2^4 = 16

示例 3:
输入: 218
输出: false

来源：力扣（LeetCode）
[链接](https://leetcode-cn.com/problems/power-of-two)：https://leetcode-cn.com/problems/power-of-two

### 两种解法

##### 1.统计给定数字的二进制表示出现了几个1

```java
public boolean isPowerOfTwo(int n) {
        if(n < 0)
            return false;
        int count = 0;
        while(n != 0){ // 2的幂次 二进制位级表示只有1个1存在。
            if((n & 1) == 1)
                count++;
            n >>>= 1;
            if(count > 1) return false; // 当两个1存在时，肯定不满足条件了 直接返回false
        }
        return count == 1;
    }
```

思路分析：

* 将二进制数转换为十进制数的时候，二进制表示从右往左，遇到1就加上`2^(i - 1)`。如果一个数是2的幂次，就意味着它的二进制表示中只有一个1。
* 不过在计算机中，要注意符号位为1，其余位为0的情况。恰好，所有的负数都不可能是2的幂次，所以排除负数的时候就把这种情况也排除了。
* 要统计二进制表达式中数字位数为 ‘1’ 的个数。
    * 任何一个数`n`和1做与运算`n & 1`，得到的都是n的二进制表示的第一位的数字。得到第一位之后如何得到第二位，可以对`n`进行右移1位，这样`n`原来的第一位就被舍去，第二位被移动到第一位。
    * 但是这里要注意，在java中右移`>>`进行有符号右移动，对于负数其高位会一直添加1，移位统计就永远不会结束。所以我们使用无符号右移`>>>`，无论正数还是负数都在高位补0。
    * 每次将n无符号右移一位，直到`n==0`，此时已经将所有的1统计完成了
    * 使用一个计数变量`count`计数1的次数，在遍历当中如果`count > 1`，那么该数字二进制表示中至少有2位是一，不可能是2的幂次，直接返回`false`。
* 当循环结束后，返回`count == 1`。
* 时间复杂度：$O(1)$ 。运行时间依赖于数字 n的位数。由于这题中 n 是一个 32 位数。空间复杂度是$O(1)$的。

运行结果：

* 执行用时 :1 ms, 在所有 Java 提交中击败了100.00%的用户
* 内存消耗 :36.8 MB, 在所有 Java 提交中击败了5.50%的用户

##### 2.位运算技巧

```java
public boolean isPowerOfTwo2(int n) {
        if(n < 0) return false;
        return (n & (n - 1)) == 0;
    }
```

思路分析：

* 在刚才方法一中已经提到，如果一个数是2的幂次，就意味着它的二进制表示中只有一个1。
* 有一个技巧 x & (x - 1) 将x位级中最右的一位 1 改为 0。对于2的幂，改了之后一定是一串0000000。
* 所以排除了绝对不可能是2的幂的负数之后`if(n < 0) return false;`，就只需要判断`n & (n - 1)`的结果是不是0，如果是，那么它是2的幂次。