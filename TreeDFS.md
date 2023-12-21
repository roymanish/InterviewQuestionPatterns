# Tree DFS Patterns

1) > Given a binary tree and a number S, find if the tree has a path from root-to-leaf such that the sum of all the node values of that path equals S.

   ````js
   Input: root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22
   Output: true
   Explanation: The root-to-leaf path with the target sum is shown.
   ````
   ![](https://github.com/roymanish/InterviewQuestionPatterns/blob/main/images/pathsum1.jpg)

   - Start with root node recursively with currSum in each iteration. Starting with 0.
   - If root is null then return false.
   - Recursively traverse left and right subtree computing sum for left and right subtree.
   - Whenever we see a leaf node we should check if currSum matches targetSum then return true.
   - If any of the subtree result is true then sum exists and we should return true.
  
   ````java
   public boolean hasPathSum(TreeNode root, int targetSum, int currSum) {
        
        if(root == null){
            return false;
        }

        if(root.left == null && root.right == null){
            currSum = currSum + root.val;
            if(currSum == targetSum){
                return true;
            }
        }

        boolean lSum = hasPathSum(root.left, targetSum, currSum+root.val);
        boolean rSum = hasPathSum(root.right, targetSum, currSum+root.val);

        return lSum || rSum;
    }
   ````
   ------
2) > Given the root of a binary tree and an integer targetSum, return all root-to-leaf paths where the sum of the node values in the path equals targetSum. Each path should be returned as a list of the node values, not node references.
A root-to-leaf path is a path starting from the root and ending at any leaf node. A leaf is a node with no children.

   ````js
   Input: root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
   Output: [[5,4,11,2],[5,8,4,5]]
   Explanation: There are two paths whose sum equals targetSum:
   5 + 4 + 11 + 2 = 22
   5 + 8 + 4 + 5 = 22
   ````
   ![](https://github.com/roymanish/InterviewQuestionPatterns/blob/main/images/pathsumii1.jpg)

   - This is similar to previous question but in this case we need to keep searching for solutions by backtracking.
   - To backtract after recurring for left and right child remove the last element from the currPath
   - Start from root.
   - Add currentNode in currPath
   - Recur for left and right child with currSum+root.val and currPath
   - When we find leaf node then check for currSum == targetSum
   - If true then add the currPath in result.
  
   ````java
   public void pathSum(TreeNode root, int targetSum, int currSum, List<Integer> currPath) {
        
        if(root == null){
            return;
        }

        currPath.add(root.val);
        if(root.left == null && root.right == null){
            currSum = currSum + root.val;
            if(currSum == targetSum){
                List<Integer> tempList = new ArrayList<>(currPath);
                result.add(tempList);
            }
        }

        pathSum(root.left, targetSum, currSum+root.val, currPath);
        pathSum(root.right, targetSum, currSum+root.val, currPath);
        currPath.remove(currPath.size()-1);
    }
   ````
   ------
3) > A path in a binary tree is a sequence of nodes where each pair of adjacent nodes in the sequence has an edge connecting them. A node can only appear in the sequence at most once. Note that the path does not need to pass through the root.
The path sum of a path is the sum of the node's values in the path.
Given the root of a binary tree, return the maximum path sum of any non-empty path.

   ````js
   Input: root = [-10,9,20,null,null,15,7]
   Output: 42
   Explanation: The optimal path is 15 -> 20 -> 7 with a path sum of 15 + 20 + 7 = 42.
   ````
   ![](https://github.com/roymanish/InterviewQuestionPatterns/blob/main/images/exx2.jpg)

   - Starting from root traverse left subtree and right subtree.
   - Recursively calculate max path sum for left and right subtree.
   - Max path sum for any subtree in max of left, right and whole subtree.

   ````java
   public int maxPathSum(TreeNode root) {
        
        if(root == null){
            return 0;
        }

        int leftSubtreeSum = maxPathSum(root.left);
        int rightSubtreeSum = maxPathSum(root.right);

        int maxOfLeftRight = Math.max(leftSubtreeSum, rightSubtreeSum);
        int maxWithRoot = Math.max(maxOfLeftRight, root.val + maxOfLeftRight);

        int allMax = Math.max(maxWithRoot, leftSubtreeSum + rightSubtreeSum + root.val);
        return allMax;
    }
   ````
