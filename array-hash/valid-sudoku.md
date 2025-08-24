# Problem Statement
Determine if a `9 x 9` Sudoku board is valid. Only the filled cells need to be validated **according to the following rules**:

1. Each row must contain the digits `1-9` without repetition.
2. Each column must contain the digits `1-9` without repetition.
3. Each of the nine `3 x 3` sub-boxes of the grid must contain the digits `1-9` without repetition.

# Examples
```text
Input: board =
[["5","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: true
```

```text
Input: board = 
[["8","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: false

	Note that two 8s are in the same box (upper left one)
```
# Solution Approach
This problem actually made me take a break from coding problems for a couple of weeks! Well, I also had other things to do, but this problem seemed pretty tough to wrap my head around.

To ensure property 1 and property 2 are satisfied, the logic is pretty much the same. For 1, simply traverse each row of the matrix and use a new `HashSet<Integer>` to determine whether any element is seen more than once. For 2, you do the exact same but for columns instead.

Property 3 is what gave me some problems. Looking back, it was really all about finding how to move the bounds correctly. In a $9\times9$ matrix, we have $9$ boxes to consider.

I use the following notation to mean all pairs where the row is in the range $[i, j, k]$ and the column is taken from one of the three block ranges


$$
[0,1,2] \times \{[0,1,2], [3, 4, 5], [6,7,8]\}
$$

$$
[3,4,5] \times \{[0,1,2], [3, 4, 5], [6,7,8]\}
$$

$$
[6,7,8] \times \{[0,1,2], [3, 4, 5], [6,7,8]\}
$$

I intuitively knew that we would need to use a double `for` loop to define our ranges. There are $3$ possible ranges we could be in for the current row and $3$ possible ranges we could be in the for current column. I use the following fields to determine how to locate our submatrix within the given matrix:
- For `i = 0`, our row range is from `0` (inclusive) to `3` (exclusive)
    - Let `startRow = 0` and `endRow = 3`
- For `i = 1`, our row range is from `3` (inclusive) to `6` (exclusive)
    - We can generalize `startRow` and `endRow` by observing
    - `startRow = i * 3` and `endRow = starRow + 3`
- For `j = 0`, our row range is from `0` (inclusive) to `3` (exclusive)
    - It turns out that we can also generalize `startColumn` and `endColumn` by the same logic
    - `startColumn = j * 3` and `endColumn = startColumn + 3`

 Ok, now that we have defined the submatrix, we can simply use a HashSet<Integer> and a double for loop to traverse it and determine whether any element appears more than once!

 # Working Code
 ```java
class Solution {
	public boolean isValidSudoku(char[][] board) {
		// To have clean, modular code
		return validRows(board) && validColumns(board) && validSubboxes(board);
	}
	
  private boolean validRows(char[][] board) {

      HashSet<Character> digits = null;

      for (int i = 0; i < 9; i++) {

          digits = new HashSet<>();

          for (int j = 0; j < 9; j++) {
              
              char temp = board[i][j];
              if (temp == '.') continue;

              if (!digits.contains(temp))
                  digits.add(temp);

              else 
                  return false;
          }
      }

      return true;
  }

  private boolean validColumns(char[][] board) {

      HashSet<Character> digits = null;

      for (int i = 0; i < 9; i++) {

          digits = new HashSet<>();

          for (int j = 0; j < 9; j++) {
              
              char temp = board[j][i];
              if (temp == '.') continue;

              if (!digits.contains(temp))
                  digits.add(temp);
              
              else
                  return false;
          }
      }

      return true;
  }

	private boolean validSubboxes(char[][] board) {
		// Possible ranges for the rows
		for (int i = 0; i < 3; i++) {
			// Possible ranges for the columns
			for (int j = 0; j < 3; j++) {
				// Define the bounds of the current box
				int startRow = i * 3;
				int endRow = startRow + 3;
				int startCol = i * 3;
				int endCol = i * 3;
				
				// Test current box and return false if invalid
				if (!validateBox(board, startRow, endRow, startCol, endCol))
					return false;
			}
		}
		
		return true;
	}

	private boolean validateBox(char[][] board, int startRow, 
															int endRow, int startCol, int endCol) {
	
	
		HashSet<Character> set = new HashSet<>();
		
		for (int i = startRow; i < endRow; i++)
			for (int j = startCol; j < endCol; j++) {
			
				char temp = board[i][j];
				if (temp == '.') 
					continue;
				
				if (!set.contains(temp))
					set.add(temp);
				// Return false if this character has been seen within this subbox
				else 
					return false;
			}
			
		return true;
	}
}
```
