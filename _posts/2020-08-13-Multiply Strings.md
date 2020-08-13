---
title: Multiply Strings
tags:
 - 算法
---

## 题目

#### [43. Multiply Strings](https://leetcode-cn.com/problems/multiply-strings/)

Given two non-negative integers `num1` and `num2` represented as strings, return the product of `num1` and `num2`, also represented as a string.

**Example 1:**

```
Input: num1 = "2", num2 = "3"
Output: "6"
```

**Example 2:**

```
Input: num1 = "123", num2 = "456"
Output: "56088"
```

**Note:**

1. The length of both `num1` and `num2` is < 110.
2. Both `num1` and `num2` contain only digits `0-9`.
3. Both `num1` and `num2` do not contain any leading zero, except the number 0 itself.
4. You **must not use any built-in BigInteger library** or **convert the inputs to integer** directly.

## 解法

额……暴力模拟竖式计算的过程。

```C++
class Solution {
public:
    string add(string& num1, string& num2){
        int carry = 0;
        string res;
        for(int i=0;;i++){
            if(i>= num1.length() && i >= num2.length()){
                break;
            }
            if(i >= num1.length()){
                int bit1 = 0;
                int bit2 = num2[num2.length()-1-i] - '0';
                char value = (bit1 + bit2 + carry) % 10 + '0';
                carry = (bit1 + bit2 + carry) / 10;
                res.push_back(value);
            }else if(i >= num2.length()){
                int bit1 = num1[num1.length()-1-i] - '0';
                int bit2 = 0;
                char value = (bit1 + bit2 + carry) % 10 + '0';
                carry = (bit1 + bit2 + carry) / 10;
                res.push_back(value);
            }else{
                int bit1 = num1[num1.length()-1-i] - '0';
                int bit2 = num2[num2.length()-1-i] - '0';
                char value = (bit1 + bit2 + carry) % 10 + '0';
                carry = (bit1 + bit2 + carry) / 10;
                res.push_back(value);
            }
        }
        if(carry) res.push_back(carry + '0');
        string ans;
        for(int i=res.length()-1;i>=0;--i){
            ans.push_back(res[i]);
        }
        return ans;
    }
    string multiplyOneChar(string& num1, char num2){
        string res;
        int carry = 0;
        int bit2 = num2 - '0';
        for(int i=num1.length()-1;i>=0;i--){
            int bit1 = num1[i] - '0';
            int temp = bit1 * bit2 + carry;
            char value = temp % 10 + '0';
            carry = temp / 10;
            res.push_back(value);
        }
        if(carry) res.push_back(carry + '0');
        string ans;
        for(int i=res.length()-1;i>=0;--i){
            ans.push_back(res[i]);
        }
        return ans;
    }
    string multiply(string num1, string num2) {
        string res = "0";
        if(num1 == res || num2 == res) return res;
        string temp = num1;
        if(num1.length() > num2.length()){
            num1 = num2;
            num2 = temp;
        }
        string zeros;
        for(int i=num1.length()-1;i>=0;--i){
            string tempStr = multiplyOneChar(num2,num1[i]);
            tempStr = tempStr + zeros;
            res = add(res,tempStr);
            zeros += "0";

        }
        return res;
    }
};
```

先挖个坑：有更优的解法。

