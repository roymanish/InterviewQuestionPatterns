# InterviewQuestionPatterns
## Subsets
Finding all the subsets or patterns of a given set of numbers

1) > Find all subsets with one or more elements from a given array
   Ex - [1,5,3]
   Ans - [[], [1], [5], [1,5], [3], [1,3], [5,3], [1,5,3]]

   - Start from 0th index and recur by choose and unchoosing each item.
   - Must recur after unchoosing as well because current element may or may not be present in the final output.
  
`private void subsets(int[] nums, int n, List<Integer> list, List<List<Integer>> res){
        if(n==nums.length){
            List<Integer> temp = new ArrayList<>(list);
            res.add(temp);
            return;
        }
        list.add(nums[n]);
        subsets(nums, n+1, list, res);
        list.remove(list.size()-1);
        subsets(nums, n+1, list, res);
    }`
2) 
