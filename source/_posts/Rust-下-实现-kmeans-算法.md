---
title: "Rust 下 实现 kmeans 算法"
categories: [ "算法" ]
tags: [ "algorithms","rust","kmeans" ]
draft: false
slug: "implementing-kmeans-algorithm-under-rust"
date: "2019-07-23 15:51:00"
---

## 关于聚类

    聚类算法是这样的一种算法：给定样本数据Sample，要求将样本Sample中相似的数据聚到一类。有了这个认识之后，就应该了解了聚类算法要干什么了吧。说白了，就是归类。 
    首先，我们需要考虑的是，如何衡量数据之间的相似程度？比如说，有一群说不同语言的人，我们一般是根据他们的方言来聚类的（当然，你也可以指定以身高来聚类）。这里，语言的相似性（或者身高）就成了我们衡量相似的量度了。在考虑存在海量数据，如微博上各种用户的关系网，如何根据用户的关注和被关注来聚类，给用户推荐他们感兴趣的用户？这就是聚类算法研究的内容之一了。 


<!--more-->


    Kmeans就是这样的聚类算法中比较简单的算法，给定数据样本集Sample和应该划分的类数K，对样本数据Sample进行聚类，最终形成K个cluster，其相似的度量是某条数据i与中心点的”距离”(这里所说的距离，不止于二维)。

## 基本思想

KMeans算法的基本思想是初始随机给定K个簇中心，按照最邻近原则把待分类样本点分到各个簇。然后按平均法重新计算各个簇的质心，从而确定新的簇心。一直迭代，直到簇心的移动距离小于某个给定的值。

## 基本步骤 

K-Means聚类算法主要分为三个步骤： 
1，初始化k个聚类中心。 
2，计算出每个对象跟这k个中心的距离（相似度计算，这个下面会提到），假如x这个对象跟y这个中心的距离最小（相似度最大），那么x属于y这个中心。这一步就可以得到初步的k个聚类 。 
3，在第二步得到的每个聚类分别计算出新的聚类中心，和旧的中心比对，假如不相同，则继续第2步，直到新旧两个中心相同，说明聚类不可变，已经成功 。
复杂度分析 

时间复杂度：O(tKmn)，其中，t为迭代次数，K为簇的数目，m为记录数，n为维数 
空间复杂度：O((m+K)n)，其中，K为簇的数目，m为记录数，n为维数
初始质心的选择

    选择适当的初始质心是基本kmeans算法的关键步骤。常见的方法是随机的选取初始质心，但是这样簇的质量常常很差。处理选取初始质心问题的一种常用技术是：多次运行，每次使用一组不同的随机初始质心，然后选取具有最小SSE（误差的平方和）的簇集。这种策略简单，但是效果可能不好，这取决于数据集和寻找的簇的个数。 
     第二种有效的方法是，取一个样本，并使用层次聚类技术对它聚类。从层次聚类中提取K个簇，并用这些簇的质心作为初始质心。该方法通常很有效，但仅对下列情况有效： 
        （1）样本相对较小，例如数百到数千（层次聚类开销较大）； 
        （2）K相对于样本大小较小

    第三种选择初始质心的方法，随机地选择第一个点，或取所有点的质心作为第一个点。然后，对于每个后继初始质心，选择离已经选取过的初始质心最远的点。使用这种方法，确保了选择的初始质心不仅是随机的，而且是散开的。但是，这种方法可能选中离群点。此外，求离当前初始质心集最远的点开销也非常大。为了克服这个问题，通常该方法用于点样本。由于离群点很少（多了就不是离群点了），它们多半不会在随机样本中出现。计算量也大幅减少。 
    第四种方法是**使用canopy算法进行初始划分**。基于Canopy Method的聚类算法将聚类过程分为两个阶段： 
   Stage1：聚类最耗费计算的地方是计算对象相似性的时候，Canopy Method在第一阶段选择简单、计算代价较低的方法计算对象相似性，将相似的对象放在一个子集中，这个子集被叫做Canopy ，通过一系列计算得到若干Canopy，Canopy之间可以是重叠的，但不会存在某个对象不属于任何Canopy的情况，可以把这一阶段看做数据预处理。 
  Stage2：在各个Canopy 内使用传统的聚类方法(如K-means)，不属于同一Canopy 的对象之间不进行相似性计算。从这个方法起码可以看出两点好处：首先，Canopy 不要太大且Canopy 之间重叠的不要太多的话会大大减少后续需要计算相似性的对象的个数；其次，类似于K-means这样的聚类方法是需要人为指出K的值的，通过Stage1得到的Canopy 个数完全可以作为这个K值，一定程度上减少了选择K的盲目性。

Rust 实现 (kmeans):
```rust
// 宏来实现F64和F32的kmeans，而不写入任何内容两次或导入"num" crate
macro_rules! impl_kmeans {
    ($kind: ty, $modname: ident) => {
        // 因为我们不能在rust中重载方法，所以必须使用命名空间
        pub mod $modname {
            use std::$modname::INFINITY;

            /// 计算两个相同大小的矢量之间的平方偏差之和
            /// `x`, and `y`.
            fn distance(x: &[$kind], y: &[$kind]) -> $kind {
                x.iter()
                    .zip(y.iter())
                    .fold(0.0, |dist, (&xi, &yi)| dist + (xi - yi).powi(2))
            }

            /// 返回包含最靠近每个数据的质心的索引z<sub>i</sub> in {0, ..., K-1} of
            fn nearest_centroids(xs: &[Vec<$kind>], centroids: &[Vec<$kind>]) -> Vec<usize> {
                xs.iter()
                    .map(|xi| {
                        // 通过使用包含argmin和最小距离的元组进行折叠来查找argmin。
                        let (argmin, _) = centroids.iter().enumerate().fold(
                            (0_usize, INFINITY),
                            |(min_ix, min_dist), (ix, ci)| {
                                let dist = distance(xi, ci);
                                if dist < min_dist {
                                    (ix, dist)
                                } else {
                                    (min_ix, min_dist)
                                }
                            },
                        );
                        argmin
                    })
                    .collect()
            }

            /// 根据当前聚类重新计算质心
            fn recompute_centroids(
                xs: &[Vec<$kind>],
                clustering: &[usize],
                k: usize,
            ) -> Vec<Vec<$kind>> {
                let ndims = xs[0].len();

                // NOTE: 效率有点低，因为我们会扫描每个k形心的所有数据。
                (0..k)
                    .map(|cluster_ix| {
                        let mut centroid: Vec<$kind> = vec![0.0; ndims];
                        let mut n_cluster: $kind = 0.0;
                        xs.iter().zip(clustering.iter()).for_each(|(xi, &zi)| {
                            if zi == cluster_ix {
                                n_cluster += 1.0;
                                xi.iter().enumerate().for_each(|(j, &x_ij)| {
                                    centroid[j] += x_ij;
                                });
                            }
                        });
                        centroid.iter().map(|&c_j| c_j / n_cluster).collect()
                    })
                    .collect()
            }

            /// 使用k-means聚类将n个d维数据“xs”分配给“k”个聚类。
            pub fn kmeans(xs: Vec<Vec<$kind>>, k: usize) -> Vec<usize> {
                assert!(xs.len() >= k);

                // 我们不依赖于随机选择质心的起始点，而是通过在“xs”中选择均匀间隔的点来确定地选择它们。`
                let n_per_cluster: usize = xs.len() / k;
                let centroids: Vec<Vec<$kind>> =
                    (0..k).map(|j| xs[j * n_per_cluster].clone()).collect();

                let mut clustering = nearest_centroids(&xs, &centroids);

                loop {
                    let centroids = recompute_centroids(&xs, &clustering, k);
                    let new_clustering = nearest_centroids(&xs, &centroids);

                    // 循环，直到计算出新的质心后群集不再改变
                    if new_clustering
                        .iter()
                        .zip(clustering.iter())
                        .all(|(&za, &zb)| za == zb)
                    {
                        // 我们需要使用'return'来中断'loop`
                        return clustering;
                    } else {
                        clustering = new_clustering;
                    }
                }
            }
        }
    };
}

// generate code for kmeans for f32 and f64 data
impl_kmeans!(f64, f64);
impl_kmeans!(f32, f32);

#[cfg(test)]
mod test {
    use self::super::f64::kmeans;

    #[test]
    fn easy_univariate_clustering() {
        let xs: Vec<Vec<f64>> = vec![
            vec![-1.1],
            vec![-1.2],
            vec![-1.3],
            vec![-1.4],
            vec![1.1],
            vec![1.2],
            vec![1.3],
            vec![1.4],
        ];
        let clustering = kmeans(xs, 2);
        assert_eq!(clustering, vec![0, 0, 0, 0, 1, 1, 1, 1]);
    }

    #[test]
    fn easy_univariate_clustering_odd_number_of_data() {
        let xs: Vec<Vec<f64>> = vec![
            vec![-1.1],
            vec![-1.2],
            vec![-1.3],
            vec![-1.4],
            vec![1.1],
            vec![1.2],
            vec![1.3],
            vec![1.4],
            vec![1.5],
        ];
        let clustering = kmeans(xs, 2);
        assert_eq!(clustering, vec![0, 0, 0, 0, 1, 1, 1, 1, 1]);
    }

    #[test]
    fn easy_bivariate_clustering() {
        let xs: Vec<Vec<f64>> = vec![
            vec![-1.1, 0.2],
            vec![-1.2, 0.3],
            vec![-1.3, 0.1],
            vec![-1.4, 0.4],
            vec![1.1, -1.1],
            vec![1.2, -1.0],
            vec![1.3, -1.2],
            vec![1.4, -1.3],
        ];
        let clustering = kmeans(xs, 2);
        assert_eq!(clustering, vec![0, 0, 0, 0, 1, 1, 1, 1]);
    }

    #[test]
    fn high_dims() {
        let xs: Vec<Vec<f64>> = vec![
            vec![-2.7825343, -1.7604825, -5.5550113, -2.9752946, -2.7874138],
            vec![-2.9847919, -3.8209332, -2.1531757, -2.2710119, -2.3582877],
            vec![-3.0109320, -2.2366132, -2.8048492, -1.2632331, -4.5755581],
            vec![-2.8432186, -1.0383805, -2.2022826, -2.7435962, -2.0013399],
            vec![-2.6638082, -3.5520086, -1.3684702, -2.1562444, -1.3186447],
            vec![1.7409171, 1.9687576, 4.7162628, 4.5743537, 3.7905611],
            vec![3.2932369, 2.8508700, 2.5580937, 2.0437325, 4.2192562],
            vec![2.5843321, 2.8329818, 2.1329531, 3.2562319, 2.4878733],
            vec![2.1859638, 3.2880048, 3.7018615, 2.3641232, 1.6281994],
            vec![2.6201773, 0.9006588, 2.6774097, 1.8188620, 1.6076493],
        ];

        let clustering = kmeans(xs, 2);
        assert_eq!(clustering, vec![0, 0, 0, 0, 0, 1, 1, 1, 1, 1]);
    }
}
```

参考：
- [https://www.jb51.net/article/135037.htm](https://www.jb51.net/article/135037.htm)
- [https://www.cnblogs.com/lliuye/p/9144312.html](https://www.cnblogs.com/lliuye/p/9144312.html)