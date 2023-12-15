#Interview Question Patterns

1) > There are n points on a road you are driving your taxi on. The n points on the road are labeled from 1 to n in the direction you are going, and you want to drive from point 1 to point n to make money by picking up passengers. <br /> You cannot change the direction of the taxi. <br />
The passengers are represented by a 0-indexed 2D integer array rides, where rides[i] = [starti, endi, tipi] denotes the ith passenger requesting a ride from point starti to point endi who is willing to give a tipi dollar tip.<br />
For each passenger i you pick up, you earn endi - starti + tipi dollars. You may only drive at most one passenger at a time.<br />
Given n and rides, return the maximum number of dollars you can earn by picking up the passengers optimally.<br />
Note: You may drop off a passenger and pick up a different passenger at the same point.<br />

**Input** : n = 5, rides = [[2,5,4],[1,5,1]] <br />
**Output** : 7 <br />
**Explanation** : We can pick up passenger 0 to earn 5 - 2 + 4 = 7 dollars. <br />

- Start from 0th index subarray till last index subarray
- Choose to pick the current subarray customer and recur for remaining. In this case add the price of picking the current customer and add it with recursion for remaining
- Choose to skip the current subarray customer and recur for remaining. In this no need to add the price of current customer.
- Result should be maximun of result of both the above recursions
- Use memo per startIdx to optimize

**Code** : 
````java
private long maxTaxiEarnings(int n, int[][] rides, int startIdx, long[] memo){
        if(startIdx >= rides.length){
            return 0;
        }
        if(memo[startIdx] > 0){
            return memo[startIdx];
        }
        int i = startIdx + 1;
        while(i < rides.length){
            if(rides[i][0] >= rides[startIdx][1]){
                break;
            }
            i++;
        }
        int currTip = rides[startIdx][1] - rides[startIdx][0] + rides[startIdx][2];
        memo[startIdx] = Math.max(maxTaxiEarnings(n, rides, startIdx+1, memo), 
                                    currTip + maxTaxiEarnings(n, rides, i, memo));
        return memo[startIdx];
    }
````
-------------
2) > Given an integer array nums, return true if you can partition the array into two subsets such that the sum of the elements in both subsets is equal or false otherwise.

**Input**: nums = [1,5,11,5] <br />
**Output**: true <br />
**Explanation**: The array can be partitioned as [1, 5, 5] and [11]. <br />

- Since we are looking for subsets with equal sum hence we need to look for subsets whose sum is equal to half of the sum of whole array.
- Check for total array sum to be even otherwise no solution exists.
- Now the problem is similar to findings subsets whose target sum is equal to half of the sum of whole array.
- Apply memoization on current index and currSum to optimize. memo[i][currSum]

**Code** :
````java
private boolean canPartition(int[] nums, int currSum, int i, Boolean[][] memo){

        if(currSum == 0){
            return true;
        }

        if(i >= nums.length || currSum < 0){
            return false;
        }

        if(memo[i][currSum] != null){
            return memo[i][currSum];
        }
        
        memo[i][currSum] = canPartition(nums, currSum - nums[i], i+1, memo) 
                    || canPartition(nums, currSum, i+1, memo);
        return memo[i][currSum];
    }
````
-------------------
3) > Given a rod of length n, we are asked to cut the rod and sell the pieces in a way that will maximize the profit. We are also given the price of every piece of length i where 1 <= i <= n.

**Lengths**: [1, 2, 3, 4, 5] <br />
**Prices**: [2, 6, 7, 10, 13] <br />
**Rod Length**: 5 <br />

- This problem can be divided in sub-problems by cutting the rod at each index and solving for remaining rod.
- In this case if you cut rod at 1 then remaining problem becomes prices[0] + cutRod(prices, n - i - 1)
- For each subproblem length of rod we need to start from 0th index to make sure we cover all sub-problems.
- At each step we need to keep updating the maximum

**Code** : 

````java
private static int rodCutting(int[] prices, int n) {
        
        // zero price of rod with length 0 or less
        if(n<=0) {
            return 0 ;
        }
        
        int maxPrice = Integer.MIN_VALUE;
        
        for(int i = 0;i<n;i++) {
            int currPrice = prices[i] + rodCutting(prices, n-i-1);
            maxPrice = Math.max(maxPrice, currPrice);
        }
        return maxPrice;
    }
````
------------
4) > Given a number array to represent different coin denominations and a total amount T, we need to find the minimum number of coins needed to make a change for T. We can assume an infinite supply of coins, therefore, each coin can be chosen multiple times.

**Input**: coins = [1,2,5], amount = 11 <br />
**Output**: 3 <br />
**Explanation**: 11 = 5 + 5 + 1 <br />

- Iterate through each coin starting from 0.
- For each coin we either include that coin in solution or we don't by recuring for each coice.
- Compute the solution for each recursion and them return the minimum of each of them.
- Handle the case where for invalide case result may be -1 and hence it may not give desired solution.

**Code** : 
````java
private int coinChange(int[] coins, int amount, int coin, int dp[][]){
        
        if(amount == 0){
            return 0;
        }

        if(coin >= coins.length || amount < 0){
            return Integer.MAX_VALUE;
        }
        
        if(dp[coin][amount] != -1){
            return dp[coin][amount];
        }

        int currCoinCount = Integer.MAX_VALUE;
        int usingCoin = coinChange(coins, amount - coins[coin], coin, dp);
        if(usingCoin != Integer.MAX_VALUE){
            currCoinCount = usingCoin + 1;
        }
        int notUsingCoint = coinChange(coins, amount, coin+1, dp);
        dp[coin][amount] =  Math.min(currCoinCount, notUsingCoint);
        return dp[coin][amount];
    }
````
---------
5) > Given an array of positive numbers, where each element represents the max number of jumps that can be made forward from that element, write a program to find the minimum number of jumps needed to reach the end of the array (starting from the first element). If an element is 0, then we cannot move through that element.

**Input**: {2,1,1,1,4} <br />
**Output** = 3 <br />
**Explanation**: Starting from index '0', we can reach the last index through: 0->2->3->4 <br />

- Starting from every position try to reach the end from current+1 to end.
- Here current+1 to end is the subproblem which we want to solve for.
- Add one if the result is not MAX_VALUE and keep track of min.
- If currentPosition goes beyond end position then return 0.
- Keep track on min for every starting position 1 to n.
- Use memoization to to avoid duplicate subproblems

**Code** : 
````java
private int minJumps(int[] nums, int i){

        if(i >= nums.length-1) {
            return 0;
        }

        int minWays = Integer.MAX_VALUE;
        for(int j = 1;j<=nums[i]; j++){
           int minJumps = minJumps(nums, i+j);
           if(minJumps != Integer.MAX_VALUE)
                minWays = Math.min(minJumps + 1, minWays);
        }
        return minWays;
    }
````
---------
6) > You are given an integer array cost where cost[i] is the cost of ith step on a staircase. Once you pay the cost, you can either climb one or two steps. <br /><br />

        You can either start from the step with index 0, or the step with index 1. <br /> <br />
        
        Return the minimum cost to reach the top of the floor.
        
        **Input**: cost = [10,15,20] <br />
        **Output**: 15 <br />
        **Explanation**: You will start at index 1.Pay 15 and climb two steps to reach the top.The total cost is 15. <br />
        
        - We can either start from 0th index or 1st index. So our start can be 0 or 1.
        - From start position we can either take 1 step or 2 step.
        - Get the min for both the above two choices.
        - Add the fee for current position in the min value calculated above.
        - If start position goes beyong total number of steps then result 1.
        - Use dp[currentPost] to keep track of duplicate problems.
        
        **Code** :
        ````java
        //Start from either 0th step or first step. Get min of that
        return Math.min(minCostClimbingStairs(cost, 0, dp), minCostClimbingStairs(cost, 1, dp));
        
        private int minCostClimbingStairs(int[] cost, int i, int[] dp){
        
                if(i >= cost.length){
                    return 0;
                }
        
                if(dp[i] != -1){
                    return dp[i] ;
                }
        
                // For each step add the cost of current step and recur for 1 step or 2 step from current step
                // Store the minimum cost of both the steps
                dp[i] = cost[i] + Math.min(minCostClimbingStairs(cost, i+1, dp), minCostClimbingStairs(cost, i+2, dp));
                return dp[i];
            }
        ````
-------
7) > Given a sequence, find the length of its Longest Palindromic Subsequence (LPS). In a palindromic subsequence, elements read the same backward and forward.