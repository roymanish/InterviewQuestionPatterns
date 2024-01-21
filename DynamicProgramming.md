# Dynamic Programming Patterns

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

        //If we pick passenger at startIdx then find the next passenger we can pick for recursion
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

        // For all rod lengths we should start from 0 as all rods can be cut in any size.
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

**Input**: s = "bbbab" <br />
**Output**: 4 <br />
**Explanation**: One possible longest palindromic subsequence is "bbbb". <br />

- We can start from both the sides as start and end.
- If both characters match we add 2 and then solve for subproblem of start+1 and end-1;
- If both characters do not match then we have two choices to solve for (start+1, end) or (start, end-1)
- Return the maximum of both the subproblems as result.
- If start > end then return 0.
- Store subproblem as dp[start][end]

````java
private int maxPalidromicSeq1(String s, int start, int end, int[][] dp) {
        if(start > end){
            return 0;
        }
        if(start == end){
            return 1;
        }
        if(dp[start][end] != 0){
            return dp[start][end];
        }
        //If the characters at both ends are same the increament the palidrome size by 2 and recur remaining characters
        if(s.charAt(start) == s.charAt(end)){
            dp[start][end] = 2 + maxPalidromicSeq1(s, start+1, end - 1, dp);
            return dp[start][end];
        }
        dp[start][end] = Math.max(maxPalidromicSeq1(s, start+1, end, dp), 
                                  maxPalidromicSeq1(s, start, end-1, dp));
        return dp[start][end];
    }
````
------------
8) > Given a string, find the length of its Longest Palindromic Substring (LPS). In a palindromic string, elements read the same backward and forward.

   **Input**: s = "babad" <br />
   **Output**: "bab" <br />
   **Explanation**: "aba" is also a valid answer. <br />

- For each character starting from index 0 recur (start = i, end = i) for odd length palindromes
- And recur for (start = i, end = i+1) for even length palindromes
- For each substring starting at start and ending at end expand till characters are matching or start and end are within bounds to find out if it is palindrome.
- Return length of palindrome as (end-1)-(start+1) + 1 = end - start - 1
- Return the maximum of both subproblems as result.

**Code** :
````java
String result = "";
        int max = Integer.MIN_VALUE;
        for(int i = 0;i<s.length()-1;i++){
            String str1 = isPalindrome(s, i, i);
            String str2 = isPalindrome(s, i, i+1);
            if(str1.length() > str2.length() && str1.length() > max){
                max = str1.length();
                result = str1;
            }
            else {
                result = str2;
            }
        }
        return result;
````
--------
9) > Given a string, find the minimum number of characters that we can remove/insert to make it a palindrome.

   - Find Longest palindromic subsequence and substract it from the length of string.
   - This will give correct result for minimum deletions required or minimum insertions required.
  
   **Code** :
   ````java
   return s.length() - longestPalindromicSubseq(s, 0, s.length()-1, dp);

    public int longestPalindromicSubseq(String s, int start, int end, int[][] dp){ 
        
        if(start > end){
            return 0;
        }
        if(start == end){
            return 1;
        }

        if(dp[start][end] != -1){
            return dp[start][end];
        }

        if(s.charAt(start) == s.charAt(end)){
            dp[start][end] = 2 + longestPalindromicSubseq(s, start+1, end-1, dp);
            return dp[start][end];
        }

        int includeStart = longestPalindromicSubseq(s, start, end-1, dp);
        int includeEnd = longestPalindromicSubseq(s, start + 1, end, dp);
        dp[start][end] = Math.max(includeStart, includeEnd);
        return dp[start][end];
    }
   ````
   ---------
10) > Given two strings str1 and str2, find the length of the longest subsequence which is common in both the strings.

    **Input**: text1 = "abcde", text2 = "ace" <br />
    **Output**: 3  <br />
    **Explanation**: The longest common subsequence is "ace" and its length is 3. <br />

    - Start from last index in both the string.
    - If the characters match then increment count by 1 and recur for remaining string
    - If the characters do not match then choose either the current index for first string or first character of 2nd string and recur for both.
    - Result will be max of both the recursion results.
    - Use memoization for optimization
    - If either of the index becomes less than 0 then return 0

    **Code** :
    ````java
    int longestCommonSubsequence(String S1, String S2, int i, int j, int[][] dp) {
        if(i<0 || j<0){
            return 0;
        }
        
        if(dp[i][j] != -1){
            return dp[i][j];
        }
        if(S1.charAt(i) == S2.charAt(j)){
            dp[i][j] = 1 + longestCommonSubsequence(S1, S2, i-1, j-1, dp);
        }
        else {
            dp[i][j] = Math.max(longestCommonSubsequence(S1, S2, i-1, j, dp), longestCommonSubsequence(S1, S2, i, j-1, dp));
        }
        return dp[i][j];
    }
    ````
    ------
11) > Given strings str1 and str2, we need to transform str1 into str2 by deleting and inserting characters. Write a function to calculate the count of the minimum number of deletion and insertion operations.

    ````js
    Input: str1 = "abc"
    str2 = "fbc"
    Output: 1 deletion and 1 insertion.
    Explanation: We need to delete {'a'} and insert {'f'} to str1 to transform it into str2.
    ````
    - Let’s assume length1 is the length of str1 and length2 is the length of str2.
    - Now let’s assume c1 is the length of LCS of the two strings str1 and str2.
    - To transform str1 into str2, we need to delete everything from str1 which is not part of LCS, so minimum deletions we need to perform from str1 => length1 - c1
    - Similarly, we need to insert everything in str1 which is present in str2 but not part of LCS, so minimum insertions we need to perform in str1 => length2 - c1

    **Code** :
    ````java
    int longestCommonSubsequence(String S1, String S2, int i, int j, int[][] dp) {
        if(i<0 || j<0){
            return 0;
        }
        
        if(dp[i][j] != -1){
            return dp[i][j];
        }
        if(S1.charAt(i) == S2.charAt(j)){
            dp[i][j] = 1 + longestCommonSubsequence(S1, S2, i-1, j-1, dp);
        }
        else {
            dp[i][j] = Math.max(longestCommonSubsequence(S1, S2, i-1, j, dp), longestCommonSubsequence(S1, S2, i, j-1, dp));
        }
        return dp[i][j];
    }
    ````
    ---------

12) > Given an integer array nums, return the length of the longest strictly increasing subsequence.

    ````js
    Input: nums = [10,9,2,5,3,7,101,18]
    Output: 4
    Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.
    ````

    - This problem can be solved by calculating the LIS of each subarray in a recursive way
    - For each index we can either choose it if it is greated then previous element or we can skip it. We recur for remaining elements
    - If we choose the current element then we need to add 1 in the result.
    - We need to track prevIdx to check if curr is greater than previous or not. Start prevIdx with -1 for base case.
    - Start with two pointers prevIdx = -1 and currIdx = 0
    - If prevIdx is less than currIdx then add one and compute LIS recursively for remaining number of elements.
    - If the condition is not true then skip the current element and compute for remaining.
    - Final result is max of result of both choices
    - If curr == nums.length then return 0.
    - Use dp[currIdx][[prev+1] to store reursion result and optimization.
   
    **Code** :
    ````java
    private int lengthOfLIS(int[] nums, int currIdx, int prevIdx, int[][] dp) {

        if(currIdx == nums.length){
            return 0;
        }

        if(dp[currIdx][prevIdx+1] != -1){
            return dp[currIdx][prevIdx+1];
        }
        int count1 = 0;
        if(prevIdx == -1 || nums[currIdx] > nums[prevIdx]){
            count1 = 1 + lengthOfLIS(nums, currIdx+1, currIdx, dp);
        }

        int count2 = lengthOfLIS(nums, currIdx+1, prevIdx, dp);
        
        dp[currIdx][prevIdx+1] = Math.max(count1, count2);
        return dp[currIdx][prevIdx+1];
    }
    ````
    ---------
13) > Given a sequence, find the length of its longest repeating subsequence (LRS). A repeating subsequence will be the one that appears at least twice in the original sequence and is not overlapping (i.e. none of the corresponding characters in the repeating subsequences have the same index).
    
    ````js
        Input: “t o m o r r o w”
        Output: 2
        Explanation: The longest repeating subsequence is “or” {tomorrow}.
    ````
    - Assume there are two strings of same value and find LCS where index of LCS not same in both the strings.
    - Similar to LCS problem, only in this case we need to make sure whenever char at two index match then index should not be same.
       
    **Code** :
    ````java
        int longestCommonSubsequence(String S1, String S2, int i, int j, int[][] dp) {
                if(i<0 || j<0){
                    return 0;
                }
                
                if(dp[i][j] != -1){
                    return dp[i][j];
                }
                //Additional check for non overlapping condition
                if(i!=j && S1.charAt(i) == S2.charAt(j)){
                    dp[i][j] = 1 + longestCommonSubsequence(S1, S2, i-1, j-1, dp);
                }
                else {
                    dp[i][j] = Math.max(longestCommonSubsequence(S1, S2, i-1, j, dp), longestCommonSubsequence(S1, S2, i, j-1, dp));
                }
                return dp[i][j];
        }
    ````
        
    -----
15) > Given a string and a pattern, write a method to count the number of times the pattern appears in the string as a subsequence.

    ````js
    Input: string: “baxmx”, pattern: “ax”
    Output: 2
    Explanation: {baxmx, baxmx}.
    ````

    - Start with 0th index of both substrings.
    - If both chars match then recursively check for rest of the substring in both the strings. <br/>
              - We will not add 1 to the recursion as we are not calculating the length of result but the count of result. We only need to return 1 when result if found.
    - If characters do not match then only move the first string to next character and recur for next char of first string and 0th of 2nd string.
    - Final result is sum of the result of both the recursions
    - If 2nd string is finished before first i.e str2Idx == str2.length() then return 1 as we have found on occurence of str2 in str1.
    - If str1 finishes before then return 0 as no result was found.

    **Code** :
    ````java
    if(str2Idx == S2.length()){
            return 1;
        }
        
        if(str1Idx == S1.length()) {
            return 0;
        }
        
        int count1 = 0;
        if(S1.charAt(str1Idx) == S2.charAt(str2Idx)){
            count1 = countWays(S1, S2, str1Idx+1, str2Idx+1);
        }
        int count2 = countWays(S1, S2, str1Idx+1, str2Idx);
        return count1+count2;
    ````
    ------
16) > Given a number sequence, find the length of its Longest Bitonic Subsequence (LBS). A subsequence is considered bitonic if it is monotonically increasing and then monotonically decreasing.

    ````js
    Input: {4,2,3,6,10,1,12}
    Output: 5
    Explanation: The LBS is {2,3,6,10,1}.
    ````

    - Iterate from 0th to end in for loop.
    - For each index compute the Longest Descreasing Subsequence(LDS) to the left and to the right.
    - The final result will be max sum of LDS from left and right for all indexes.
   
    **Code** :
    ````java
    public int LongestBitonicSequence(int[] nums)
    {
        
        
        int maxLen = Integer.MIN_VALUE;
        for(int i = 0;i<nums.length;i++){
            
            maxLen = Math.max(maxLen, findLDS(nums, -1, i)+findLDSReverse(nums, -1, i)-1);
        }
        return maxLen;
    }
    
    int findLDS(int[] nums, int prev, int curr){
        
        if(curr == nums.length){
            return 0;
        }
        
        int countWithCurr = 0;
        if(prev == -1 || nums[curr] < nums[prev]){
            countWithCurr = 1 + findLDS(nums, curr, curr+1);
        }
        
        int countWithoutCurr = findLDS(nums, prev, curr+1);
        return Math.max(countWithCurr, countWithoutCurr);
    }
    
    int findLDSReverse(int[] nums, int prev, int curr){
        
        if(curr < 0){
            return 0;
        }
        
        int countWithCurr = 0;
        if(prev == -1 || nums[curr] < nums[prev]){
            countWithCurr = 1 + findLDSReverse(nums, curr, curr-1);
        }
        
        int countWithoutCurr = findLDSReverse(nums, prev, curr-1);
        return Math.max(countWithCurr, countWithoutCurr);
    }
    ````
    ------
17) > Given strings s1 and s2, we need to transform s1 into s2 by deleting, inserting, or replacing characters. Write a function to calculate the count of the minimum number of edit operations.

    ````js
    Input: s1 = "bat"
    s2 = "but"
    Output: 1
    Explanation: We just need to replace 'a' with 'u' to transform s1 to s2.
    ````

    - Start with 0th index of both the strings.
    - If the chars at current index match then no modification is required. We can just increment both the indexes
    - If they do not match then there are three options :
      - Insert a character : In this case we will only increment the 2nd index as inserted character will always match.
      - Delete a character : In this case we will move index1 to the next character to match.
      - Update a character : In this case we will move both the indexes as both chars will match after update.
    - Final result in minimum of result of above 3 recursions.
    - If either of the indexes reach limit then return the remaining index of other string

    **Code** :
    ````java
    int len1 = word1.length();
        int len2 = word2.length();

        if(len1 == 0) {
            return len2;
        }

        if(len2 == 0){
            return len1;
        }

        String key = word1+"-"+word2;
        if(memo.get(key) != null){
            return memo.get(key);
        }

        if(word1.charAt(len1-1) == word2.charAt(len2-1)){
            memo.put(key, minDistance(word1.substring(0,len1-1), word2.substring(0,len2-1), memo));
        }
        else{
            memo.put(key, 1 + Math.min(minDistance(word1.substring(0,len1), word2.substring(0,len2-1), memo),
                            Math.min(minDistance(word1.substring(0,len1-1), word2.substring(0,len2), memo),
                                     minDistance(word1.substring(0,len1-1), word2.substring(0,len2-1), memo))));
        }
        return memo.get(key);
    ````
    ------
18) > Given three strings m, n, and p, write a method to find out if p has been formed by interleaving m and n. p would be considered interleaving m and n if it contains all the letters from m and n and the order of letters is preserved too.

    ````js
    Input: m="abd", n="cef", p="abcdef"
    Output: true
    Explanation: 'p' contains all the letters from 'm' and 'n' and preserves their order too. 
    ````
    - Start 3 pointers from start of each string
    - If currIdx of str1 matches with currIdx of str3 then recur for remaining chars of both the string by incrementing both the indexes
    - If currIdx of str2 matches with currIdx of str3 then recur for remaining chars of both the string by incrementing both the indexes
    - Final result if || of above two recursion results.
    - If length of s3 is not equal to s1+s2 then return false.
    - If at any point all 3 indexes have reached their limit then return true as we have successfully traversed all chars of all strings
    - If at any point 3rd index reaches its limit then return false because there may be remaining chars in s1 and s2.
   
    **Code** :
    ````java
    private boolean isInterleave(String s1, String s2, String s3, int i, int j, int k, Map<String, Boolean> dp){

        if(k == s3.length() && i == s1.length() && j == s2.length()){
            return true;
        }

        if(k == s3.length()) {
            return false;
        }

        String key = i+"-"+j+"-"+k;
        if(dp.get(key) != null){
            return dp.get(key);
        }

        boolean result1 = false;
        if(i < s1.length() && s1.charAt(i) == s3.charAt(k)){
            result1 = isInterleave(s1, s2, s3, i+1, j, k+1, dp);
        }

        boolean result2 = false;
        if(j < s2.length() &&  k < s3.length()  && s2.charAt(j) == s3.charAt(k)){
            result2 = isInterleave(s1, s2, s3, i, j+1, k+1, dp);
        }

        dp.put(key, result1 || result2);
        return dp.get(key);
    }
    ````
    --------
19) > You are given an integer array cost where cost[i] is the cost of ith step on a staircase. Once you pay the cost, you can either climb one or two steps.
You can either start from the step with index 0, or the step with index 1.
Return the minimum cost to reach the top of the floor.

    ````js
    Input: cost = [10,15,20]
    Output: 15
    Explanation: You will start at index 1.
        - Pay 15 and climb two steps to reach the top.
        The total cost is 15.
    ````

    - Start either from 0th step or 1st step and take min of that.
    - Add the cost of current stair and recur for i+1 (1 step) and i+2(2 step).
    - Get minimum of that and add it to the cost of current stair cost.
    - If i is greater than total number of stairs then return 0.

    ````java
    public int minCostClimbingStairs(int[] cost) {
        
        int[] dp = new int[cost.length];
        Arrays.fill(dp, -1);

        //Start from either 0th step or first step. Get min of that
        return Math.min(minCostClimbingStairs(cost, 0, dp), minCostClimbingStairs(cost, 1, dp));
    }

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
    ------
20) > Given an array of integers nums and an integer k, return the total number of subarrays whose sum equals to k.
A subarray is a contiguous non-empty sequence of elements within an array.

    ````js
    Input: nums = [1,2,3], k = 3
    Output: 2
    ````

    - Start from 0th index and keep track of running sum.
    - For each sum store the value in hashmap if it does not exist or increase the counter if already exists.
    - Check in hashmap if sum - k already exists then there exists a subarray with sum k hence increate result count by 1.

    ````java
    public int subarraySum(int[] nums, int k) {
        
        int count = 0, sum = 0;
        HashMap < Integer, Integer > map = new HashMap < > ();
        map.put(0, 1);
      
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            if (map.containsKey(sum - k))
                count += map.get(sum - k);
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        return count;
    }
    ````
    -------
20) > Two Similar Problems with little tweek.
    > 1) https://leetcode.com/problems/combinations/description/
    > 2) https://leetcode.com/problems/permutations/

    ````js
    - In the first problem where we have to find combination of elements from a array of size(n) for a fixed number of elements(k)
    - Lets say k=2 we have to iterate through each element and add it to the result.
    - Once the size of result is equal to k then add the result to final result.
    - Once we have chosen 1 element(i) we only have to recur for remaining elements(i+1)
    - After recuring to make sure we take all combinations we have to remove the chosen element from the result
    ````

    **Code** :
    ````java
    private void combine(int n, int k, List<Integer> currResult, int start) {
        if(currResult.size() == k){
            result.add(new ArrayList<>(currResult));
            return;
        }

        //Always start from next element
        for(int i = start; i<=n; i++){
            currResult.add(i);
            combine(n, k, currResult, i+1);
            currResult.remove(currResult.size() - 1);
        }
    }
    ````

    ````js
    - In the 2nd problem we have to find all permutation of all elements so here k = size of whole array.
    - Since here we have to find all permutation hence we have to always iterate from the 0th element and skip all ready added element in result.
    - We add the current element in result and recur for remain elements apart from element already added.
    - Once size of result is equal to array size we add the result for final result.
    - Other then that both the problems are same.
    ````

    **Code** :
    ````java
    private void permute(int[] nums, List<Integer> currResult) {

        if(currResult.size() == nums.length){
            System.out.println(currResult);
            result.add(new ArrayList<>(currResult));
            return;
        }

        // For each recursion always start from 0th element
        for(int i = 0;i<nums.length;i++){
            // Check to avoid already selected element
            if(!currResult.contains(nums[i])){
                currResult.add(nums[i]);
                permute(nums, currResult);
                currResult.remove(currResult.size()-1);
            }
        }
    }
    ````
21) > Prefix Sum for subarray problems. </br>
      Given an array of integers nums and an integer k, return the total number of subarrays whose sum equals to k.</br>
      A subarray is a contiguous non-empty sequence of elements within an array.

    ````js
    Example 1:
    Input: nums = [1,1,1], k = 2
    Output: 2
    
    Example 2:
    Input: nums = [1,2,3], k = 3
    Output: 2
    ````

    ````js
    - Most findings subarray(s) with given sum/average related problems can be solved by maintaining a prefixSum map/array.
    - A prefixSum is running sum upto a particular element.
    - For example if we have arr = [1,1,1,1] and we need to search subarrays with sum = 2.
    - We can maintain a map with [key, value] = [sum till ith element, count].
    - To find sum of elements between index i and j where j > i we can subtract the sum till i from sum till j.
    - In the above example if we want sum of elements i = 1 till j = 2 then we can subtract the sum till i = 1 from sum till j=2.
    - Applying this logic to the problem mentioned above we can look for subarray that can be removed from current sum to get the required sum.
    - So for reuired sum = 2 the sum till j = 2 is 3 and sum till i = 1 is 1.
    - Here we can see that if we sum of elements till i=1 from sum till j=2 then we get 2 which is the required sum.
    ````
    **Code** :
    ````java
    public int subarraySum(int[] nums, int k) {
        
        Map<Integer, Integer> prefixSumMap = new HashMap<>();
        prefixSumMap.put(0,1);

        int count = 0;
        int sum = 0;
        for(int i=0;i<nums.length;i++){
            sum = sum + nums[i];

            //Given current sum this like check if there is a prefix sum that can be removed to get required sum subarray.
            // Example if array is [1,1,1,1] , k = 2
            // If we are element 3 then currSum = 3
            // Here is we remove prefix sum till element 1 which is 1 then we will get k which is the sum of element 2 and 3
            // To get the sum of middle elements we just substract the sum of previous elements from totalsum till here.
            if(prefixSumMap.containsKey(sum-k)){
                count += prefixSumMap.get(sum-k);
            }
            prefixSumMap.put(sum, prefixSumMap.getOrDefault(sum, 0)+1);
        }
        return count;
    }
    ````

