***内存对齐 aligned address***


*内存访问粒度*

```shell
CPU是按照'块(chunk)'来读写内存的，块的大小可以是2bytes, 4bytes, 8bytes, 16bytes甚至是32bytes. 这个CPU访问内存采用的块的大小，我们可以称为'内存访问粒度'
```
----

***位运算***

```yaml
&      位运算 AND
|      位运算 OR
^      位运算 XOR
&^     位清空 (AND NOT)
<<     左移
>>     右移
```

- *^XOR*

XOR是不进位加法计算，也就是异或计算
```shell
0000 0100
0000 0010
---
0000 0110 == 6 
```
- *&^*
(AND NOT)位清空运算和被运算变量位置有关系

    example 1 
```shell
# example 1 
    x := 2
    y := 4
    fmt.Println(x&^y)

    result == 2
计算x&^y 首先我们先换算成2进制  
    0000 0010 
    &^ 
    0000 0100 
    = 
    0000 0010 
    如果y bit位上的数是0则取x上对应位置的值， 如果y bit位上为1则取结果位上取0
```
- *>>|<<*

```shell

```
----
