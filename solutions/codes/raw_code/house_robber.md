# 问题

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

示例 1：
输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。 偷窃到的最高金额 = 1 + 3 = 4 。

提示：
> 0 <= nums.length <= 100
> 0 <= nums[i] <= 400

## 解法

- 偷 还是 不偷? 为了使得我们光顾完所有房子之后获得的现金额最大, 我们需要考虑怎样去最优化地偷?
这里虽然数据量很小, 只有100, 但是也是没有办法找到一个暴力地算法可以在规定时间内通过(O(2^N))

- 我们对于当前房子执行的操作, 会影响到对于相邻的下一个房子的操作(即如果偷了, 我们就不能够偷下一个;否则我们仍然可以偷下一个)

- 我们定义这样的状态 `dp[i][0/1]` 代表截至到第i个房子, 最终状态是偷(1)/不偷(0), 所能够获得的最大价值;

- 可以看到这一的状态定义符合动态规划的适用规则: 最优子问题(最大价值)+无后效性(确定了之前的状态就一定可以确定下一个状态)

### 状态转移方程

- `dp[i][0] = max(dp[i - 1][0], dp[i - 1][1])` 表示当前第i个房子不偷的情况下, 能够获得的最大价值等于从之前状态的直接转移;

- `dp[i][1] = dp[i - 1][0] + nums[i]` 表示之前不偷的最大价值+当前房子内拥有的价值, 一定是`[0,i]`最大的价值组合;

### 代码

```cpp++
class Solution {
public:
    int dp[105][2];

    int rob(vector<int>& nums) {
        int n = nums.size();

        dp[0][0] = 0;
        dp[0][1] = nums[0];

        for(int i = 1; i < n; ++i){
            dp[i][0] = max(dp[i - 1][1], dp[i - 1][0]);
            dp[i][1] = dp[i - 1][0] + nums[i];
        }

        return max(dp[n - 1][0], dp[n - 1][1]);
    }
};
```
