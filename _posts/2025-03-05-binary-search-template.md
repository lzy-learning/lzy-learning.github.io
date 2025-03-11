---
layout: post  # specified the layout current markdown file uses
title: "Template of Binary Search"   # the title of the essay
subtitle: Simple but easy prone algorithm!    # the descirption of current essay
tags: [binary search, algorithm, c++]      # convenient for readers to search for articles based on tags
comments: true      # allow reader comment on this article
author: Lin Zhiyang
---

Binary search is a fundamental and powerful algorithm used to find a specific element in a sorted array with a time complexity of O(log n). While it's relatively straightforward to implement when searching for an exact match in a sorted array without duplicates, things get more complicated when dealing with:

1. Finding lower/upper bounds
2. Locating the first/last occurrence of an element in arrays with duplicates
3. Avoiding common pitfalls like index out-of-bounds errors or infinite loops

This guide will walk you through best practices for implementing binary search, covering key considerations and providing ready-to-use templates in C++.

### Key Considerations of Binary Search

When working with binary search, there are several important factors to keep in mind:

1. Setting the Right Boundary

   The right boundary can be initialized as either `n-1` or `n`, which affects:

   - The loop condition
   - How we update the boundaries after each iteration

   Here's the general rule:

   - Use `right = n-1` with `left <= right` and `right = mid-1`
   - Use `right = n` with `left < right` and `right = mid`
   

2. Boundary Updates

   The way we update boundaries can make or break our implementation. A common issue is getting stuck in infinite loops or missing the correct answer. Here's a practical solution:

   Use a `record` variable to track valid results. When you find a satisfactory element, store its index in `record`. This approach lets you safely update boundaries without worrying about losing the correct answer.

   Here's an example implementation for finding the lower bound:

   ```c++
   int lower_bound(std::vector<T> arr, T target){
       int l = 0, r = arr.size()-1, m, record=-1;
       while(l <= r){
           m = l + (r-l)/2;
           if(arr[m] >= target){
               record = m;     // record the satisfied result
               r = m-1;
           }else
               l = m+1;
       }
       return record;
   }
   ```

   In the above code, the left and right boundaried both assgin to `m +/- 1`, so the loop infinite would never happen.


3. Using the `std::lower_bound` and `std::upper_bound`

   While understanding the core algorithm is important, using built-in functions can save time and reduce bugs. In C++, the STL provides:

   - `std::lower_bound`: Finds the first element ≥ target
   - `std::upper_bound`: Finds the first element > target

   Here's how to find the last occurrence of an element:

   ```c++
   template <typename T>
   int last_appear(std::vector<T> &arr, T target)
   {
       auto it = std::upper_bound(arr.begin(), arr.end(), target); // Obtain the first element greater than target
   
       if (it == arr.end())
           return (arr.empty() || arr.back() == target) ? -1 : arr.size() - 1;
   
       if (it != arr.begin())
       {
           --it;
           if (*it == target)
               return std::distance(arr.begin(), it);
       }
       return -1;
   }
   ```

   The default behavior of `std::lower_bound` is to return an iterator pointing to the first element that is greater than or equal to the target value, while `std::upper_bound` searches for the first iterator whose value is strictly greater than the target value. Therefore, finding the first occurrence of a value is typically associated with `std::lower_bound`, whereas finding the last occurrence is related to `std::upper_bound`. Both functions return the end iterator of the original iterable object to indicate the absence of a matching element. Additionally, note that these template functions support iterable objects and user-defined comparison rules.

   ```c++
   #include <iostream>
   #include <iterator>
   
   class CustomArray
   {
   public:
       using value_type = long long;
       using size_type = std::size_t;
   
       class Iterator
       {
       public:
           // tell standard library algorithms which operations the iterator supports
           using iterator_category = std::random_access_iterator_tag;
           using value_type = long long;
           // the data type of difference between two iterator
           using difference_type = std::ptrdiff_t;
   
           using pointer = long long *;
           using reference = long long &;
   
           Iterator(pointer ptr) : m_ptr(ptr) {}
           reference operator*() const { return *m_ptr; }
           pointer operator->() { return m_ptr; }
   
           // prefix increment
           Iterator &operator++()
           {
               ++m_ptr;
               return *this;
           }
   
           // postfix increment
           Iterator operator++(int)
           {
               Iterator temp = *this;
               ++(*this);
               return temp;
           }
   
           Iterator &operator--()
           {
               --m_ptr;
               return *this;
           }
   
           Iterator operator--(int)
           {
               Iterator temp = *this;
               --(*this);
               return temp;
           }
   
           Iterator& operator+=(difference_type n) { m_ptr += n; return *this; }
           Iterator& operator-=(difference_type n) { m_ptr -= n; return *this; }
   
           Iterator operator+(difference_type n) const { return Iterator(m_ptr + n); }
           Iterator operator-(difference_type n) const { return Iterator(m_ptr - n); }
           difference_type operator-(const Iterator& other) const { return m_ptr - other.m_ptr; }
   
           bool operator==(const Iterator& other) const { return m_ptr == other.m_ptr; }
           bool operator!=(const Iterator& other) const { return m_ptr != other.m_ptr; }
           bool operator<(const Iterator& other) const { return m_ptr < other.m_ptr; }
           bool operator>(const Iterator& other) const { return m_ptr > other.m_ptr; }
           bool operator<=(const Iterator& other) const { return m_ptr <= other.m_ptr; }
           bool operator>=(const Iterator& other) const { return m_ptr >= other.m_ptr; }
   
       private:
           pointer m_ptr;
       };
   
       CustomArray(size_type size):m_size(size){
           m_data = new value_type[size];
       }
       ~CustomArray(){
           delete[] m_data;
       }
   
       value_type& operator[](size_type index) { return m_data[index]; }
       const value_type& operator[](size_type index) const { return m_data[index]; }
       
       Iterator begin() { return Iterator(m_data); }
       Iterator end() { return Iterator(m_data + m_size); }
   
   private:
       size_type m_size;
       value_type *m_data;
   };
   
   struct LongLong{
       long long val;
   };
   // retrive the maximum element from arr which is less than target
   int floor(CustomArray &arr, LongLong target) {
       auto it = std::lower_bound(arr.begin(), arr.end(), target,
           [](const CustomArray::value_type& element, const LongLong& target) {
               return element < target.val;
           });
   
       if (it != arr.begin() && it != arr.end()) {
           --it;
           return std::distance(arr.begin(), it);
       }
       return -1;
   }
   ```



### C++ Template for Binary Search

Here are some ready-to-use binary search templates that cover common use cases:

```c++
#include<iostream>
#include<vector>

namespace BinarySearch
{

    template <typename T, typename Compare = std::less<T>>
    /***
     * @description: Search the index of the maximum element less than or equal to the specified element target
     *
     * @param { T} target       The search element must be less than or equal to the target.
     * @param {Compare} comp    The comparison rule must return true when the first argument is less than the second argument.
     * @return {*}              The index of the maximum element less than or equal to target. The function return -1 if there is no valid element.
     */
    int floor_idx(const vector<T> &arr, T target, Compare comp = Compare())
    {
        int l = 0, r = arr.size() - 1;
        int record = -1;
        while (l <= r)
        {
            int m = l + (r - l) / 2;
            if (!comp(arr[m], target))
            {
                r = m - 1;
            }
            else
            {
                record = m;
                l = m + 1;
            }
        }
        return record;
    }

    template <typename T, typename Compare = std::less<T>>
    /***
     * @description: The same parameters and usage as floor_idx(), while this function return the retrieved element rather than its index.
     */
    T floor(const vector<T> &arr, T target, Compare comp = Compare())
    {
        int ans = floor_idx<T>(arr, target, comp);
        return ans == -1 ? NULL : arr[ans];
    }

    template <typename T, typename Compare = std::less<T>>
    /***
     * @description: Search the index of the minimum element greater than or equal to the specified element target
     *
     * @param {vector<T>} &arr
     * @param { T} target       The search element must be greater than or equal to the target.
     * @param {Compare} comp    The user defined comparator, using std::less<T> as default. And the comparison rule must return true when the first argument is less than the second argument.
     * @return {*}              The index of the minimum element greater than or equal to target. The function return -1 if there is no valid element.
     */
    int ceil_idx(const vector<T> &arr, T target, Compare comp = Compare())
    {
        int l = 0, r = arr.size() - 1;
        int record = -1;
        while (l <= r)
        {
            int m = l + (r - l) / 2;
            if (comp(target, arr[m]))
            {
                record = m;
                r = m - 1;
            }
            else
            {
                l = m + 1;
            }
        }
        return record;
    }

    template <typename T, typename Compare = std::less<T>>
    /***
     * @description: The same parameters and usage as ceil_idx(), while this function return the retrieved element rather than its index.
     */
    T ceil(const vector<T> &arr, T target, Compare comp = Compare())
    {
        int ans = ceil_idx<T>(arr, target, comp);
        return ans == -1 ? NULL : arr[ans];
    }

    template <typename T, typename Compare = less<T>>
    /***
     * @description: Retrieve the first position in which the element is greater than or equal to the target.
     * @param {vector<T>} &arr
     * @param { T} target
     * @param {Compare} comp    The comparison rule must return true when the first argument is less than the second argument.
     * @return {*}   The index meets the requirement, or size of arr if there are no matching element.
     */
    int lower_bound(const vector<T> &arr, T target, Compare comp = Compare())
    {
        int l = 0, r = arr.size();
        while (l < r)
        {
            int m = l + (r - l) / 2;
            if (!comp(arr[m], target))
                r = m;
            else
                l = m + 1;
        }
        return l;
    }

    template <typename T, typename Compare = less<T>>
    /***
     * @description: Retrieve the first position in which the element is greater than the target.
     * @param {vector<T>} &arr
     * @param { T} target
     * @param {Compare} comp    The comparison rule must return true when the first argument is less than the second argument.
     * @return {*}   The index meets the requirement, or size of arr if there are no matching element.
     */
    int upper_bound(const vector<T> &arr, T target, Compare comp = Compare())
    {
        int l = 0, r = arr.size();
        while (l < r)
        {
            int m = l + (r - l) / 2;
            if (comp(target, arr[m]))
                r = m;
            else
                l = m + 1;
        }
        return l;
    }

    template <typename T, typename Compare = less<T>>
    /***
     * @description: @description: Retrieve the first position in which the element is equal to the target. The function will call the external function lower_bound() to implement functionality.
     * @param {vector<T>} arr
     * @param { T} target
     * @param {Compare} comp
     * @return {*}
     */
    int first_appear(const std::vector<T> &arr, T target, Compare comp = Compare())
    {
        int first_larger_or_equal_idx = lower_bound(arr, target, comp);
        if (first_larger_or_equal_idx != -1 && arr[first_larger_or_equal_idx] == target)
            return first_larger_or_equal_idx;
        return -1;
    }

    template <typename T, typename Compare = less<T>>
    /***
     * @description: Retrieve the last position in which the element is equal to the target. The function will call the external function upper_bound() to implement functionality.
     * @param {vector<T>} &arr
     * @param { T} target
     * @param {Compare} comp    The comparison rule must return true when the first argument is less than the second argument.
     * @return {*}   The index meets the requirement, or -1 if there are no matching element.
     */
    int last_appear(const vector<T> &arr, T target, Compare comp = Compare())
    {
        int first_larger_idx = upper_bound(arr, target, comp);
        if (first_larger_idx > 0 && arr[first_larger_idx - 1] == target)
            return first_larger_idx - 1;
        return -1;
    }
}
```

### Conclusion

In this article, we've explored the fundamental concepts and practical implementations of binary search algorithms. The templates and examples provided here are meant to serve as starting points for your own implementations. I hope the explanations and code samples have been helpfule in your journey to understand binary search better. Happy coding!
