# Problem Statement
Given an array of strings strs, group the anagrams together. You can return the answer in any order.

# Examples
```
Input: strs = ["eat","tea","tan","ate","nat","bat"]

Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

# Solution Approach
I honestly really liked this one! It was fun to think about and it involved one of those ‘aha’ moments.

I begun by remembering the *Valid Anagram* LeetCode problem I solved earlier. That was pretty simple. In fact, the one line solution in Python is: `return sorted(str1) == sorted(str2)`. 

The intuition is that an anagram is really just a possible arrangement of a sorted string. The other thing I considered was that in the worst case, no string within `strs` is an anagram. So, the resulting `List<List<String>>` would contain `strs.length` anagrams. In the best case, all strings within `strs` are anagrams of one another, which would result in our `List<List<String>>` having size of 1. 

With *Two Sum* in mind, I knew that the brute force method would be to compare each string with one another and somehow keep a record of the anagrams. Clearly, not efficient at all. So, HashMaps came to mind! Specifically, the key would be a sorted string we have come across and it would map to an ArrayList of its anagrams. 

My approach is as follow:

1. Iterate through each string in `strs`
2. Carry out string operations to treat this string in a sorted manner
3. Check if the (key, value) = (sorted_string, ArrayList) pair exists. Create if it it doesn’t
4. Using the sorted string as the key, retrieve the mapped ArrayList and append the current, unsorted string

I'll present the solution in both Java and Python!

# Working code
```java
class Solution {

	public List<List<String>> groupAnagrams(String[] strs) {
		
		// Trivial case: no strings
		if (strs.length == 0)
			return new ArrayList<>();
			
		// Create hash map
		// valid: HashMap<String, ArrayList> map = new HashMap<>();
		HashMap<String, List<String>> map = new HashMap<>();
		
		// Iterate through each string in strs
		for (String s: strs) {
			// Create the key by sorting the current string
			char[] temp = s.toCharArray();
			Arrays.sort(temp);
			String key = new String(temp);
			
			// Create key,value pair if not already contained in map
			if (!map.containsKey(key)) 
				map.put(key, new ArrayList<>());
			
			// Add the current, unsorted string s into the associated array list
			map.get(key).add(s);
		
		}
		
		// Create resulting list
		List<List<String>> res = new ArrayList<>();
		// Add all stored values (which are lists themselves) to this res list
		res.addAll(map.values());
		
		return res;

	}
}
```

```python
class Solution:
	def groupAnagrams(self, strs):
	
		# Handle trivial case
		if len(strs) == 0:
			return []
			
		# Initialize hash map
		map = {}
		
		# Iterate through each string in strs
		for s in strs:
			# Sort s to create its associated key
			# Note: use tuple to use key in the hash map
			# sorted(s) returns a list, which is unhashable!! hashmaps require hashable
			key = tuple(sorted(s))
			
			# Create (key, value) pair if it doesn't exist
			if key not in map:
				map[key] = []
				
			# Append 's' to its associated list
			map[key].append(s)
			
		# Return the list of list of strings
		return list(map.values())
```
