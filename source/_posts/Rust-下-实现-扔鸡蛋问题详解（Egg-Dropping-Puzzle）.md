---
title: "Rust 下 实现 扔鸡蛋问题详解（Egg Dropping Puzzle）"
categories: [ "算法" ]
tags: [ "algorithms","rust","扔鸡蛋问题" ]
draft: false
slug: "implementation-of-egg-dropping-puzzle-under-rust"
date: "2019-07-19 11:24:00"
---

## 题目

**一幢 200 层的大楼，给你两个鸡蛋，如果在第 n 层扔下鸡蛋，鸡蛋不碎，那么从第 n-1 层扔鸡蛋，都不碎。这两只鸡蛋一模一样，不碎的话可以扔无数次，且鸡蛋在0层不会碎。设计一种策略能保证可以测出鸡蛋恰好会碎的楼层，且如果该策略是最坏的情况所扔次数最少。**


<!--more-->


## 解决问题

### 思维分析

**首先我们需要确定最坏情况是什么样子的。**
 假设n是我们的决定第一次尝试的楼层，第一个鸡蛋从n层开始扔。

1. 如果没有坏，那么我们就可以从[n+1,100]这个区间扔鸡蛋了，这个时候怎么扔就是我们需要考虑的策略。
2. 但是如果运气比较背，鸡蛋坏了_!那么这个时候我们就只有一个鸡蛋了，所以为了满足我们要测出恰好会碎的楼层，我们只能从1楼一直扔到n-1楼。这个时候我们的最坏情况就是n次。

**鸡蛋没有坏该怎么选择第二次以及以后扔鸡蛋的策略呢？**
 由于没有碎，所以第n层对于我们而言和第0层是一样一样的，所以我们不能采用一层一层增加的方式扔鸡蛋！因为有两个鸡蛋，比较任性。下面提出几个合理的假设，然后分析：

1. 增加n层：碎了的话，最坏情况就是我们还要扔2n-n-1+2=n+1次，这个时候最坏情况比第一次还坏，而且照这个趋势下去，最坏情况只会越来越坏，是不可控的，所以这种策略抛弃。
2. 增加大于n层：和增加n层一样，如果第一个鸡蛋碎了那最坏情况就是越来越坏，且比增加n层更坏（可以自己做个简单的算术推导一下）。
3. 增加小于n层：随着n的不断减小，最坏情况下需要扔的次数恒定为n是不会变的（因为第一次就碎了，对于这种情况就是最坏情况）。那么我们需要考虑的是如何使扔的次数最少，想想看，果断取n-1，这样既保证了快速找到刚好碎的楼层，又保证了最坏情况扔的次数最少。
4. 所以我们最后的策略是增加n-1层。

以上就是一个分析过程，对于第三次，第四次....都可以递归的进行分析。
 由于最好情况是第一个鸡蛋一直扔到了100层，而100层与n之间是有一个函数关系的，下面就可以列出一个等式：
 n+(n-1)+(n-2)+...+1=100=n(n+1)/2
 所以n约等于14
 所以第一次从第十四层开始扔，最坏情况就是第一次就碎了，然后需要从1楼开始一层一层的扔，共扔14次。

### 编程解答

以上是分析然后数学解答，还有一种代码做法。
 **设f(n, m)为n层楼, m个蛋所需次数, 那么它就成了一道DP题**

实现代码（egg_dropping.rs）:
```rust
/// # Egg Dropping Puzzle

/// `egg_drop(eggs, floors)` 返回确定鸡蛋掉落时不会破碎的最高楼层所需要扔的次数
///
/// 假设: n > 0
pub fn egg_drop(eggs: u32, floors: u32) -> u32 {
    assert!(eggs > 0);

    // 处理边缘情况(可选)
    if eggs == 1 || floors == 0 || floors == 1 {
        return floors;
    }

    let eggs_index = eggs as usize;
    let floors_index = floors as usize;

    // 在2D Vec 存储子问题的解决方案,
    // 其中 egg_drops[i][j] 表示 第i个鸡蛋和第j层 鸡蛋掉落问题的解决方案。
    let mut egg_drops: Vec<Vec<u32>> = vec![vec![0; floors_index + 1]; eggs_index + 1];

    // 分配解决方案： egg_drop(n, 0) = 0, egg_drop(n, 1) = 1
    for egg_drop in egg_drops.iter_mut().skip(1) {
        egg_drop[0] = 0;
        egg_drop[1] = 1;
    }

    // 分配解决方案： egg_drop(1, k) = k
    for j in 1..=floors_index {
        egg_drops[1][j] = j as u32;
    }

    // 利用最优子结构性质完成解向量
    for i in 2..=eggs_index {
        for j in 2..=floors_index {
            egg_drops[i][j] = std::u32::MAX;

            for k in 1..=j {
                let res = 1 + std::cmp::max(egg_drops[i - 1][k - 1], egg_drops[i][j - k]);

                if res < egg_drops[i][j] {
                    egg_drops[i][j] = res;
                }
            }
        }
    }

    egg_drops[eggs_index][floors_index]
}

#[cfg(test)]
mod tests {
    use super::egg_drop;

    #[test]
    fn zero_floors() {
        assert_eq!(egg_drop(5, 0), 0);
    }

    #[test]
    fn one_egg() {
        assert_eq!(egg_drop(1, 8), 8);
    }

    #[test]
    fn eggs2_floors2() {
        assert_eq!(egg_drop(2, 2), 2);
    }

    #[test]
    fn eggs3_floors5() {
        assert_eq!(egg_drop(3, 5), 3);
    }

    #[test]
    fn eggs2_floors10() {
        assert_eq!(egg_drop(2, 10), 4);
    }

    #[test]
    fn eggs2_floors36() {
        assert_eq!(egg_drop(2, 36), 8);
    }

    #[test]
    fn large_floors() {
        assert_eq!(egg_drop(2, 100), 14);
    }
}
```
参考：

- [https://www.jianshu.com/p/896cfbca0d3e](https://www.jianshu.com/p/896cfbca0d3e)
- [https://blog.csdn.net/joylnwang/article/details/6769160](https://blog.csdn.net/joylnwang/article/details/6769160)