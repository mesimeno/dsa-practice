# Problem Statement

Given an unsorted array of integers `nums`, return *the length of the longest consecutive elements sequence.*

You must write an algorithm that runs in `O(n)` time.

# Examples
```text
Input: nums = [100,4,200,1,3,2]
Output: 4
Explanation: The longest consecutive elements sequence is[1, 2, 3, 4]. Therefore its length is 4.
```

```text
Input: nums = [1,0,1,2]
Output: 3
```

# Solution Approach
As usual, this was a fun one! Working with arrays, I’ve learned that hash sets and hash maps are very powerful tools to decide whether a “missing piece” of sorts is missing. For example, in the classic Two Sum problem, the missing piece is the number you need to reach the target sum. By storing possible complements in the hash set, you can efficiently and elegantly check whether that complement already exists.

In this case, we can use this to our advantage by observing that a consecutive sequence of numbers is a sequence separated by $1$. My initial approach was to add all elements to a hash set. Then, we traverse the `nums` array again and for any element `i` explore how many elements extended a sequence the left of it (by repeatedly checking if `set.contains(i-1)`) and how many extended a sequence to the right of it (this time repeatedly checking if `set.contains(i+1)`). 

Elements that are already part of an explored sequence are removed from the hash set to avoid redundant calculations.

This approach works perfectly! But it can be made more elegant by exploring a sequence only from the starting element. We know that some element `i` is the starting element of a sequence if `sequence.contains(i-1) == false`. This avoids unnecessary work!

# Working Code
Initial approach
```java
class Solution {
    public int longestConsecutive(int[] nums) {

        HashSet<Integer> set = new HashSet<>();

        // Add all numbers from nums to the HashSet
        for (int i : nums) 
            set.add(i);

        // Traverse nums again and check for consecutive sequence
        int longest = 0;
        for (int i : nums) {
            
            int curr = 1;

            int num = i;
            
            // Traverse all numbers less than current one
            while (set.contains(num - 1)) {
                // Do not consider part of sequence again
                set.remove(num - 1);
                curr++;
                num--;
            }

            // Traverse numbers greater than current one
            num = i;
            while (set.contains(num + 1)) {
                // Do not consider part of sequence again
                set.remove(num + 1);
                curr++;
                num++;
            }

            if (curr > longest)
                longest = curr;
        }

        return longest;
    }
}
```
Elegant approach
```java
class Solution {
    public int longestConsecutive(int[] nums) {

        // Add all numbers from nums to the HashSet
        HashSet<Integer> set = new HashSet<>();

        for (int i : nums) 
            set.add(i);

        // Traverse nums again and check for consecutive sequence
        int longest = 0;

        for (int i : set) {
            // Only work with starting elements in a sequence
            if (!set.contains(i-1)) {
                int current = 1;
                int num = i;
                
                while (set.contains(num + 1)) {
                    current++;
                    num++;
                }

                longest = Math.max(longest, current);
            }
        }

    
        return longest;
    }
}
```

