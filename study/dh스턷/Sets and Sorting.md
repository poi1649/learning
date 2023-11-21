
the set interface has unique key and value.
key is to discriminate other elements and the interface has some method like insert and delete and find_min(), find_max(), find(k).

we keep the data structure having unique values, and it can be used by keeping only unique values.

How can we implement a set's find(k) method with an array which has elements in arbitrary order?
-> Just iterate from beginning to this array and check all elements.

we use single for loop in this case. we just do loop all the range of these array if we find the value we want to find. just return it.

In worst case, how long will the algorithm take?
-> Algorithm takes order n time. Because we should walk along the entire array before I find him.

Then how can we optimize the find(k) method?
-> By sorting the list, we can optimize the method. In this case, It takes just log n, because we can use binary search.

if i make a set using sorted array. we can reduce the time complexity into logN. because in the sorted array, we can use binary search. 

Then, In that case, what is the build method's runtime?
-> NlogN. because we should sort n length array.

In sort algorithm, what does destructive mean?
-> what destructive mean is that rather than reserving some new memory for my sorted array B and then putting a sorted version of A into B.
Basically, A destructive algorithm is one that just overwrites A with a sorted version of A.

the original array is not preserved. we don't use the extra space. we don't need to create.

How about in-place?
-> What in-place mean is they don't use extra memory in the process of sorting at least up to constant.

How can we do sort in NlogN time?
-> We can use merge sort. 

How can we implement merge sort?
-> Divide the unsorted list into n sublist. -> repeatedly merge sublists to produce new sorted sublists until there is only one sublist remaining.


between start and end -> starting point and end point


Let's call that T for time.
Let's assume T(n) as whole time complexity of merge_sort

T(n) = 2T(n / 2) + thetha of N

