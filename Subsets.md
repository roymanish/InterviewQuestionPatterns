# InterviewQuestionPatterns

Finding all the subsets or patterns of a given set of numbers

1) > Find all subsets with one or more elements from a given array
   
   Input - [1,5,3] <br />
   Output - [[], [1], [5], [1,5], [3], [1,3], [5,3], [1,5,3]]

   - Start from 0th index and recur by choosing and unchoosing each item.
   - Must recur after unchoosing as well because current element may or may not be present in the final output.

   Code : 
   ````java
   private void subsets(int[] nums, int n, List<Integer> list, List<List<Integer>> res){
           if(n==nums.length){
               List<Integer> temp = new ArrayList<>(list);
               res.add(temp);
               return;
           }
           list.add(nums[n]);
           subsets(nums, n+1, list, res);
           list.remove(list.size()-1);
           subsets(nums, n+1, list, res);
       }
   ````
   -----------------------------------------------------------------------------------------------
   
2) > Given a set of numbers that might contain duplicates, find all of its distinct subsets.

   Input - [1, 5, 3, 3] <br />
   Output - [[], [1], [3], [1,3], [3,3], [1,3,3]]

   -----------------------------------------------------------------------------------------------

3) > Given a set of distinct numbers, find all of its permutations.

   Input - [1,2,3] <br />
   Output - [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]

   - Iterate over the array from 0 to n.
   - Choose one item at a time and recur.
   - Each recursion avoid adding same item again.
   - In this case there is no need to recur after unchoosing because final solution must contain all elements in the input array.

   Code : 
   ````java
   private void permute(int[] nums, List<Integer> currResult) {

        if(currResult.size() == nums.length){
            System.out.println(currResult);
            result.add(new ArrayList<>(currResult));
            return;
        }
        
        for(int i = 0;i<nums.length;i++){

            if(!currResult.contains(nums[i])){
                currResult.add(nums[i]);
                permute(nums, currResult);
                currResult.remove(currResult.size()-1);
            }
        }
    }
   ````

   -----------------------------------------------------------------------------------------------

4) > Given a string, find all of its permutations preserving the character sequence but changing case.

   Input - "a1b2" <br />
   Output: ["a1b2","a1B2","A1b2","A1B2"]

   - Add input in the list of results
   - Iterate over each item in input.
   - Add all combinations of each character to all results collected in previous step.
   - Processing the first character a, we will get two permutations: "ab7c", "Ab7c"
   - Processing the second character b, we will get four permutations: "ab7c", "Ab7c", "aB7c", "AB7c"
   - Since the third character is a digit, we can skip it.
   - Processing the fourth character c, we will get a total of eight permutations: "ab7c", "Ab7c", "aB7c", "AB7c", "ab7C", "Ab7C", "aB7C", "AB7C"
  
   Code :
   ````java
   private List<String> letterCasePermutation1(String s) {

        List<String> permutations = new ArrayList<>();
        permutations.add(s);

        for(int i = 0; i<s.length();i++){

            char c = s.charAt(i);
            if(Character.isLetter(c)){

                int size = permutations.size();
                for(int j = 0;j<size; j++){
                    char[] chars = permutations.get(j).toCharArray();

                    if(Character.isUpperCase(chars[i])){
                        chars[i] = Character.toLowerCase(chars[i]);
                    }
                    else {
                        chars[i] = Character.toUpperCase(chars[i]);
                    }
                    permutations.add(new String(chars));
                }
            }
        }
        return permutations;
    }
   ````

   -----------------------------------------------------------------------------------------------
  
5) > Given an expression containing digits and operations (+, -, *), find all possible ways in which the expression can be evaluated by grouping the numbers and operators using parentheses.

   Input: expression = "2-1-1" <br />
   Output: [0,2]

   - Iterate over the array from 0 to n.
   - Choose one item at a time.
   - If the item is an operator then split the array into leftPart and rightPart and recur for each part.
   - Collect the results from each recursion as lefts and rights.
   - Apply all results with the operator in a nested loop.
  
   Code :
   ````java
   private List<Integer> diffWaysToCompute(String expression, int start, int end){

        List<Integer> result = new ArrayList<>();
        if(start > end){
            return result;
        }

        if(end-start <= 2){
            result.add(Integer.parseInt(expression.substring(start, end)));
            return result;
        }

        for(int i=start;i<end;i++){

            char c = expression.charAt(i);
            if(c == '-' || c == '+' || c == '*'){
                List<Integer> lefts = diffWaysToCompute(expression, start, i);
                List<Integer> rights = diffWaysToCompute(expression, i+1, end);
                
                for(Integer left : lefts) {
                    for(Integer right: rights) {
                        
                        switch(c){
                        case '-':
                            result.add(left-right);
                            break;
                        case '+':
                            result.add(left+right);
                            break;
                        case '*':
                            result.add(left*right);
                            break;
                        }
                    }
                }
            }
        }
        return result;
    }
   ````

   -----------------------------------------------------------------------------------------------

6) > Given a number n, write a function to return all structurally unique Binary Search Trees (BST) that can store values 1 to n?

   This can be same way as mentioned in point number 5. By splitting left and right half. Solving for them separately. And then combining at the results.

   -----------------------------------------------------------------------------------------------
