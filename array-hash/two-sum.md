# Problem Statement
Given an array of integers `nums` and an integer `target`, return *indices of the two numbers such that they add up to `target`*.

You may assume that each input would have ***exactly* one solution**, and you may not use the *same* element twice.

You can return the answer in any order.

# Examples
```text
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
```
# Solution Approach
This is a class LeetCode problem. In fact, it’s the first one!

The brute force solution is very straightforward. It’s simply a nested loop. Matter of fact, I’ll code this up for you in 30 seconds:
```java
for (int i = 0; i < nums.length; i++) {
	for (int j = 0; j < nums.length; j++) {
		if (i != j) {
			if (nums[i] + nums[j] == target)
				return new int[]{i, j};
		}
	}
}
```

The above code makes this brute force approach run in $O(n^2)$. A much better runtime can be accomplished when approaching this with a HashMap.

This involves understanding the nature of the problem. We are guaranteed a solution in the problem statement. We can also make use of the fact that this deals with addition, which is commutative. This leads to the observation that it is redundant for us to check `nums[i] + nums[j] = sum` *and* `nums[j] + nums[i] = sum`, which we do if we go with the brute force approach.

So the optimization lies in avoiding this redundancy. This redundancy only arises because of the nature of using a nested loop. So, let’s use a single loop to traverse through our `nums` input array! First, however, we need to create a HashSet that stores the values of the previously visited elements, as well as their corresponding index, since this is what the problem asks us to return.

The `target` remains constant throughout the problem. What also remains ‘constant’ are the elements that we have already visited and have accounted for. Thus, the only thing that ‘varies’ is the current element that introduces an unseen element. This unseen element will either form part of the solution, by having it add successfully with a visited element to equal the target (which we’ll determine with subtraction), or we’ll add it as a potential contender within our hash map.

That is the best way I can spit this out, but it is much more intuitive in the code. I’ll present it first in Java, then in Python, which I will begin practicing with each coding problem to gain proficiency.

# Working Code
```java
class Solution {

	public int[] twoSum(int[] nums, int target) {
		
		// Simple base case I included since an answer is guaranteed
		if (nums.length == 2) 
			return new int[]{0, 1};
			
		// Initialize the hash map: some_number -> index
		HashMap<Integer, Integer> map = new HashMap<>();
		
		// Loop to iterate through each element
		for (int i = 0; i < nums.length; i++) {
			
			// Store the addend needed to make this element part of the solution
			int addend = target - nums[i];
			
			// If this addend exists/already visited, then we have a solution!
			if (map.containsKey(addend)) 
				return new int[]{i, map.get(addend)};
				
			// If addend DNE, add current element as a potential future addend
			map.put(nums[i], i);
		}
		
		// Note that an answer is guaranteed so this will never be reached
		return null;
	}

}
```

```python
class Solution(object):

	def twoSum(self, nums, target):
		# Simple base case I included since an answer is guaranteed
		if len(nums) == 2:
			return [0, 1]
			
		# Initialize the hash map
		map = {}
		
		# Iterate through each element in nums
		# Note: using enumeration syntax (see explanation)
		for i, value in enumerate(nums):
			# Find potential addend that would make current element part of solution
			addend = target - value
			# Check if potential addend has been visited
			if addend in map:
				# If so, retrieve its associated index and return as part of int array
				return [i, map[addend]]
			
			# If not, then store current element for potential future use
			# DO NOT DO map[addend] = i
			map[value] = i
			
		return None	
```
```enumerate(some_array)``` in Python allows you to have a reference to both the current index and its associated value. In this case, we have a reference to both ```i``` and ```nums[i]```
