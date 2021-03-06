---
title: leetcode problem 22. 括号生成
tags: 算法
categories:
  - leetcode
  - leetcode 0001-0050
abbrlink: baa2f2c0
date: 2021-04-15 22:40:16
---

# <center>leetcode problem 22. 括号生成</center>

## 链接

https://leetcode-cn.com/problems/generate-parentheses/



## 题目描述

数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

 

示例 1：

输入：n = 3
输出：\[\"((()))\",\"(()())\",\"(())()\",\"()(())\",\"()()()\"\]
示例 2：

输入：n = 1
输出：\[\"()\"\]


提示：

1 <= n <= 8



## 解法

### 1.

使用递归回溯的方式，在每次递归调用中在条件允许的情况下分别处理添加左括号或者添加右括号的情况，获得每一种括号结果。注意，右括号必须保证在已经有左括号的前提下，才可以进行递归求解，不然不是有效的括号组合，所以在递归左括号的时候，给可用的右括号个数+ 1，表明可以使用的右括号+1，在可用的左括号个数和右括号个数都为0的时候，表明已经获得了一种结果，保存该结果。

<img src="leetcode-problem-22/p22_1.jpg" alt="p22_1" style="zoom: 50%;" />

#### 代码

```c++
class Solution {
public:
    std::vector<std::string> GenerateParenthesis(const int n)
    {
        std::vector<std::string> results;

        this->Recursive(n, 0, "", results);

        return results;
    }

private:
    void Recursive(const int left_num, const int right_num, const std::string& str, std::vector<std::string>& results)
    {
        if (left_num > 0)
        {
            this->Recursive(left_num - 1, right_num + 1, str + '(', results);
        }

        if (right_num > 0)
        {
            this->Recursive(left_num, right_num - 1, str + ')', results);
        }

        if (left_num == 0 && right_num == 0)
        {
            if (str != "")
            {
                results.push_back(str);
            }
        }
    }
}
```

### 2.

递归的另一种方式，也使用了动态规划的方法。将n拆解成一个一个的小问题，然后计算小问题的结果，组合成大问题的结果。如果要组成n对括号，从较小的规模开始想起，n = 0时，结果只有\"\"，n = 1时，结果只有\"()\"，n = 2时，比n = 1增加了一对括号，假设我们现在单独把这对括号拿出来\"()\"，n还剩1，由于n = 1时，结果就是\"()\"，现在就变成了2对\"()\"相互组合的情况，也可以看作是2个n = 1的组合，\"()\"可以放在\"()\"中间，也可以放在\"()\"的左右(结果是相同的)，所以n = 2时，结果为\"()()\"和\"(())\"。

通过这样的性质，对任意的n，我们可以先取出当前n中的1对括号，对于剩下的n-1对括号，使用成对组合的方式，\[0， n-1\]，\[1，n-2\]，\[2，n-3\]...成对的进行递归调用，获取两组结果作为左右两侧的结果，遍历左右两侧的结果，与单独提出的一对括号，进行组合，得出公式\"(\" + left + \")\" + right，其中left和right部分可以为\"\"。以n = 2举例的话，n - 1 = 1，两种组合\[0，1\]，\[1，0\]，假设dp\[i\]为i = n时的结果，dp\[0\] = \[\"\"\]，dp\[1\] = \[\"()\"\]，n = 2的两种结果为\"(\" + \"\" + \")\" + \"()\" 和 \"(\" + \"()\" + \")\" + \"\"，即为\"()()\"和\"(())\"

<img src="leetcode-problem-22/p22_2.jpg" alt="p22_2" style="zoom:50%;" />

#### 代码

```c++
class Solution {
public:
    std::vector<std::string> GenerateParenthesis(const int n)
    {
        std::map<int, std::vector<std::string>> dp;

        this->RecursiveOther(n, dp);

        return dp[n];
    }

private:
    std::vector<std::string>* RecursiveOther(const int n, std::map<int, std::vector<std::string>>& dp)
    {
        if (dp.find(n) != dp.end())
        {
            return &dp[n];
        }

        std::vector<std::string> res;

        if (n == 0)
        {
            res.emplace_back("");
        }
        else
        {
            for (int i = 0; i < n; ++i)
            {
                std::vector<std::string>* left_res = this->RecursiveOther(i, dp);
                std::vector<std::string>* right_res = this->RecursiveOther(n - i - 1, dp);

                for (int j = 0; j < left_res->size(); ++j)
                {
                    for (int k = 0; k < right_res->size(); ++k)
                    {
                        res.emplace_back("(" + (*left_res)[j] + ")" + (*right_res)[k]);
                    }
                }
            }
        }

        dp[n] = res;

        return &dp[n];
    }
};
```

