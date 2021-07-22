# if判断与switch简化

```go
//if条件判断
func main() {
	age := 61
	if age < 18 {
		fmt.Println("还未成年")
	} else if age <= 60 {
		fmt.Println("成年了！")
	} else {
		fmt.Println("退休啦！")
	}

	switch n := 1; n {
	case 1:
		fmt.Println("大拇指")
	case 2:
		fmt.Println("食指")
	case 3:
		fmt.Println("中指")
	case 4:
		fmt.Println("无名指")
	case 5:
		fmt.Println("小拇指")
	default:
		fmt.Println("无效值")
	}

	//判断奇偶
	switch m := 4; m {
	case 1, 3, 5, 7, 9:
		fmt.Println("奇数")
	case 0, 2, 4, 6, 8:
		fmt.Println("偶数")
	default:
		fmt.Println(m)
	}
}
```

# for循环

```go
//for循环
func main() {
	//基本格式：for 初始语句;条件表达式;结束语句{}
	for i := 0; i < 5; i++ {
		fmt.Println(i)
	}

	//不写初始语句
	m := 0
	for ; m > -5; m-- {
		fmt.Println(m)
	}

	//初始语句和结束语句都不写
	n := 6
	for n < 11 {
		fmt.Println(n)
		n++
	}

	//无限循环
	//for {
	//	fmt.Println("123")
	//}

	//for range循环：遍历字符串、数组、切片返回索引和值
	a := "abdd好的daf"
	for i, v := range a {
		fmt.Printf("%d %c\n", i, v)
	}

	//当a等于5时退出for循环
	for a := 0; a < 10; a++ {
		if a == 5 {
			break
		}
		fmt.Println(a)
	}

	//当b等于5时跳过本次循环
	for b := 0; b < 10; b++ {
		if b == 5 {
			continue
		}
		fmt.Println(b)
	}

	//goto-laber标签跳出多层循环
	for c := 0; c < 10; c++ {
		for d := 0; d <= c; d++ {
			if d == 5 {
				goto xx
			}
			fmt.Printf("%v,%v\n", c, d)
		}
	}
xx: //laber标签
	fmt.Println("over")
}
```

## 九九乘法表

```go
func main() {
	for i := 1; i < 10; i++ {
		for j := 1; j <= i; j++ {
			fmt.Print(j, "*", i, "=", i*j, "\t")
		}
		fmt.Println()
	}
}
```

