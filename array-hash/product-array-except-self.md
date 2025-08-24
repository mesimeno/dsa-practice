# Problem Statement
Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all the elements of `nums` *except* `nums[i]`.

The product of any prefix or suffix of `nums` is **guaranteed** to fit in a **32-bit** integer.

You must write an algorithm that runs in `O(n)` time and without using the division operation

# Examples
```text
Input: nums = [1,2,3,4]
Output: [24,12,8,6]
```

```text
Input: nums = [-1,1,0,-3,3]
Output: [0,0,9,0,0]
```

# Solution approach
Despite the directions, I solved this problem using division. I will revisit it later to solve it using prefix/suffix sum, which avoids the division operation.

There are three cases to consider in this problem.

The most trivial case involves an array `nums` that contains no zeros. This simply involves first traversing `nums` to determine  `product` and traversing it again to fill the `res` array where `res[i] = product / nums[i]`

Another case is when `nums` contains one zero. Let `nums[k] = 0`. Then we must calculate `product` for all elements in `nums` other than `nums[k]`. Then in the `res` array, we simply let `res[k] = product` and return the array, which is prefilled with `0`s in Java by default.

The final case is when `nums` contains more than one zero. This guarantees that `res` is an array of all `0`s.

# Working Code
```java
class Solution {

	public int[] productExceptSelf (int[] nums) {
	
		// Store length of the input array
		int len = nums.length;
		
		// Create a solution array of that same size
		int[] res = new int[len];
		
		int product = 1; // Track product as you traverse the array
		int numZeros = 0; // Flag all zeros
		int zeroIndex = 0; // Store the index of a potential zero
		
		// Traverse the array to determine the product
		for (int i = 0; i < len; i++) {
			
			int val = nums[i];
			
			// Flag a zero if encountered
			if (val == 0) {
				numZeros++;
				zeroIndex = i;
			}
				
			// Return array of all 0s if more than one zero has been encountered
			if (numZeros > 1)
				return res;
				
			// Otherwise, continue building up the product
			if (val != 0)
				product *= val;
		}
		
		// If no zero was encountered, trivially populate solution array
		if (numZeros == 0)
			for (int i = 0; i < len; i++)
				res[i] = product / nums[i];
				
		// If one zero was encountered at index zeroIndex, fill only res[zeroIndex]
		else
			res[zeroIndex] = product;
			
		return res; 
	}

}
```
