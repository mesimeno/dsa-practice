# Problem Statement

Given $n$ points in metric space, find a pair of points with the smallest distance between them.

- Metric space refers to a a set of points with a notion of ‘distance’ between them

# Examples

```
Input: arr[] = [[-1, -2], [0, 0], [1, 2], [2, 3]]
Output: 1.414214
Explanation: The smallest distance is between points (1, 2) and (2, 3),
	which is 1.414214.
```

```
Input: arr[] = [[-2, -2], [1, 2], [-1, 0], [3, 3]]
Output: 2.236068
Explanation: The smallest distance is between points (-2, -2) and (-1, 0),
	which is 2.236068
```

# Solution approach
So this is a classic problem associated with the divide and conquer problem solving paradigm. I learned it a couple semesters ago when taking *Computer Science II*, but was not concerned with the actual code implementation of the algorithm.

The brute force solution is very simple: for each point, simply calculate the Euclidian distance between itself and every other point, storing the overall minimum distance encountered as you continue. The Euclidian distance between points $(x_i, y_i)$ and $(x_j, y_j)$ is 

$$
\sqrt{(x_i-x_j)^2+(y_i-y_j)^2}
$$

The divide and conquer solution is also quite intuitive. First, store each point sorted by its $x$-coordinate in the array `xArray`. To optimize a later calculation, also store each point sorted by its $y$-coordinate in the array `yArray`. Pass these sorted lists into a recursive function, `closestPair`. 

For each pass, verify whether the base case has been reached. This is the case if either list has reached $2$ or $3$ elements (note that they are the same size, `xArray.length == yArray.length`). If so, simply find the minimum distance among the points and recurse no further.

In the general case, we must split the array in half and store each one.

We split `xArray` about the index `mid = xArray.length/2`.

We do this by copying the elements in the range $[0,$ `mid`$)$ into `xLeft` and elements in the range  $[\text{mid},$  `xArray.length`$)$ into `xRight`. 

Since each `xArray` and `yArray` contain the same elements just in different orders, we must also create `yLeft` and `yRight` by simply traversing  `yArray` once and assigning some element `i` to `yLeft` if `i.x <= xArray[mid].x` or to `yRight` if `i.x > xArray[mid].x`. 

We then pass each appropriate half recursively into `closestPair` to find the minimum distance on the left half and the minimum distance on the right half. For clarity, the pass looks like:

```java
double minLeft = closestPair(xLeft, yLeft);
double minRight = closestPair(xRight, yRight);
```

We can now assign the assumed minimum distance, `delta`, as the minimum between `minLeft` and `minRight`.

We must account for the possibility that the minimum distance might actually cross the point at which we split our set of points. If this were the case, then this hypothetical pair of points must be within distance `delta` of each other.

The packing argument is a geometric observation that states that if no two points are closer than a distance $\delta$, then a strip of area $\delta \times \delta$ can contain at most a constant number of points. A property in $2$ dimensions is that any given strip of area $\delta \times \delta$ can contain at most $7$ points. 

This is very significant for our algorithm! Say we have $m$ points within the strip. Without geometric intuition, considering any given point will require checking the distance between itself and the other $m-1$ points, which results in a time complexity of $O(m^2)$. With the packing argument in mind, we must only check the distance between the next $c$ points, where $c \leq 7$, which results in a time complexity of $O(cm) = O(m)$. This is why we have the sorted `yArray` for each pass! We avoid sorting at this step and automatically know what the next $c$ points are to check.

Let `strip` be a list that contains points with a horizontal distance `delta` of the mid point, `xArr[mid]`.

Let `minStrip` be the minimum distance between points in the strip. 

We assume that `minStrip == delta` before we try to see whether there is a lesser distance within the strip. We iterate through all points in `strip` and see if their vertical distance is within `delta` of each other. If so, we update `minStrip` to be this new distance. 

Finally, the distance of the closest pair of points is the minimum of `delta` and `minStrip`. Note that if we did not find a lesser distance within the strip, then `delta == minStrip`.

In my implementation, I use a `Result` class to store the closest two points and their distance. This doesn’t affect the algorithm’s correctness or complexity but makes the results easier to interpret.

# Working Code
```java
import java.util.Arrays;
import java.util.List;
import java.util.ArrayList;

public class ClosestPair {
    
    static class Result {
        Point a, b;
        double dist;
        
        Result(Point a, Point b, double dist) {
            this.a = a;
            this.b = b;
            this.dist = dist;
        }
        
        void formatOutput() {
            String pointA = "(" + a.x + ", " + a.y + ")";
            String pointB = "(" + b.x + ", " + b.y + ")";
            double min = Math.round(dist*100.0)/100.0;
            
             System.out.print("Shortest distance is " + min);
             System.out.print(" between points " + pointA + " and " + pointB);
        }
    }
    
    static class Point {
        double x, y;
        
        Point (double x, double y) {
            this.x = x;
            this.y = y;
        }
    }
    
	public static void main(String[] args) {
	    // Example 1:
        // Point[] points = new Point[]{
        //     new Point(-1, -2),
        //     new Point(0, 0),
        //     new Point(1, 2),
        //     new Point(2, 3)
        // };
        
        // Example 2:
        Point[] points = {
            new Point(-2, -2),
            new Point(1, 2),
            new Point(-1, 0),
            new Point(3, 3)
        };
        
        // Pre sort array before first pass
        
        // Sort by x-coordinate
        Point[] xArray = points.clone();
        Arrays.sort(xArray, (a,b) -> Double.compare(a.x, b.x));
        
        // Sort by y-coordinate
        Point[] pointsY = points.clone();
        Arrays.sort(pointsY, (a,b) -> Double.compare(a.y, b.y));
        
        Result res = closestPair(xArray, pointsY);
        res.formatOutput();
	}
	
    static Result closestPair(Point[] xArray, Point[] yArray) {
        
        int length = xArray.length;
        
        // Base case: 2 or 3 points being considered
        if (length == 2 || length == 3)
            return minDist(xArray);
            
        // Divide step:
        int mid = length / 2;
        
        // Store reference to both halves of the pointsX array
        Point[] xLeft = Arrays.copyOfRange(xArray, 0, mid);
        Point[] xRight = Arrays.copyOfRange(xArray, mid, length);
        
        // Store corresponding halves of the pointsY array
        List<Point> yLeft = new ArrayList<>();
        List<Point> yRight = new ArrayList<>();
        
        // Use mid point to decide which point goes in which y-array
        Point midPt = xArray[mid];
        for (Point point : yArray) {
            if (point.x <= midPt.x) 
                yLeft.add(point);
            else
                yRight.add(point);
        }
        
        // Conquer: determine minimum of both sides by recursion
        Result leftMin = closestPair(xLeft, yLeft.toArray(new Point[0]));
        Result rightMin = closestPair(xRight, yRight.toArray(new Point[0]));
        // Delta contains the minimum distance between the two
        Result deltaMin = null;

        if (leftMin.dist <= rightMin.dist) 
            deltaMin = leftMin;
        else    
            deltaMin = rightMin;
            
        double delta = deltaMin.dist;
        
        // Consider points in the strip: within delta distance of mid point
        List<Point> strip = new ArrayList<>();
        
        for (Point point : yArray) {
            if (Math.abs(point.x - midPt.x) < delta)
                strip.add(point);
        }
        
        Result stripRes = deltaMin;
        double minStrip = delta;
        int n = strip.size();
        
        for (int i = 0; i < n; i++) {
            
            Point refPt = strip.get(i);
            
            for (int j = i + 1; j < n; j++) {
                
                Point curPt = strip.get(j);
                
                // Since sorted, curPt >= refPt
                
                // No further check necessary if next point is too far
                if (curPt.y - refPt.y > minStrip)
                    break;
                    
                // Calculate distance that is less than delta
                double tempDist = distance(refPt, curPt);
                if (tempDist < minStrip) {
                    minStrip = tempDist;
                    stripRes = new Result(refPt, curPt, minStrip); 
                }
            }
        }
        
        // Note stripRes == delta remains unchanged if no better dist found
        return stripRes;
    }
    
    static double distance(Point a, Point b) {
        
        double deltaX = a.x - b.x;
        double deltaY = a.y - b.y;
        
        return Math.sqrt(deltaX*deltaX + deltaY*deltaY);
    }
    
    static Result minDist(Point[] arr) {

        int len = arr.length;
        
        if (len == 2) 
            return new Result(arr[0], arr[1], distance(arr[0], arr[1]));
        

        double min = Double.MAX_VALUE;
        Point a = null;
        Point b = null;
        
        
        for (int i = 0; i < len; i++) {
            for (int j = i + 1; j < len; j++) {
                
                double temp = distance(arr[i], arr[j]);
                if (temp < min) {
                    min = temp;
                    a = arr[i];
                    b = arr[j];
                }
            }
        }
        
        return new Result(a, b, min);
    }
}

```
