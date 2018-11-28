
## concurrent query && tree structure
--------------
In this kinds of question, we will be required to monitor the property of the structure while regularly updating it. A quick intuition would be using structures like balanced tree or deque that enable modification within a rather better time complexity. Balanced tree will usually be used for concurrent searching while deque could be applied when instant max/min was required. We will discuss detailed scenarios below.

### 1. slicing window max/min/average
In this kinds of questions, we could use a single treemap/treeset.



### 2. slicing window median
- practise: LC480
- methods 1
	- we use two treeset(storing index and one storing increasing sequence and another storing decreasing sequence.
	```java
	if keep inc > dec;
	inc.add(x);
	dec.add(inc.poll()); 
	while(dec.size()> inc.size()) inc.add(dec.poll());
	```
- methods 2
	- we use primitive datastructure to kill overhead
	- arrays.copyOf, system.arraycopy(src, stidx, des, stidx, len)
	- use an array size of k to maintain sorted structure of slicing window. 
	- each time we only need to adjust up to two slot ( delete prev value and add new slot)


### 3. interval join
- practise: LC352
- fill in a number to check if it can be formed a new interval with existing intervals
- technique: treemap, logn combine (because we will only input a single number to fill)
			
			
			
### 4. static query && structure
- practise: LC topk, 
- scenario: ask for global properties of the data structure
- related techniques: priorityqueue, bucket sort
- classification:
	- <top k>
		- calculate freq by map
		- use priorityqueue/bucketsort get topk/k th element
	- <max freq, top 1>
		- only track max is needed, no pq or bucketsort
