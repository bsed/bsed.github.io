---
title: 组合算法（C++ 实现
date: 2017-04-10 20:55:00
updated: 2017-04-10 20:57:00
tags: 
- linux
- ubuntu
- bbr
categories: 
- linux

---
# 组合算法（C++ 实现

排列组合是高中数学中比较难的部分。用我高中数学老师的话说，叫做「会者不难，难者不会」，说是排列组合基本靠悟。

高中数学中，排列组合相关的题目，重点是求在某个场景下，排列/组合的可能数是多少，并不要求学生列出这些可能的排列/组合分别是什么。在实际工程应用中，有些场景却会有这样的需求。

在 Python 中，标准库 `itertools` 提供了排列、组合、笛卡尔积的方法。然而在 C++ 中，标准库只提供了 `next_permutation` 和 `prev_permutation`，通常来说不太够用。

这里，我们给出两种思路的算法。


<!--more-->


## 二进制辅助的方法

我们先来讨论一下非递归的方法。

对于组合来说，对每个元素是否选取，只有「选」和「不选」两种状态。因此，我们可以用一串二进制，来表示「选与不选」。例如：`10110` 表示五选三时，第一位、第三位和第四位被选择，剩下两位则不选。

接下来，我们要非重复、不遗漏地找到所有可能的组合方式，就有必要找到某种顺序。这种顺序应该满足：

1. 不重复
2. 不遗漏
3. 有某种可以观察的良好性质
4. 在计算机上容易实现

不难想到，如果我们以二进制来表示一种组合状态，那么它就对应着一个十进制数。比如五选三时，就是要求解五位二进制数中，有三个数位是 `1` 的全部可能性。要不重复不遗漏地找出这些可能性，我们可以很简单地定义这样的顺序：找到满足五位二进制数中有三个数位是 `1` 的数字的升序排列。

我们首先来看看一个已经排序好的序列：

```
11100 -> 7   (左边表示低位，即实际的二进制数应是 00111。下同)
11010 -> 11
10110 -> 13
01110 -> 14
11001 -> 19
10101 -> 21
01101 -> 22
10011 -> 25
01011 -> 26
00111 -> 28

```

不难发现，这实际上是一个「逐位移动」的问题。想要得到升序中相邻的数，显而易见，应该将低位的 `1` 与相邻的高位的 `0` 交换位置。也就是说，提高了这个 `1` 的权重。同时，应该将比发生换位的 `1` 低位的所有的 `1` 挪到最低位。

放进我们的示例当中，就有伪代码：

```
Given:
    int: choose, from
Outout:
    vector<string>: result
---
working <- "1" * choose + "0" * (from - choose)
result.append(working)
while (found(10))
    swap (found(10))
    sort (working, working + found(10), reverse = true)
    result.append(working)

```

这样，我们可以写出相应的 C++ 代码：

```
binomial.cc#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;
vector<string>& combination
  (vector<string>& res, const size_t& choose, const size_t& from);
bool compare (const char& lhs, const char& rhs);
int main () {
  vector<string> res;
  const size_t choose = 3, from = 5;
  combination (res, choose, from);
  for (size_t i = 0; i != res.size(); ++i) {
    cout << res[i] << '\t';
    for (size_t j = 0; j != from; ++j) {
      if (res[i][j] == '1') {
        cout << j + 1 << ' ';
      }
    }
    cout << endl;
  }
  return 0;
}
vector<string>& combination
  (vector<string>& res, const size_t& choose, const size_t& from) {
  string wk = string (choose, '1') + string (from - choose, '0');
  res.push_back (wk);
  size_t found = string::npos;
  while ((found = wk.find("10")) != string::npos) {
    // 1. swap found
    wk[found] ^= wk[found + 1];
    wk[found + 1] ^= wk[found];
    wk[found] ^= wk[found + 1];
    // 2. sort before
    sort (wk.begin(), wk.begin() + found, compare);
    res.push_back (wk);
  }
  return res;
}
bool compare (const char& lhs, const char& rhs) {
  return lhs > rhs;
}

```

这里我用 `std::string` 实现了算法。实际上，可以用更快的 `cstring` 来实现（因为它实际是数组）。

## 平凡的递归解法

现在我们来看看求解组合的递归算法。

首先，我们回忆一下高中数学中提到的组合数递推关系：

Cmn=Cm−1n−1+Cmn−1.(1)(1)Cnm=Cn−1m−1+Cn−1m.

在高中讲到组合数时，老师一定根据组合数的定义，手工推导了这一递推关系。但是，当时老师并不一定讲了这个递推关系的内在含义。

实际上，这个递推关系有着明确的意义。我们考虑从 nn 个物件中取出 mm 个物件的情况（m<nm<n）。对第一个物件来说，我们要不然选它，然后在剩下的 n−1n−1 个物件中取出 m−1m−1 个物件；要不然不选它，然后干脆地在剩下的 n−1n−1 个物件中取出 mm 个物件。

将这两种情况合起来，就得到公式 [1](http://liam0205.me/2016/01/31/binomial-in-cpp/#mjx-eqn-eqbinomial-re)1 了。

上述分析给出了明确的递归思路，那么不难得到下面的代码：

```
binomial.cc
```