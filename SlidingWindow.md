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
   -----
3) > You are given a string s and an integer k. You can choose any character of the string and change it to any other uppercase English character. You can perform this operation at most k times.
   *Return the length of the longest substring containing the same letter you can get after performing the above operations.*
   
   ````js
   Input: s = "ABAB", k = 2
   Output: 4
   Explanation: Replace the two 'A's with two 'B's or vice versa.
   ````
   - For any string number of modifications required to make all chars same is (number of chars - mostFrequentChar)
   - Start from 0th index with windowStart and windowEnd
   - Keep track of mostFreqChar at each step
   - Expand till windowSize - mostFreqCharCount is <= K.
   - Contract window by incrementing windowStart and reducing the freq from counterMap. Contract till condition becomes valid again.
   - At each step keep track of max window size.
  
   **Code** :
   ````java
   public int characterReplacement(String s, int k) {
        
        int windowStart = 0, windowEnd = 0, maxLen = Integer.MIN_VALUE, mostFrqCharCount = Integer.MIN_VALUE;
        char[] freq = new char[128];
        while(windowEnd < s.length()) {

            freq[s.charAt(windowEnd)]++;
            mostFrqCharCount = Math.max(mostFrqCharCount, freq[s.charAt(windowEnd)]);
            int currWindowSize = windowEnd - windowStart + 1;

            while(currWindowSize - mostFrqCharCount > k){
                freq[s.charAt(windowStart)]--;
                windowStart++;
                currWindowSize = windowEnd - windowStart + 1;
            }
            maxLen = Math.max(maxLen, currWindowSize);
            windowEnd++;
        }
        return maxLen;
    }
   ````
   ------
4) > Given a binary array nums and an integer k, return the maximum number of consecutive 1's in the array if you can flip at most k 0's.

   ````js
   Input: nums = [1,1,1,0,0,0,1,1,1,1,0], k = 2
   Output: 6
   Explanation: [1,1,1,0,0,1,1,1,1,1,1]
   Bolded numbers were flipped from 0 to 1. The longest subarray is underlined.
   ````
   - Same approach as question 3 above.
   - Only in this case instead of most frequent char we need to track number of 0s.
   - When 0 counts goes beyong K window is invalid and we need to contract window till it is valid again.
  
   ````java
   public int longestOnes(int[] nums, int k) {
        
        int count0 = 0;

        int winStart = 0, winEnd = 0;
        int maxWin = Integer.MIN_VALUE;

        while(winEnd < nums.length){

            if(nums[winEnd] == 0){
                count0++;
            }

            while(count0 > k){
                if(nums[winStart] == 0){
                    count0--;
                }
                winStart++;
            }
            winEnd++;
            maxWin = Math.max(maxWin, winEnd - winStart);
        }
        return maxWin;
    }
   ````
   -----
5) > Given two strings s and t of lengths m and n respectively, return the minimum window substring
   of s such that every character in t (including duplicates) is included in the window. If there is no such substring, return the empty string "".

   ````js
   Input: s = "ADOBECODEBANC", t = "ABC"
   Output: "BANC"
   Explanation: The minimum window substring "BANC" includes 'A', 'B', and 'C' from string t.
   ````
   - Start window from 0th index on s.
   - Keep expanding till the window in invalid i.e window does not contain all chars from t.
   - When we get a valid window keep contracting till the window becomes invalid again. Keep track of minWindowSize at each step.
   - After traversion all chars try to contract window again outside the window loop to get the minimum possible window. This is required to handle scenario where minWindow ends at the last character.

   **Code** :
   ````java
   public String minWindow(String s, String t) {
        
        if(s.length() < t.length()) {
            return "";
        }

        int windowStart = 0, windowEnd = 0, minWindow = Integer.MAX_VALUE;

        while(windowEnd < s.length()){

            while(isValidWindow(s.substring(windowStart, windowEnd), t)){
                
                minWindow = Math.min(minWindow, windowEnd - windowStart + 1);
                windowStart++;
            }
            windowEnd++;
        }

        while(isValidWindow(s.substring(windowStart, windowEnd), t)){

            minWindow = Math.min(minWindow, windowEnd - windowStart + 1);
            windowStart++;
        }
        
        return minWindow < Integer.MAX_VALUE ? s.substring(windowEnd - minWindow + 1, windowEnd) : "";
    }

    private boolean isValidWindow(String s1, String s2){


        if(s1.length() < s2.length()){
            return false;
        }

        int[] chars = new int[52];

        for(char c : s1.toCharArray()){
            chars[c - 'A']++;
        }

        for(char c : s2.toCharArray()){
            chars[c - 'A']--;
        }

        for(char c : s2.toCharArray()){
            if(chars[c - 'A'] < 0){
                return false;
            }
        }
        return true;
    }
   ````
   -----
6) > You are given a string s and an array of strings words. All the strings of words are of the same length.
   > A concatenated substring in s is a substring that contains all the strings of any permutation of words concatenated.
   > For example, if words = ["ab","cd","ef"], then "abcdef", "abefcd", "cdabef", "cdefab", "efabcd", and "efcdab" are all concatenated strings. "acdbef" is not a concatenated substring because it is not the concatenation of any permutation of words.
   > Return the starting indices of all the concatenated substrings in s. You can return the answer in any order.

   ````js
   Input: s = "barfoothefoobarman", words = ["foo","bar"]
   Output: [0,9]
   Explanation: Since words.length == 2 and words[i].length == 3, the concatenated substring has to be of length 6.
   The substring starting at 0 is "barfoo". It is the concatenation of ["bar","foo"] which is a permutation of words.
   The substring starting at 9 is "foobar". It is the concatenation of ["foo","bar"] which is a permutation of words.
   The output order does not matter. Returning [9,0] is fine too.
   ````

   - Since we need to find all words in words array concatenated together(say p) in any perputation in s. Hence this can be converted in finding any permutation of p in s.
   - Our window size will be totalNumberOfWords in words array multiplied by size of any word.
   - Once we have window size we can start the window with this size on s and check if all chars of p exists in currentwindow of s or not.
   - If it exists then we store the windowStart for current window in results.
   - Keep moving the window one character at a time.
   - To check if current window has all chars of p or not use counter mapor char array of p and window substring.
  
   **Code** :
   ````java
   public List<Integer> findSubstring(String s, String[] words) {
        
        int s_len = s.length();
        int words_len = words.length;
        int w_len = words[0].length();
        Map<String, Integer> cntrMap = new HashMap<>();

        for(String word : words){
            cntrMap.put(word, cntrMap.getOrDefault(word, 0) + 1);
        }

        List<Integer> result = new ArrayList<>();
        int windowSize = words_len*w_len;
        int windowStart = 0, windowEnd = windowStart + windowSize;
        while(windowEnd <= s_len){

            if(isValidWindow(s.substring(windowStart, windowEnd), w_len, cntrMap)){
                result.add(windowStart);
            }
            windowEnd++;
            windowStart++;
        }
        return result;
    }

    private boolean isValidWindow(String s, int w_len, Map<String, Integer> contrMap1) {

        Map<String, Integer> contrMap2 = new HashMap<>();

        int windowStart = 0, windowEnd = windowStart + w_len;

        while(windowEnd <= s.length()){
            String word = s.substring(windowStart, windowEnd);
            contrMap2.put(word, contrMap2.getOrDefault(word, 0) + 1);
            windowEnd = windowEnd + w_len;
            windowStart = windowStart + w_len;
        }
        return contrMap1.equals(contrMap2);
    }
   ````
