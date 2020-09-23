---
title: 算法 - Golang是、指数质数（素数）
date: 2014-11-10 16:11:00
updated: 2017-02-21 16:22:20
tags: 
- ubuntu
- swap
categories: 
- linux

---
```
package main

import "time"
import "os"
import "fmt"
import "math"


<!--more-->


func main() {
	timeStart := time.Now()
	// 获取50万以内的质数（普通算法，个数41538，耗时8.7秒）
	prime := GetPrimeLimit1(500000)
	print("个数", len(prime), "，")
	ShowTimeUsed(timeStart)
	// ShowPrime(prime)               // 显示结果
	// SavePrime("prime1.txt", prime) // 保存结果

	timeStart = time.Now()
	// 获取前5万个质数(普通算法，个数50000，耗时12.6秒)
	prime = GetPrimeCount1(50000)
	print("个数", len(prime), "，")
	ShowTimeUsed(timeStart)
	// ShowPrime(prime)               // 显示结果
	// SavePrime("prime2.txt", prime) // 保存结果

	timeStart = time.Now()
	// 获取1亿以内的质数（筛选算法，个数5761455，耗时1.4秒）
	prime = GetPrimeLimit2(100000000)
	print("个数", len(prime), "，")
	ShowTimeUsed(timeStart)
	// ShowPrime(prime)               // 显示结果
	// SavePrime("prime1.txt", prime) // 保存结果

	timeStart = time.Now()
	// 判断一个100亿亿级的大数是否是质数（耗时11.7秒）
	print(IsPrime(9223372036854775783), "，")
	ShowTimeUsed(timeStart)
}

// 获取指定范围内的质数（普通算法）
func GetPrimeLimit1(limit int) []int {
	// 先处理2，再处理2以上的质数
	prime := []int{2}
intLoop:
	// 2以上的质数都必须是奇数
	for i := 3; i <= limit; i += 2 {
		// 与之前的所有质数（除了2）相除，能整除则不是质数
		q := int(math.Sqrt(float64(i)))
		for j := 1; j < len(prime); j++ {
			if prime[j] <= q && i%prime[j] == 0 {
				continue intLoop
			}
		}
		prime = append(prime, i)
	}
	return prime
}

// 获取指定数量的质数（普通算法）
func GetPrimeCount1(count int) []int {
	// 先处理2，再处理2以上的质数
	prime := []int{2}
intLoop:
	// 2以上的质数都必须是奇数
	for i := 3; ; i += 2 {
		// 与之前的所有质数（除了2）相除，能整除则不是质数
		q := int(math.Sqrt(float64(i)))
		for j := 1; j < len(prime); j++ {
			if prime[j] <= q && i%prime[j] == 0 {
				continue intLoop
			}
		}
		prime = append(prime, i)
		if len(prime) >= count {
			break
		}
	}
	return prime
}

// 获取指定范围内的质数（筛选算法）
// 此方法转自http://blog.csdn.net/liukehua123/article/details/5482854
func GetPrimeLimit2(num int) []int {
	primeFlag := make([]bool, num, num)
	primeFlag[2] = true
	for i := 3; i < num; i += 2 {
		primeFlag[i] = true
	}
	for i := 3; i <= int(math.Sqrt(float64(num))); i++ {
		if primeFlag[i] {
			for j := i + i; j < num; j += i {
				primeFlag[j] = false
			}
		}
	}
	prime := []int{}
	for i := 0; i < num; i++ {
		if primeFlag[i] {
			prime = append(prime, i)
		}
	}
	return prime
}

// 判断一个数是否是质数
// 此方法转自http://blog.csdn.net/l04205613/article/details/6025118
func IsPrime(num int) bool {
	p := []int{4, 2, 4, 2, 4, 6, 2, 6}
	var j, q int
	i := 7
	if num == 1 {
		return false
	}
	if num == 2 || num == 3 || num == 5 {
		return true
	}
	if num%2 == 0 || num%3 == 0 || num%5 == 0 {
		return false
	}
	q = int(math.Sqrt(float64(num)))
	for i <= q {
		for j = 0; j < 8; j++ {
			if num%i == 0 {
				return false
			}
			i += p[j]
		}
		if num%i == 0 {
			return false
		}
	}
	return true
}

// 显示函数执行时间
func ShowTimeUsed(timeStart time.Time) {
	println("耗时：", time.Now().Sub(timeStart).String())
}

// 显示结果
func ShowPrime(prime []int) {
	for index, value := range prime {
		if (index+1)%10 == 0 { // 每10个质数一行
			println(value)
		} else if index+1 == len(prime) { // 最后一个质数后面不添加逗号
			print(value)
		} else {
			print(value, ", ")
		}
	}
	println()
}

// 保存结果
func SavePrime(filename string, prime []int) {
	f, err := os.OpenFile(filename, os.O_CREATE|os.O_TRUNC|os.O_RDWR, 0660)
	if err != nil {
		println("文件打开错误")
		return
	}
	defer f.Close()

	for index, value := range prime {
		if (index+1)%10 == 0 { // 每10个质数一行
			f.WriteString(fmt.Sprintf("%d\n", value))
		} else if index+1 == len(prime) { // 最后一个质数后面不添加逗号
			f.WriteString(fmt.Sprintf("%d", value))
		} else {
			f.WriteString(fmt.Sprintf("%d, ", value))
		}
	}
}
```