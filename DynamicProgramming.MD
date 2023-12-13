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
