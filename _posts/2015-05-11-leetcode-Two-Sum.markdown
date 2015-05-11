---
layout: post
title: "Two Sum"
description: ""
categories:
- leetcode
tags:
- acm
- hash


---

Given an array of integers, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

You may assume that each input would have exactly one solution.

Input: numbers={2, 7, 11, 15}, target=9
Output: index1=1, index2=2

[submit solution][0]

####一、暴力方法，O(N^2) 超时
>改用Hash，使查找复杂度由O(N)降成O(1)



####二、采用hash
*1.重复的数冲突如何处理？*
>对于这题可不处理冲突，采用一些编码技巧就可以解答出题。

*2.直接哈希hash[nums[i]] = i + 1,nums[i]为负数，数组越界咋办？*
>采用下面hash方法解决**负数**的问题
>	
```c
	#define HASH_MAX_SIZE    (1000000)  
	#define HASH_KEY(value)  (unsigned int)(value)%HASH_MAX_SIZE
	hash[HASH_KEY(nums[i])] = i + 1;
```		

*3. AC代码如下*

```c
int* twoSum(int* nums, int numsSize, int target) 
{
#define HASH_MAX_SIZE    (100000)    
#define HASH_KEY(value)  (unsigned int)(value)%HASH_MAX_SIZE    

    int *result = (int *)malloc(2*sizeof(int));
    result[0] = 0, result[1] = 0;
    
    int hash[HASH_MAX_SIZE] = {0};
    
    int i, tofind;
    for(i = 0; i < numsSize; i++)
    {
        tofind = target - nums[i];
        if(0 == hash[HASH_KEY(tofind)] )
            hash[HASH_KEY(nums[i])] = i + 1;
        else
        {
            result[0] = hash[HASH_KEY(tofind)];
            result[1] = i+1;
            return result;
        }
    }
    
    return result;
}
```

*4.采用uthash*

C++/Java都有对应的hash库，C语言没有，而leetcode C语言OJ环境默认包含[uthash][1]。下面是uthash使用AC代码：

```c
//#include"uthash.h"

struct _hashnode {
     int key ;
     int val ;
     UT_hash_handle hh; 
};

int* twoSum(int* nums, int numsSize, int target) 
{
    struct _hashnode *hashtable = NULL , *tmp = NULL, *ptr;
    int *result = (int *)malloc(2*sizeof(int));
    int i, tofind;
    
    for(i = 0; i < numsSize; i++)
    {
        tofind = target - nums[i];
        HASH_FIND_INT(hashtable, &tofind, tmp);
        if( tmp )
        {
            result[0] = tmp->val;
            result[1] = i+1;
            break;           
        }
        else 
        {
            ptr = (struct _hashnode *)malloc(sizeof(struct _hashnode));
            ptr->key =nums[i] ;
            ptr->val =i+1;
            HASH_ADD_INT(hashtable,key,ptr);
        }
    }
    
    HASH_ITER(hh, hashtable, ptr, tmp) {
        HASH_DEL(hashtable,ptr);  /* delete it (users advances to next) */
        free(ptr);            /* free it */
    }

    
    return result;
}

```



[0]:https://leetcode.com/problems/two-sum/
[1]:https://troydhanson.github.io/uthash/userguide.html