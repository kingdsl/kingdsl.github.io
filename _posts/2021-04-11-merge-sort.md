---
layout: post
title:  "Merge Sort"
date:   2021-04-11 19:20:41 +0100
categories: [Algorithms, Sort]
tags: [merge sort, algorithms]
---

# Merge Sort

The merge sort is a well-known algorithm, and undoubtedly you'll be able to find a lot of good information around the internet, even so, I've reviewed a lot of pages and videos about this basic algorithm and I still think that a lot of beginners will find it challenging to understand at first sight and the reason is that some explanations do not correlate its code with the diagrams.

In the first place, for those who don't know what the merge sort is, this is a divide-and-conquer algorithm that uses recursion to arrange a data structure, it has a complexity of O(n * log(n)).

## What is a divide-and-conquer algorithm?
In computer science, we are talking to dived-and-conquer algorithms when we apply recursive solutions to divide problems into subproblems related to the original one, and the resolution of each subproblem combined will give a solution to the main problem.

![](https://i.stack.imgur.com/YlHqG.gif)



Here you will find the java implementation for this algorithm, I've taken this code from [geeksforgeeks Rajat Mishra](https://www.geeksforgeeks.org/merge-sort/) and made some changes to explain it in my way.


```java
public class MergeSort {
    
    public void display(int[] arr){
        for(int i = 0; i < arr.length ; i++){
            System.out.print("[" + arr[i] + "] ");
        }
        System.out.println();
    }

    public void sort(int[] arr){
        internalSort(arr, 0, arr.length - 1);
    }

    private void internalSort(int[] arr, int left, int right){
        if(left < right){
            int middle = left + (right - left) / 2;
            
            internalSort(arr, left, middle);
            internalSort(arr, middle + 1, right);
            
            merge(arr, left, middle, right);
        }
    }

    private void merge(int[] arr, int left, int middle, int right) {
        int[] leftArray  = copyOfRange(arr, left, middle);
        int[] rightArray = copyOfRange(arr, middle + 1, right);

        int i = 0, j = 0;
        int k = left;

        while(i < leftArray.length && j < rightArray.length){
            arr[k++] = (leftArray[i] <= rightArray[j]) ? leftArray[i++] : rightArray[j++];
        }

        setRemainingElements(arr, leftArray,  i, k);
        setRemainingElements(arr, rightArray, j, k);
    }

    private void setRemainingElements(int[] arr, int[] half, int index, int k){
        while(index < half.length){
            arr[k++] = half[index++];
        }
    }

    private int[] copyOfRange(int[] arr, int startIndex, int endIndex){
        int[] result = new int[(endIndex - startIndex) + 1];
        int j = 0, i = startIndex;

        while(i <= endIndex){
            result[j++] = arr[i++];
        }

        return result;
    }

    public static void main(String[] args){
        int arr[] = { 99, 23, 101, 53, 62, 17, 11, 32, 8 };
        MergeSort ob = new MergeSort();
        ob.display(arr);
        ob.sort(arr);
        ob.display(arr);
    }
}
```




I don't pretend to go in deep with the theoretical definitions, so now that you have a brief idea of what the merge sort is, let's continue understanding what is happening in the code above step by step.

## Uderstanding the methods:

The first method named 'sort' is used as a wrapper to create the first call for the recursive method 'internalSort', then if an external class wants to use this functionality, it won't be required to fill all the initial parameters for the method 'internalSort' using the 'sort' method instead and providing just the input array.


```java
    public void sort(int[] arr){
        internalSort(arr, 0, arr.length - 1);
    }
```
Let's use the following input and trigger the sort method explained above:

```json
arr: [99] [23] [101] [53] [62] [17] [11] [32] [8]
```
Once the sort method is called, it will invoke the 'internalSort' method as you already saw, and the input variables will be:

```json
arr  : [99] [23] [101] [53] [62] [17] [11] [32] [8]
left : 0 //Always the first iteration starts with 0.
right: 8 //arr.length - 1
```
The 'internalSort' method will keep doing two calls to itself as long as the 'left' variable be less than the 'right' variable. The first thing here is to calculate the middle index with a simple range formula: initial + (final - initial) / 2. If you are wondering about, what happens in the case of (final - initial) is an odd number, the answer is simple, java truncates the result. For example, let's assume that the 'final'  index is 9 and the first one is 0, you'll get 9/2 and, this means 4 in terms of java integers.

```java
    private void internalSort(int[] arr, int left, int right){
        if(left < right){
            int middle = left + (right - left) / 2;
            
            internalSort(arr, left, middle);
            internalSort(arr, middle + 1, right);
            
            merge(arr, left, middle, right);
        }
    }
```
The diagram below will show you how the recursion works in this method, follow the green path, then the orange, and finally the purple one.


![](https://i.imgur.com/Iufc3pu.png)

After following the diagram, you must be aware that:
 - The current recursion level is 3
 - The algorithm has never created another array yet (This point will be discussed later)
 - The merge method will be invoke with params below

```json
arr   : [99] [23] [101] [53] [62] [17] [11] [32] [8]
left  : 0
middle: 0
right : 1
```

The merge method is where two subarrays will be created using the given indexes:

```json
leftArray  = A subarray of 'arr' from the left index to the middle.
rightArray = A subarray of 'arr' from the middle + 1 until right.
```
For this code, I've implemented my version of 'copyOfRange', as you can see, this method will return a new array slicing the array given (Both indexes inclusive). You can always use the Arrays library to perform this operation.

Once the arrays have been created the next step is to perform the merge loop, the array starts its iteration from the left index given, and both subarrays will always start from zero, our condition is simple if one of both arrays finishes its iteration the loop ends. Finally, for the remaining elements, these will be check in the method 'setRemainingElements'.

```java
    private void merge(int[] arr, int left, int middle, int right) {
        int[] leftArray  = copyOfRange(arr, left, middle);
        int[] rightArray = copyOfRange(arr, middle + 1, right);

        int i = 0, j = 0;
        int k = left;

        while(i < leftArray.length && j < rightArray.length){
            arr[k++] = (leftArray[i] <= rightArray[j]) ? leftArray[i++] : rightArray[j++];
        }

        setRemainingElements(arr, leftArray,  i, k);
        setRemainingElements(arr, rightArray, j, k);
    }
```

```json
leftArray  : [99]
rightArray : [23]

leftArray.length  : 1
rightArray.length : 1

//---- first and unique iteration for this cicle:
starts with
k: 0 //left value assigned
i: 0
j: 0

leftArray[0]  : 99
rightArray[0] : 23

arr[0] = [23] //Algorithm assigns the minimum value.

k: 1 // k++ after asignation.
i: 0
j: 1 // j++ after be setted.

Now our current arr is:
arr: [23] [23] [101] [53] [62] [17] [11] [32] [8]
```

As you may see, the value of `[99]` is missing, and `[23]` is duplicated. That's why the method 'setRemainingElements' is called.

```java
    private void setRemainingElements(int[] arr, int[] half, int index, int k){
        while(index < half.length){
            arr[k++] = half[index++];
        }
    }
```
The first calling has the following parameters:
```json
arr   :  [23] [23] [101] [53] [62] [17] [11] [32] [8]
half  :  [99] // This is the left half.
index :  0    // Corresponds to the value of i.
k     :  1    // k ended with the value of 1 from the last iteration.

//---- first and unique iteration for this cicle:
half.length : 1
arr[1] = half[0]

arr   :  [23] [99] [101] [53] [62] [17] [11] [32] [8]
k     :  2    // This change occurs in a local scope.
index :  1
```

The second calling
```json
arr   :  [23] [99] [101] [53] [62] [17] [11] [32] [8]
half  :  [23] // This is the right half.
index :  1    // Corresponds to the value of j.
k     :  1    // k ended with the value of 1 from the last iteration.

//---- The iteration won't take place.
```

The first 'merge' method call has ended, as a result of this the main array value is:

```json
arr   :  [23] [99] [101] [53] [62] [17] [11] [32] [8]
```
And the execution will return to the recursion level of 2, invoking the second call for the 'internal sort method and creating another level 3 of recursion.

## Where the arrays are being created

As I mentioned before, we are not creating any array in the 'InternalSort' method, the sub-arrays are being created inside the 'merge' method, and understanding this phase is crucial to see what's happening in the algorithm.

The 'internalSort' just chose the indexes to slide the sub-array when this one has mutated. 

### I've created a chart to visualize these steps. The eight times that the merge sort is going called. In each step, you will find:
- The current array state.
- The recursion levels in the blue background.
- Each recursion level has the current state array and a square (Red for the left array and blue for the right array).
- The Square mentioned above highlights the range of the subarray but, you will see that the array values positions will change during the merge phase.
- The last recursion level is where the subarray will be taken.
- The Merge phase (the one that occurs inside the merge method)
Array (The same array in the current state).
LeftArray & RightArray: Using the last recursion marks for the left and the right array the new subarrays are created and store in their respective variables.
- Array Merged: The array state after while loop finished its iteration.
The final array: The array resultant after both calls of 'setRemainingElements'

[![](https://i.imgur.com/6Gg20gL.png)](https://i.imgur.com/6Gg20gL.png)

*As you may see, that resultant array is the new current state used in step two.