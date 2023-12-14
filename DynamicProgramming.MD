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
