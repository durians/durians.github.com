---
layout: post
title: "leetcode:Largest Number"
description: ""
categories:
- leetcode
tags:
- acm


---

Given a list of non negative integers, arrange them such that they form the largest number.

For example, given [3, 30, 34, 5, 9], the largest formed number is 9534330.

Note: The result may be very large, so you need to return a string instead of an integer.

##关键在于排序

**1.按高位最大排序**
	
	9 > 34

**2.高位相等时，按第二位排序，依次类推**

	34 > 30    

**3.对应高位都相等，位数不同的数,让大数的后面的数位与较小数的首、尾位相比，大的排前**
   	
   	3    > 30     (0 < 3)
   	824  > 8247   (7 < 8)
   	829  > 8297   (7 < 9)
   	6247 > 624    (7 >6 && 7 > 4)
   	
   	[3, 30, 34, 5, 9]排序为[9,5,34,3,30] ,于是最大数为9534330

**4.对应高位相等，位数不同，大数后面的数位与较小数的首尾位都相等时，让大数的尾数与较小数的每一位相比，第一个不相同位，谁大谁排前**(这点好像没有测试样例，我暂时忽略了，不管怎样，AC了就OK啦)
   
   	121与121111  1 < 2: 121111< 121
   	101与101111  1 > 0: 101111> 101
   	
**5.AC 代码如下**

```c
int getnumat(int num, int index)
{
    int a = pow(10,index-1);
    
    if(index == 1 && num < 10) return num;
    
    if(num < a ) return -1;
    
    while(num >= 10*a)
    {
        num = num / 10;
    }
    
    return (num % 10);
}

int cmp(const void *p, const void *q)
{
    int a = *(int *)p;
    int b = *(int *)q;
    int i = 1, c, d,tm,tc,td;
    
    while( (c = getnumat(a,i)) >= 0 && (d = getnumat(b,i)) >= 0)
    {
        if(c != d) return d - c;
         i++;
    }
    
    if( c >= 0) 
    {
        d = getnumat(b,i-1);
        tm = getnumat(b,1); 
        if(d < tm) d = tm; 
        while( (c = getnumat(a, i)) >= 0 && (c == d))
            i++;
            
        if( c < 0)
        {
            //a = 121111, b = 121  d = 1  c = -1 return 2
            //121 > 121111
            //a = 828888, b = 828  d = 8  c = -1 return 9
            //828 < 828888
            i = 1;
            tc = getnumat(a,1);
            td = getnumat(a,2);
            while(tc == td)
            {
                i++;
                tc = td;
                td = getnumat(a,i);
            }    
            return td - tc;   
        }  
         
    }
    else 
    {
        c = getnumat(a, i-1);
        tm = getnumat(a,1);
        if(c < tm) c = tm;  
        
        while( (d = getnumat(b, i)) >= 0 && (c == d))
            i++;  
           
        if( d < 0)
        {
            i = 1;
            tc = getnumat(b,1);
            td = getnumat(b,2);
            while(tc == td)
            {
                i++;
                tc = td;
                td = getnumat(b,i);
            }    
            return tc - td;   
        }  
             
    }
    
    return d - c; 
}
char* largestNumber(int* nums, int numsSize) 
{
    int i,iszero = 1;
    char * result = malloc(numsSize*31*sizeof(char));
    memset(result,0, numsSize*5*sizeof(char));
    
    qsort(nums,numsSize,sizeof(int),cmp);
    for(i = 0; i < numsSize; i++)
    {
        if(nums[i] > 0) iszero = 0;
        sprintf(result+strlen(result),"%d",nums[i]);
    }    
    if(iszero){
        result[0] = '0';
        result[1] = '\0';
    } 
        
    return result;    
}
```   	
   	
##按字符串排序处理方便
**a与b排序直接比较字符串ab与ba即可**

	a=3, b=30, ab=330,ba=303 ab > ba
	a=121,b=121111, ab=121121111,ba=121111121, ab > ba
	a=824, b=8247, ab=8248247, ba=8247824, ab > ba
	...

**AC代码如下**

```c
int compare(const void *p, const void *q)
{
    char *a = *(char **)p;
    char *b = *(char **)q;
    
    char str1[64] = {0};
    char str2[64] = {0};
    
    strcat(str1,a),strcat(str1,b);
    strcat(str2,b),strcat(str2,a);

    int ret = strcmp(str2,str1);
    
    return ret; 
}
char* largestNumber(int* nums, int numsSize) 
{
    char **numstr; 
    int i, len = 0;
    
    numstr = (char **)malloc(numsSize*sizeof(char*));
    
    for( i = 0; i < numsSize; i++)
    {
        numstr[i] = (char *)malloc(32); 
        memset(numstr[i],0,32);
        sprintf(numstr[i],"%d",nums[i]);
        if(nums[i])
            len += strlen(numstr[i]);  
    }
    qsort(numstr,numsSize,sizeof(char *),compare);
    
    if(len == 0) return "0";
    
    char *result = (char *)malloc(len);
    memset(result,0,len);
    for(i = 0; i < numsSize; i++)
    {
        strcat(result,numstr[i]);
        free(numstr[i]); 
    }
    free(numstr);
    
    return result;
    
}
```	