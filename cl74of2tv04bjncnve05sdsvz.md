---
title: "Arrays — Beginning with Data Structues"
datePublished: Mon Aug 22 2022 11:30:00 GMT+0000 (Coordinated Universal Time)
cuid: cl74of2tv04bjncnve05sdsvz
slug: arrays-beginning-with-data-structues
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/jLwVAUtLOAQ/upload/v1660043227062/21_NlIY_h.jpeg
tags: cpp, programming, data-structures, fundamentals, array

---

Ask a Computer Major what they need to focus on the most, and 9/10 of them would say that they need a firmer grasp of Data Structures and Algorithms. As a sophomore in college, I've also started the tedious task of understanding and implementing various Data Structures. I'm going to be using C++ for my implementations but would also be providing pseudo-code for other languages!

A quick reminder that I'm just starting out with this, so my solutions might not be the most optimized. I would love to learn more in the comments!

# Arrays

To sum it up in as few lines as possible, an Array is a collection of objects with similar data types. In C++, once an array size has been defined, it can not be changed unless we use the concept of Dynamic Memory Allocation. This article will focus on the Insertion, Deletion, and rotation of arrays.

## Traversing an Array

Traversing an Array refers to iterating through the elements of the array. Its time complexity would be `O(n)` for an array of size `n`.

### C++

```cpp
#include <iostream>
using namespace std;

int main(){
int size = 5;
int arr[size] = {7,3,2,5,1};
for(int i = 0; i < size; i++){
    cout << i << "th Element is: " << arr[i] << endl;
    }

return 0;
}
```

### Psuedo-Code

```python
INT size = 5
declare INT array of Length size
For i = 1 to 5
    OUTPUT i-th element of array
EndFor
```

### Output:

```python
0th Element is: 7
1th Element is: 3
2th Element is: 2
3th Element is: 5
4th Element is: 1
```

## Inserting an element into an Array

Before we start, we will assume that an array of significant length has been made in advance, and we would not need to use pointers or dynamic memory allocation for the same. This section would include:

* Inserting at the End of an Array
    
* Inserting at the beginning of the Array
    
* Inserting at ANY position of the Array
    

Also, some constants we would be declaring are given below:

```md
n -> total number of elements in the array
item -> element to be added
position -> position at which item is supposed to be inserted
a -> array with significant space available for new item
```

### C++

#### Inserting at the End of an Array

```cpp
#include <iostream>
using namespace std;

int main(){
    a[n] = item;
    n += 1;
    return 0;
}
```

#### Inserting at the Beginning of an Array

```cpp
#include <iostream>
using namespace std;

int main(){
    for (int i = n-1; i > 0; i--){
        a[i+1] = a[i];
    }
    a[pos-1] = item;
return 0;
}
```

#### Inserting at ANY position of an Array

```cpp
#include <iostream>
using namespace std;

int main(){
    for (int i = n-1; i >= pos; i--){
        a[i+1] = a[i];
    }
    a[pos-1] = item;
return 0;
}
```

### Pseudo-Code

#### Inserting at the End of the Array

```python
SET the n+1th element of the array = item
```

#### Inserting at the beginning of the Array

```python
For int i = last element index to first element decreasing by 1
    SET (i + 1)-th element of Array = i-th element of the Array
EndFor
SET (pos-1)-th element of Array = item
```

#### Inserting at ANY position of the Array

```python
For int i = last element index to pos-th element decreasing by 1
    SET (i + 1)-th element of Array = i-th element of the Array
EndFor
SET (pos-1)-th element of Array = item
```

#### Interpretation

The Best Case for this algorithm would be when we need to add an element to the end of the array, as it can be achieved in `O(1)`. Meanwhile, the Worst Case Scenario would be when we need to insert an element to the first index, which would have a time complexity of `O(n)`.

## Deleting Elements from an Array

As important as inserting elements directly into an array, we should also be able to remove them. We would be using the same constants as in the above section and have three sub-sections here.

* Deleting the Last Element of the Array
    
* Deleting the First Element of the Array
    
* Deleting ANY position element of the Array
    

### C++

#### Deleting the Last Element of the Array

```cpp
#include <iostream>
using namespace std;

int main(){
    a[n-1] = 0;
    n n-1;
return 0;
}
```

#### Deleting the First Element of the Array

```cpp
#include <iostream>
using namespace std;

int main(){
    for (int i = 1; i < n; i++){
        a[i-1] = a[i];
    }
    n=n-1;
return 0;
}
```

#### Deleting ANY position element from the Array

```cpp
#include <iostream>
using namespace std;

int main(){
    for (int i = pos; i < n; i++){
        a[i-1] = a[i];
    }
    n=n-1;
return 0;
}
```

### Pseudo-Code

#### Deleting the Last Element of the Array

```md
SET the last element of the Array to Zero
```

#### Deleting the First Element of the Array

```md
For i = to n
  Array's i-1-th element = Array's i-th element
EndFor
SET n = n - 1
```

#### Deleting ANY position element from the Array

```md
For i =pos to n
  Array's i-1-th element = Array's i-th element
EndFor
SET n = n - 1
```

#### Interpretation

The Best Case for this algorithm would be when we need to remove an element from the last index of the array, as it can be achieved in `O(1)`. Meanwhile, the Worst Case Scenario would be when we need to remove an element from the beginning, which would have a time complexity of `O(n)`.

## Rotation of an Array

Rotation of an array refers to changing the order of an array by shifting the elements by `m` spaces either on the left or the right.

Example via: [GeeksForGeeks](https://www.geeksforgeeks.org/array-rotation/)

```python
Input: arr[] = {1, 2, 3, 4, 5, 6, 7}, d = 2
Output: 3 4 5 6 7 1 2
```

```python
Input: arr[] = {3, 4, 5, 6, 7, 1, 2}, d=2
Output: 5 6 7 1 2 3 4
```

### C++

```cpp
using namespace std;

int gcd(int a, int b){
   if (b == 0)
     return a;
   else
     return gcd(b, a % b);
}

void array_left_rotate(int arr[], int d, int n){
   int i, j, k, temp;
   for (i = 0; i < gcd(d, n); i++){
     temp = arr[i];
     j = i;
     while (1) {
       k = j + d;
       if (k >= n)
         k = k - n;
       if (k == i)
         break;
       arr[j] = arr[k];
       j = k;
 }
   arr[j] = temp;
   }
}

int main(){
 int arr[] = { 1, 2, 3, 4, 5, 6, 7 };
 int n = sizeof(arr) / sizeof(arr[0]);
 cout<<"\nArray elements before rotating : \n";
 for(int i = 0; i < n; i++){
   cout<<arr[i]<<"\t";
 }
 int no_of_rotations = 1;
 array_left_rotate(arr, no_of_rotations, n);
 cout<<"\n\nArray elements after rotating : \n";
 for(int i = 0; i < n; i++)
 {
 cout<<arr[i]<<"\t"; // Printing the array elements after rotation of elements
 }
 cout<<"\n";
 return 0;
}
```

### Pseudo-Code

```md
1. divide the array into M sets, where M = GCD (numElements, rotationNumber), and then rotate the elements in each set.
2. The number of numElements of the array and rotationNumber to be made to the array, the GCD (numElements, rotationNumber) number of blocks are made.
3. In each block, shifting will occur to the block's corresponding elements.
4. After all the blocks' elements are shifted, the array will be rotated for the given number of times.
```

#### Interpretation

This method is not the most optimized method of rotating an array. Also, we have only rotated an array LTR (Left to Right). We can also use the concept of recursion.