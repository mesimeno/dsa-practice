# Problem Statement

Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return *the number of islands*.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

# Examples
```text
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]

Output: 1
```
```text
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]

Output: 3
```

# Solution Approach
Let’s first understand the restrictions of the problem. An island is defined as a cluster of `1`'s that is completely surrounded by `0`'s directly above/below (vertically) and directly to the left/right (horizontally).

Something to keep in mind is that if we have at least a single `1` in our matrix, then we are guaranteed to have a minimum of one island. 

The next thing to understand is how to best traverse the entire matrix. This is the easy part. Clearly, you must traverse each row and each column, which requires two `for()` loops. The outer one will be defined by `for(int row = 0; row < graph.length; row++)`, and the inner one will be defined by `for(int col = 0; col < graph[0].length; col++)`

The correct approach is to ‘flag’ the first `1` we come across. After that, we must traverse the matrix vertically and horizontally until we are either out of bounds or run into a `0`. As we carry out this depth-first search traversal, we must mark the visited, valid indices (valid in that `graph[r][c] == 1`) with `0`. We must break our traversal if `graph[r][c] == 0`. This is a nice recursive algorithm that pretty much writes itself. Let’s call this recursive algorithm `exploreNeighbors`

Now, we can incorporate this into our solution algorithm every time we come across a 1. We visit all possible neighbors and denote them as visited. If we can visit any other neighbors in later iterations, we are guaranteed to have come across a new island.

# Working Code
```java
class Solution {

    public int numIslands(char[][] grid) {
        int res = 0;

        for (int row = 0; row < grid.length; row++) {
            for (int col = 0; col < grid[0].length; col++) {
                if (grid[row][col] == '1') {
                    exploreNeighbors(grid, row, col);
                    res++;
                }
            }
        }

        return res;
    }

    // From an island starting point, explore neighbors as much as possible
    private void exploreNeighbors(char[][] grid, int r, int c) {

			// Base case: Ran into water, denoted '0'
			if (graph[r][c] == 0)
				return;
				
			// Otherwise, mark the current position as 'visited' by denoting it '0'
			graph[r][c] = 0;
			
			// Then explore immediately up, down, left, right
			
			// If row above can be visited
			if (r > 0)
				exploreNeighbors(graph, r-1, c);
				
			// If row below can be visited
			if (r < graph.length-1)
				exploreNeighbors(graph, r+1, c);
				
			// If column to the left can be visited
			if (c > 0)
				exploreNeighbors(graph, r, c-1);
			
			// If column to the right can be visited
			if (c < graph[0].length-1)
				exploreNeighbors(graph, r, c+1);
			
			// The expectation is that all recursive branches will reach base case   
		}
}
```
