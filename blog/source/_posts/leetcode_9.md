---
 title: Leetcode.9 Palindrome Number 解题报告(Go)
 date: 2019-02-28 11:28:00
 tags: 
---

## Description

Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.

### Example 1:
```
Input: 121 
Output: true
```

### Example 2:
```
Input: -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```
### Example 3:
```
Input: 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```
### Follow up:

Coud you solve it without converting the integer to a string?

## Solution

### Solution 1:
最直接的思路，就是将int转换为string，然后判断回文字符串就可以，判断字符串就用两个指针left和right分别次从input的头尾向中间收缩，每次收缩都比较left和right的值，有不同的直接返回false，否则直到指针相遇则返回true
当然，如果input是负数的话直接返回false即可

```
func isPalindrome(x int) bool {
	if	x < 0 {
		return false
	}
	
	s := strconv.Itoa(x)
	length := len(s)
	
	//left:s[i] right:s[length-1-i]
	for i := 0; i < length / 2; i++ {
		if s[i] != s[length-1-i]{
			return false
		}
	}
	
	return true
}
```

### Solution 2:
注意到Follow up中说是否可以不使用类似Itoa的操作解决这个问题，第一反应就是通过除法和取模操作依次获取input的第一位和最后一位，依次向中间收缩，例如 input是12321

1. input%10 取得最后一位right = 1
2. input/10000 (10000即pow(10, 4-i*2), 4是数字长度-1, i为比较次数，当前为0,每次比较会少两位) 取得第一位 left = 1，判断left、right 相同则继续比较, input = input%10000/10, 此时input = 232
3. i从0递增到length/2,重复以上过程即可

话不多说，撸码看看

```
func isPalindrome(x int) bool {
	if x < 0 {
		return false
	}

	length := 0
        backup := x
	for backup > 0 {
		backup /= 10
		length++
	}

	for i := 0; i < length/2; i++ {
		right := x % 10
		gap := int(math.Pow10(length - 1 - i*2))
		left := x / gap
   	        fmt.Printf("left:%d, right:%d\n", left, right)
		if left != right {
			return false
		}

		x = (x % gap) / 10
	}
	
	return true
}
```
Submit后：

Runtime: 148 ms, faster than 8.34% of Go online submissions for Palindrome Number.

Memory Usage: 5.8 MB, less than 19.47% of Go online submissions for Palindrome Number.

运行速率感人…… 于是在leecode讨论区瞻仰一下大神们的解法，结果看到了下面这个惊为天人的解法

### Solution 3:

```
func isPalindrome(x int) bool {
    if x < 0 || (x != 0 && x % 10 == 0) {
		return false
	}

	rest := 0
	for x > rest  {
		rest = rest*10 + x%10
		x /= 10	
		// log添加处
	}

	return rest/10 == x || rest == x
}
```
在循环中打印rest和x的值,输入12321

```
rest:1, x:1232
rest:12, x:123
rest:123, x:12
```

其实可以看到，这个思路是把x的右半边逐步“割掉”，在每次“割”的时候会同时计算出割舍的那部分的回文值，直到“割掉”的部分大等于剩余的部分再停止。
该方法循环次数和方案2基本一致，但区别在于方案二单次循环中的计算量远大于该方案，综合三个方案来看的话，最后这个方案是最精巧也是运行效率最高的。


