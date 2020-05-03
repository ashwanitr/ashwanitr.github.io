---
layout: post
title: Is your Binary Search Bug Free?
category: General
---


 Binary search is an Algorithm to find an element in a sorted list or array in O(logn) time complexity where ‘n’ is the number of elements in the list. The idea behind it is very simple: divide the array into two(equal) parts multiple times until the middle element is equal to the target element. After each division reduce the search space and consider only one half of the array. If the middle element is less than the target we consider the second half and first half when the middle element is greater than target element. 

Here's the simple code for binary search:

{% highlight c++ %}
int binarySearch(int arr[], int left, int right, int x) {
  while (left <= right) {
    int mid = left + right / 2;
 
    if (arr[mid] == x) {
      return mid;
    } else if (arr[mid] < x) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
 
  return -1;
}

{% endhighlight %}

Did you find anything wrong with this code? 

<br>
<br>
The code is correct for most of the cases but the bug it contains exists on the line:

{% highlight c++ %}

int mid = left + right / 2;

{% endhighlight %}

Did you figure out the bug now?
<br>
<br>
Here is the explanation by Joshua Bloch on [**Google AI Blog**](https://ai.googleblog.com/2006/06/extra-extra-read-all-about-it-nearly.html)  

<br>

> In Programming Pearls Bentley says that the analogous line "sets m to the average of l and u, truncated down to the nearest integer." On the face of it, this assertion might appear correct, but it fails for large values of the int variables low and high. Specifically, it fails if the sum of low and high is greater than the maximum positive int value (231 - 1). The sum overflows to a negative value, and the value stays negative when divided by two. In C this causes an array index out of bounds with unpredictable results. In Java, it throws ArrayIndexOutOfBoundsException. This bug can manifest itself for arrays whose length (in elements) is 230 or greater (roughly a billion elements). This was inconceivable back in the '80s, when Programming Pearls was written, but it is common these days at Google and other places. In Programming Pearls, Bentley says "While the first binary search was published in 1946, the first binary search that works correctly for all values of n did not appear until 1962." The truth is, very few correct versions have ever been published, at least in mainstream programming languages.

<br>

In simple words this line can possibly cause overflow error if the left and right are extremely large and int can not store them.   
<br>
So you may be wondering how to fix this bug?
<br>
<br>
We can calculate mid by :  
{% highlight c++ %}

int mid = left + ((right - left) / 2);

{% endhighlight %}


Also note that right shifting a number by one bit is equivalent to dividing it by 2. We can utilize the unsigned right shift operator `(>>>)` available in Java. The `>>>` operator lets you treat int and long as 32-bit and 64-bit unsigned integral types (Java does not provide unsigned int and unsigned long data types).
{% highlight java %}
int mid = (left + right) >>> 1; 
{% endhighlight %}
In C and C++, where you don't have the `>>>` operator, you can do this: 
{% highlight C++ %}
int mid = ((unsigned int)left + (unsigned int)right)) >> 1;
{% endhighlight %}
<br>

>The binary-search bug also applies to merge-sort, and to other divide-and-conquer algorithms.

<br>


I highly recommend you to read [**this article**](https://ai.googleblog.com/2006/06/extra-extra-read-all-about-it-nearly.html) on Google AI Blog