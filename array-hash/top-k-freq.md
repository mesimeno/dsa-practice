# Problem Statement
Given an integer array ```nums``` and an integer ```k```, return the ```k``` most frequent elements. You may return the answer in any order.

# Examples
```text
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```

# Solution Approach
This was another fun one to tackle. My initial approach actually turned out to be on the right track. It’s just that I got stuck when trying to retrieve a key from a hash map given a value. More on that later. 

The successful approach I designed was quite simple. The key behind it is manipulating a hash map. I created a hash map that maps `Integer` to a user-defined class I created called `Element`. This class contains two attributes, `int value` and `int frequency`. I added some class methods to make setting, getting, and updating the attributes easier. 

The code speaks for itself. It’s not too algorithmically complex. After the hash map initialization, I iterate through the input array, `nums`, and map a unique value to an `Element` object. I create it if it needs to be created and otherwise call the method `incrementFrequency()` to update. 

After that, I load the values of the hash map, which in this case are `Element` objects, into an ArrayList. I then sort this ArrayList using a lambda statement that compares these object’s `frequency` attribute.

Then, I iterate through this manipulated array for only `k` times and return the resultant array.

As it turns out, my approach runs in $O(n\log n)$ and has a space complexity of $O(n)$.

The solution, however, can be optimized to run in $O(n)$. This is accomplished through the Bucket Sort approach.

I will also present the optimized solution using Bucket Sort. We simply use an additional array that contains the possible frequencies. If we have an input array of `5` elements, we’ll use a bucket array of `6` elements, where index `i` holds elements with a frequency of `i`, and index `0` is necessarily empty, since any value has a frequency of at least 1.

# Working Code
My approach
```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
    
        // Map an integer to its frequency
        HashMap<Integer, Element> map = new HashMap<>();

        for (int i = 0; i < nums.length; i++) {
            int key = nums[i];
            // If element has not been seen
            if (!map.containsKey(key)) {
                // Create associated Element object
                Element temp = new Element(key, 1);
                // Map value to the Element object
                map.put(key, temp);
            }
            // If element has been seen, just increment its frequency
            else {
                Element temp = map.get(key);
                temp.incrementFrequency();
            }
        }

    
        ArrayList<Element> list = new ArrayList<>();
        list.addAll(map.values());

        list.sort((a, b) -> Integer.compare(b.getFrequency(), a.getFrequency()));

        int[] res = new int[k];

        for (int i = 0; i < k; i++) {
            res[i] = list.get(i).getValue();
        }

        return res; 
    }
}

class Element {
    
    private int value;
    private int frequency;

    public int getFrequency() {
        return frequency;
    }

    public int getValue() {
        return value;
    }

      public void incrementFrequency() {
        frequency++;
    }

    public Element(int value, int frequency) {
        this.value = value;
        this.frequency = frequency;
    }
}
```


Bucket sort approach
```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // Create hash map to count elements and their frequencies
        HashMap<Integer, Integer> map = new HashMap<>();
        // Iterate through each element in nums
        for (int num : nums) {
            // map.getOrSetDefault(num, 0) + 1 creates or increments a frequency record
            // Condensed way to avoid an if/else block
            map.put(num, map.getOrDefault(num, 0) + 1);
        }


        // Create an array of lists to hold frequencies
        List<Integer>[] frequency = new List[nums.length+1];
        // Initialize each index to be able to hold an implementable list
        for (int i = 0; i < frequency.length; i++) 
            frequency[i] = new ArrayList<>();
	
        // Loop through the (key, value) pairs returned by map.entrySet()
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            int frequencyIdx = entry.getValue();
            int element = entry.getKey();

            frequency[frequencyIdx].add(element);
        }

        int[] res = new int[k];

        int index = 0;
        // Traverse frequency buckets from highest to lowest
        for (int freq = frequency.length-1; freq >= 1; freq--) {
            // Do not consider empty bucketes
            if (!frequency[freq].isEmpty()) {

                // Add all numbers in current bucket to the res array
                for (int num: frequency[freq]) {
                    res[index++] = num;
                    // Stop once k elements are collected
                    if (index == k) 
                        return res;  
                }
            }
        }

        return res;
    }
}
```
