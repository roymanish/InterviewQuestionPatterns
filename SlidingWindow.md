# Sliding Window Patterns

1) > Given an array, find the average of all contiguous subarrays of size K in it.

   ````js
   Array: [1, 3, 2, 6, -1, 4, 1, 8, 2], K=5
   ````
   - Start two pointers from 0th index, windowStart and windowEnd
   - Keep incrementing windowEnd
   - Each step keep track of sum of elements in window.
   - Everytime we reach windowsize as K compute the average and set it to maxAvg if it is greater than previous average.
   - Move window left i.e substract element at windowStart from sum and increment windowStart.

   ````java
   public double findMaxAverage(int[] nums, int k) {
        
        int windowStart = 0, windowEnd = 0;
        double maxAvg = Integer.MIN_VALUE, sum = 0;

        while(windowEnd < nums.length){

            sum = sum + nums[windowEnd];

            if(windowEnd - windowStart + 1 == k){
                maxAvg = Math.max(maxAvg, sum/k);
                sum = sum - nums[windowStart];
                windowStart++;
            }

            windowEnd++;
        }
        return maxAvg;
    }
   ````
   -----
2) > You are visiting a farm that has a single row of fruit trees arranged from left to right. The trees are represented by an integer array fruits where fruits[i] is the type of fruit the ith tree produces. <br /><br />
     You want to collect as much fruit as possible. However, the owner has some strict rules that you must follow:<br /><br />
      - You only have two baskets, and each basket can only hold a single type of fruit. There is no limit on the amount of fruit each basket can hold.
      - Starting from any tree of your choice, you must pick exactly one fruit from every tree (including the start tree) while moving to the right. The picked fruits must fit in one of your baskets.
      - Once you reach a tree with fruit that cannot fit in your baskets, you must stop.
      - Given the integer array fruits, return the maximum number of fruits you can pick.
  
   ````js
   Input: fruits = [1,2,1]
   Output: 3
   Explanation: We can pick from all 3 trees.
   ````

   - Start two pointers from 0th index, windowStart and windowEnd
   - Keep expanding window to the right till we have found a fruit tree of 3rd type.
   - Maintain a map to keep track of number of fruits collected till now of each type
   - Once we reach invalid window contract the window from left till all fruits of first type i.e type at windowStart is removed from map
   - Once count of fruit type is zero then remove the key from map.
   - Update max variable if current windowsize is greater than max.

   **Code** :
   ````java
   public int totalFruit(int[] fruits) {
        Map<Integer, Integer> exists = new HashMap<>();

        int windowStart = 0, windowEnd = 0;
        int maxSize = Integer.MIN_VALUE;

        while(windowEnd < fruits.length){
          exists.put(fruits[windowEnd], exists.getOrDefault(fruits[windowEnd], 0)+1);

          while(exists.size()>2){

            exists.put(fruits[windowStart], exists.get(fruits[windowStart])-1);
            if(exists.get(fruits[windowStart]) == 0){
              exists.remove(fruits[windowStart]);
            }
            windowStart++;
          }
          maxSize = Math.max(maxSize, windowEnd - windowStart + 1);
          windowEnd++;
        }
        return maxSize;
    }
   ````
   -------
