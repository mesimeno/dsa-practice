# Problem Statement

Given the `root` of a binary tree, return *the level order traversal of its nodes' values* (i.e., from left to right, level by level).

# Examples
Input: root = [3,9,20,null,null,15,7]
Output: [[3],[9,20],[15,7]]

# Solution approach
I know that breadth first search (BFS) is a really important topic to master for any coding interview. This was my first BFS problem and I admittedly used some help to learn how to use queues and linked lists in Java.

I knew that BFS was the best approach for this problem since the problem requires us to keep track of the nodes by level-order traversal. In other words, keep track of the nodes along the tree’s breadth. 

I knew that the key difference between BFS and depth first search (DFS) is that the former uses a queue, while the latter uses a stack. This is where I first looked for some help. I now understand the basic queue functions in Java. Here’s a quick overview:

```java

 /* Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */


Queue<TreeNode> queue = new LinkedList<>(); // Notice that Queue is an interface
queue.offer(); // Equivalent to enqueue
queue.poll(); // Equivalent to dequeue
queue.peek(); // Returns a reference to the next-up element in the queue
queue.size(); // Returns the number of elements currently in the queue
queue.isEmpty(); // Returns true/false
```

So the BFS traversal is the easy part. After null-checking, enqueue the root. Then, enqueue its immediate neighbors. Dequeue the root and enqueue the next up neighbor. Continue. 

The curve ball is that we want to organize all of the nodes on the same level into the same list. With a bit of help, I noticed a pattern. Level 1 would only have a single node, the root. Level 1’s size would be 1. Level 2 could have a max of 2 nodes. Level 3 would have a max of 4 nodes. Level 4 would have a max of 8 nodes. This would continue with each level of the tree. Thus, to account for each node on the same level, you would need not only to traverse the tree, but the level as well.

# Working code
```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {

        // Interface List must be implemented by concrete class
        List<List<Integer>> res = new ArrayList<>();

        // Return empty list if root is empty
        if (root == null)
            return res;
        
        // Use a queue for breadth-first traversal
        Queue<TreeNode> queue = new LinkedList<>();
        // Since root is not null, enqueue it
        queue.offer(root);

        // Continue traversal until queue is empty
        while (!queue.isEmpty()) {
            // New list will store all nodes at this level
            List<Integer> currentLevel = new ArrayList<>();
            // Store number of elements at this level
            int levelSize = queue.size();
            
            // Traverse all nodes at this level
            for (int i = 0; i < levelSize; i++) {
                // Dequeue next node up
                TreeNode cur = queue.poll();
                // Collect its value
                currentLevel.add(cur.val);

                // Enqueue any available children
                if (cur.left != null)
                    queue.offer(cur.left);
                if (cur.right != null)
                   queue.offer(cur.right);
            }

            // Add level to result once traversed
            res.add(currentLevel);
        }
        
        return res;  
    }
}

```
