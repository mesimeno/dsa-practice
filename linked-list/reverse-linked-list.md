# Problem Statement
Given the `head` of a singly linked list, reverse the list, and return *the reversed list*.

# Examples
```text
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```
# Solution approach
So it’s been a while since I worked with linked lists! With stacks fresh in my mind, my initial approach was to use one to push all nodes onto the stack and then update their .next pointers as each node is popped.

Designing a recursive solution turned out to be a bit more frustrating than I had expected. I started by understanding the two base cases: either `head` is `null` or [`head.next`](http://head.next) is `null`, and we do not need to implement anything to reverse the list (since they are technically reversed!). This observation also serves as the base case for the recursive solution.

The next thing I figured out was the recursive call. We would need to get to the end of the list by recursively calling the `reverseList()` on `head.next`. 

Then, I figured that the best way to manipulate the list is by focusing on two nodes at a time. If we have the list with two nodes $A \rightarrow B \rightarrow \emptyset$, we would need to manipulate the list so that `A.next.next = A` and `A.next = null` to create the list $B\rightarrow A \rightarrow \emptyset$. This manipulation must occur at each step in the recursion. This implies that we need an additional pointer to store previous solutions as we move from the end of the list to the beginning. We store the head of this reversed sublist in `reversedList`.

I'll present the stack solution in Java and the recursive solution in Java and Python.

# Working code
Stack implementation
```java
class Solution {
    public ListNode reverseList(ListNode head) {

        // Initialize stack structure
        Deque<ListNode> stack = new ArrayDeque<>();
        
        // Traverse linked list to push all nodes to stack
        ListNode temp = head;
        while (temp != null) {
	        stack.push(temp);
	        temp = temp.next;
        }

				// New head is now at the top of the stack
				ListNode newHead = stack.isEmpty() ? null : stack.pop();
				// Reuse temp node to traverse
				temp = newHead;
				
				while (!stack.isEmpty()) {
					temp.next = stack.pop();
					temp = temp.next;
				}
				
				// Ensure last node popped points to null (avoid cycle)
				if (temp != null) 
					temp.next = null;
			
				return newHead;
    }
}
```

Recursive in Java
```java
class Solution {
    public ListNode reverseList(ListNode head) {
			// Base case
			if (head == null || head.next == null)
				return head;
				
			// Recursive call -- store reversed list as you climb up recursive tree
			ListNode reversedList = reverseList(head.next);
			
			// Manipulate the list locally
			head.next.next = head;
			head.next = null;
			
			return reversedList;  
    }
}
```

Recursive in Python
```python
class Solution(object):
    def reverseList(self, head):
        """
        :type head: Optional[ListNode]
        :rtype: Optional[ListNode]
        """
        
        if not head or not head.next:
	        return head
	        
	      reversed_list = self.reverseList(head.next)
	      
	      head.next.next = head
	      head.next = None
	      
	      return reversed_list
```
