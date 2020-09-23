---
title: Golang学习 - sort 包
date: 2016-05-29 14:26:00
updated: 2017-02-21 14:28:02
tags: 
- linux
- ls
categories: 
- linux

---
```golang
// 满足 Interface 接口的类型可以被本包的函数进行排序。
type Interface interface {
    // Len 方法返回集合中的元素个数
    Len() int
    // Less 方法报告索引 i 的元素是否比索引 j 的元素小
    Less(i, j int) bool
    // Swap 方法交换索引 i 和 j 的两个元素的位置
    Swap(i, j int)
}

// 对 data 进行排序（不保证相等元素的相对顺序不变）
// data 默认为升序，执行 Reverse 后为降序。
func Sort(data Interface)

// 对 data 进行排序（保证相等元素的相对顺序不变）
// data 默认为升序，执行 Reverse 后为降序。
func Stable(data Interface)


<!--more-->


// 将 data 的排序动作更改为降序，Reverse 并不改变元素顺序，只改变排序行为。
// 更改操作不可逆，更改后的对象不可以再次 Reverse。
func Reverse(data Interface) Interface

// 判断 data 是否已经排序
// 未执行 Reverse 的必须为升序，执行 Reverse 的必须为降序
func IsSorted(data Interface) bool


// 示例
func main() {
	i := []int{3, 7, 1, 3, 6, 9, 4, 1, 8, 5, 2, 0}
	a := sort.IntSlice(i)
	fmt.Println(sort.IsSorted(a)) // false
	sort.Sort(a)
	fmt.Println(a) // [0 1 1 2 3 3 4 5 6 7 8 9]
	fmt.Println(sort.IsSorted(a), "\n") // true

	b := sort.IntSlice{3}
	fmt.Println(sort.IsSorted(b), "\n") // true

	// 更改排序行为
	c := sort.Reverse(a)
	fmt.Println(sort.IsSorted(c)) // false
	fmt.Println(c) // &{[0 1 1 2 3 3 4 5 6 7 8 9]}
	sort.Sort(c)
	fmt.Println(c) // &{[9 8 7 6 5 4 3 3 2 1 1 0]}
	fmt.Println(sort.IsSorted(c), "\n") // true

	// 再次更改排序行为
	d := sort.Reverse(c)
	fmt.Println(sort.IsSorted(d)) // false
	sort.Sort(d)
	fmt.Println(d) // &{0xc42000a3b0}
	fmt.Println(sort.IsSorted(d)) // true
	fmt.Println(d) // &{0xc42000a3b0}
}

// 对 a 进行升序排列
func Ints(a []int)

// 判断 a 是否为升序排列
func IntsAreSorted(a []int) bool

// 搜索 a 中值为 x 的索引，如果找不到，则返回最接近且大于 x 的值的索引，
// 可能是 len(a)。
func SearchInts(a []int, x int) int

// 示例
func main() {
	a := []int{3, 9, 1, 6, 4, 2, 8, 2, 4, 5, 3, 0}
	sort.Ints(a)
	fmt.Println(a) // [0 1 2 2 3 3 4 4 5 6 8 9]
	fmt.Println(sort.IntsAreSorted(a)) // true
	i := sort.SearchInts(a, 7)
	fmt.Println(a[i]) // 8
}


// 功能同上，类型不同
func Float64s(a []float64)
func Float64sAreSorted(a []float64) bool
func SearchFloat64s(a []float64, x float64) int

// 功能同上，类型不同
func Strings(a []string)
func StringsAreSorted(a []string) bool
func SearchStrings(a []string, x string) int

// 实现了 sort.Interface 接口的 []int 类型
type IntSlice []int
func (p IntSlice) Len() int           // 接口方法
func (p IntSlice) Less(i, j int) bool // 接口方法
func (p IntSlice) Swap(i, j int)      // 接口方法

// 对 p 进行升序排列
func (p IntSlice) Sort()

// 搜索 p 中值为 x 的索引，如果找不到，则返回最接近且大于 x 的值的索引，
// 可能是 len(a)。
func (p IntSlice) Search(x int) int

// 示例
func main() {
	a := sort.IntSlice{3, 7, 1, 3, 6, 9, 4, 1, 8, 5, 2, 0}
	a.Sort()
	fmt.Println(a)                // [0 1 1 2 3 3 4 5 6 7 8 9]
	fmt.Println(sort.IsSorted(a)) // true
	i := a.Search(6)
	fmt.Println(i, a[i])          // 8 6
}

// 功能同上，类型不同
type Float64Slice []float64
func (p Float64Slice) Len() int
func (p Float64Slice) Less(i, j int) bool
func (p Float64Slice) Swap(i, j int)
func (p Float64Slice) Sort()
func (p Float64Slice) Search(x float64) int

// 功能同上，类型不同
type StringSlice []string
func (p StringSlice) Len() int
func (p StringSlice) Less(i, j int) bool
func (p StringSlice) Swap(i, j int)
func (p StringSlice) Sort()
func (p StringSlice) Search(x string) int

// Search 采用二分法搜索，在小于 n 的索引中查找最小的满足 f(索引) 的值。返
// 回找到的索引，如果没有符合要求的索引，则返回 n。
func Search(n int, f func(int) bool) int

// 示例
func main() {
	a := sort.StringSlice{"hello", "world", "golang", "sort", "nice"}
	a.Sort() // 二分法必须先排序
	// 获取首字母大于 n 的元素中最小的
	i := sort.Search(len(a), func(i int) bool {
		return len(a[i]) > 0 && a[i][0] > 'n'
	})
	// 显示找到的元素
	fmt.Println(a[i]) // sort
}
```