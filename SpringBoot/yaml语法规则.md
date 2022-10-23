# yaml语法

## 1.基本语法

左对齐同层级，k:(空格)v，属性和值大小写敏感

## 2.值的写法

普通的值：字符串不用单双引号，单引号原样输出，双引号里的转义字符会被解析

对象（属性和值）：在下一行来写对象的属性和值的关系

```yaml
friends:
	lastName: zhangsan
	age: 20
```

​	或者

```
friends: {lastName: zhangsan,age: 20}
```

数组：用-(空格)值来表示数组中的一个元素

```yaml
pets:
	- cat
	- dog
	- pig
```

或者

```
pets: [cat,dog,pig]
```

