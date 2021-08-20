# Algorithms Guide



### Binary Search

Finds the index of the given element, or else returns the index where it can be inserted. Condition can be changed to return -1 if element not found.

#### Recursive

```js
function findMid(i, j){
  let mid = i + (j - i) / 2;
  return Math.ceil(mid);
}

function search(arr, start, end, elem){  
  let mid = findMid(start, end);

  if(end > start){

    if(arr[start] == elem){
      return start;
    }

    if(arr[end] == elem){
      return end;
    }
  
    if(arr[mid] == elem) {
      console.log("found elem:", mid, arr[mid]);
      return mid;
    }

    if(elem > arr[mid]){
      return search(arr, mid+1, end, elem);
    }

    return search(arr, start, mid-1, elem);

  }
  
  if(arr[mid] < elem) return mid+1;
  return mid;
  
}
```



#### Iterative

```js
function search(arr, target) {

  let start = 0;
  let end = arr.length-1;

  while(end >= start){
    let mid = Math.ceil((end-start)/2) + start;

    if(arr[mid] === target) {
      return mid;
    } 
    if (arr[mid]  > target) {
      end = mid-1;
    } else {
      start = mid+1;
    }
    console.log (start, end , mid)
  }

  return start;
};
```



### Boyer-Moore Voting Algorithm

**Intuition**

If we had some way of counting instances of the majority element as +1+1 and instances of any other element as -1−1, summing them would make it obvious that the majority element is indeed the majority element.

**Algorithm**

Essentially, what Boyer-Moore does is look for a suffix suf*s**u**f* of `nums` where suf[0]*s**u**f*[0] is the majority element in that suffix. To do this, we maintain a count, which is incremented whenever we see an instance of our current candidate for majority element and decremented whenever we see anything else. Whenever `count` equals 0, we effectively forget about everything in `nums` up to the current index and consider the current number as the candidate for majority element. It is not immediately obvious why we can get away with forgetting prefixes of `nums` - consider the following examples (pipes are inserted to separate runs of nonzero `count`).

[7, 7, 5, 7, 5, 1 | 5, 7 | 5, 5, 7, 7 | 7, 7, 7, 7]

Here, the `7` at index 0 is selected to be the first candidate for majority element. `count` will eventually reach 0 after index 5 is processed, so the `5` at index 6 will be the next candidate. In this case, `7` is the true majority element, so by disregarding this prefix, we are ignoring an equal number of majority and minority elements - therefore, `7` will still be the majority element in the suffix formed by throwing away the first prefix.

[7, 7, 5, 7, 5, 1 | 5, 7 | 5, 5, 7, 7 | **5, 5, 5, 5**]

Now, the majority element is `5` (we changed the last run of the array from `7`s to `5`s), but our first candidate is still `7`. In this case, our candidate is not the true majority element, but we still cannot discard more majority elements than minority elements (this would imply that `count` could reach -1 before we reassign `candidate`, which is obviously false).

Therefore, given that it is impossible (in both cases) to discard more majority elements than minority elements, we are safe in discarding the prefix and attempting to recursively solve the majority element problem for the suffix. Eventually, a suffix will be found for which `count` does not hit `0`, and the majority element of that suffix will necessarily be the same as the majority element of the overall array.

```cpp
int major=num[0], count = 1;
 for(int i=1; i<num.length;i++){
     if(count==0){
         count++;
         major=num[i];
     }else if(major==num[i]){
         count++;
     }else count--;
    
 }
 return major;
```

