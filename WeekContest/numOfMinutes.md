# 5354. 通知所有员工所需的时间

### 原题
公司里有 n 名员工，每个员工的 ID 都是独一无二的，编号从 0 到 n - 1。公司的总负责人通过` headID `进行标识。
在` manager` 数组中，每个员工都有一个直属负责人，其中` manager[i]` 是第` i` 名员工的直属负责人。对于总负责人，`manager[headID] = -1`。题目保证从属关系可以用树结构显示。
公司总负责人想要向公司所有员工通告一条紧急消息。他将会首先通知他的直属下属们，然后由这些下属通知他们的下属，直到所有的员工都得知这条紧急消息。
第 i 名员工需要` informTime[i]` 分钟来通知它的所有直属下属（也就是说在 `informTime[i] `分钟后，他的所有直属下属都可以开始传播这一消息）。
返回通知所有员工这一紧急消息所需要的 分钟数 。 

示例 1：
输入：`n = 1, headID = 0, manager = [-1], informTime = [0]`
输出：0
解释：公司总负责人是该公司的唯一一名员工。

示例 2：
输入：`n = 6, headID = 2, manager = [2,2,-1,2,2,2], informTime = [0,0,1,0,0,0]`
输出：1
解释：id = 2 的员工是公司的总负责人，也是其他所有员工的直属负责人，他需要 1 分钟来通知所有员工。
上图显示了公司员工的树结构。

示例 3：
输入：`n = 7, headID = 6, manager = [1,2,3,4,5,6,-1], informTime = [0,6,5,4,3,2,1]`
输出：21
解释：总负责人 id = 6。他将在 1 分钟内通知 id = 5 的员工。
id = 5 的员工将在 2 分钟内通知 id = 4 的员工。
id = 4 的员工将在 3 分钟内通知 id = 3 的员工。
id = 3 的员工将在 4 分钟内通知 id = 2 的员工。
id = 2 的员工将在 5 分钟内通知 id = 1 的员工。
id = 1 的员工将在 6 分钟内通知 id = 0 的员工。
所需时间 = 1 + 2 + 3 + 4 + 5 + 6 = 21 。

示例 4：
输入：`n = 15, headID = 0, manager = [-1,0,0,1,1,2,2,3,3,4,4,5,5,6,6], informTime = [1,1,1,1,1,1,1,0,0,0,0,0,0,0,0]`
输出：3
解释：第一分钟总负责人通知员工 1 和 2 。
第二分钟他们将会通知员工 3, 4, 5 和 6 。
第三分钟他们将会通知剩下的员工。

示例 5：
输入：`n = 4, headID = 2, manager = [3,3,-1,2], informTime = [0,0,162,914]`
输出：1076

提示：

```
1 <= n <= 10^5
0 <= headID < n
manager.length == n
0 <= manager[i] < n
manager[headID] == -1
informTime.length == n
0 <= informTime[i] <= 1000
如果员工 i 没有下属，informTime[i] == 0 。
```

题目 保证 所有员工都可以收到通知。

来源：力扣（LeetCode）
[链接](https://leetcode-cn.com/problems/time-needed-to-inform-all-employees)：https://leetcode-cn.com/problems/time-needed-to-inform-all-employees

### 解法

```java
	public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        int[] count = new int[n]; // 表示到通知到第i个员工需要几分钟
        Arrays.fill(count, -1);
        count[headID] = 0;
        int res = 0;
        for (int i = 0; i < n; i++) {
            if (count[i] != -1)
                continue;
            int p = manager[i]; //p 第 i 名员工的直属负责人
            int sum = informTime[p];
            while (count[p] == -1) {
                p = manager[p];
                sum += informTime[p];
            }
            count[i] = sum + count[p];
            res = Math.max(res, count[i]);
            p = manager[i];
            sum = count[i];
            while (count[p] == -1) {
                sum -= informTime[p];
                count[p] = sum;
            }
        }
        return res;
    }
```

思路分析：

* 首先理解题目中标号的意义`manager[i]`表示`i`这名员工的上级的标号为`manager[i]`，`manager`数组的值的范围是`0-n-1`，但是有一个例外`manager[headID]=-1`表示编号为`headID`的员工是最高领导。`informTime[i]`表示`i`这名员工将消息传给直接下级的时间。
* 将员工的上下级关系图画出来，员工之间用有向箭头链接，由上级指向下级，边的权值设置为上级通知下级所用的时间。这就是一个从根到叶子的树，要求的问题就转化为到叶子结点的最长路径。所以很显然进行DFS将所有到叶子的路径都遍历出来，选取最长路径的值即可。
* 但是用树的做法需要用数据结构来表示这个树（其实说图也可以），这可能会消耗很大。
* 换一个思路，要找最长时间，可以从领导到最下级；也可以从每一个最下级到最高领导。恰好`manager[i]`的意义就是由下级找到它的上级，这样一直跌打，都会到达`manager[headID]=-1`，这样一条路径就找到了。
* 但是对于每一个最下级，都进行一次这样的寻找，会有大量的重复计算，因为很多下级会有共同的上级。且要知道最下级到达最高领导需要多少分钟，我们也必需知道次下级到达最高领导需要多少分钟加上次下级到达最下级的时间。
* 所以我们可以定义一个状态量`int[] count`，`count[i]`表示`i`这个员工到达最高领导需要多少时间。然后我们求出每个`count[i]`，最大的一个就是答案。
* 在计算`count[i]`时，是一个自底向上的过程，为了避免重复计算，我们将`count[i]`赋初值为-1，表示`i`这个员工到最高领导的时间还没计算，如果已经计算过就设置为结算结果。一个特殊的边界条件就是`count[headID] = 0;`最高领导到其自身的时间为0。
* 具体计算过程看代码解释。

代码解释：

* 6-8行，已经计算过到达最高领导所需时间的员工直接跳过。
* 9-14行，不断找到当前员工的领导，并将这二人之间的时间累加，直到某员工到达最高领导的时间已经知道，那么`i`这个员工到达最高领导的时间就是`sum+count[p]`。第四行设置了边界条件，所以循环一定会停止。
* 16行，更新最大时间。
* 17-22行，`i`这个员工记录了时间，但是这条路径上还有部分他的领导没有更新，再往上将其领导的`count`进行记录。`p`到达最高领导的时间=他到最高领导的时间-`p`到他的时间。

运行结果：17ms