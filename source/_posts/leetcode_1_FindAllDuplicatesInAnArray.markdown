---
layout: post
title: "Find All Duplicates in an Array"
date:  2017-2-10 13:28:00
comments: false
reward: false
tags: 
	- leetcode
---

##  Description    
Given an array of integers, 1 ≤ a[i] ≤ n (n = size of array), some elements appear twice and others appear once.  
Find all the elements that appear twice in this array.  
Could you do it without extra space and in O(n) runtime?  
<!-- more -->

leetcode : https://leetcode.com/problems/find-all-duplicates-in-an-array/

###  Example:
Input:  
[4,3,2,7,8,2,3,1]

Output:  
[2,3]

	
##  Solution	
``` cpp
vector<int> findDuplicates(vector<int>& nums) {
	if (nums.empty()) return{};
	vector<int> res;
	int n = nums.size();
	for (int i = 0; i < n; i++)
		nums[i] -= 1;       //for indexing flexibility

	int i = 0;
	while (i < n) {
		if (nums[i] != nums[nums[i]])
			swap(nums[i], nums[nums[i]]);   //swap elements to their respective indexes.
		else
			i++;
	}
	for (int i = 0; i < n; i++) {
		if (nums[i] != i)
			res.push_back(nums[i] + 1);     //get the elements which are at some other indexes (as they are occuring twice)
	}
	return res;
}
```
	

