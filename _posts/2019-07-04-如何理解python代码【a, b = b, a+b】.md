如何理解下面这串python代码和它的运行结果呢？
```python
a, b = 0, 1
a, b = b, a+b
```
这串代码的运行结果：a=1,b=1。为了进一步理解在python内部发生了什么，引入`dis`库分析该代码的汇编语句。
```python
import dis
def func():
    a, b = 0, 1
    a, b = b, a+b
 dis.dis(func)
```
运行结果如下：
```
  9           0 LOAD_CONST               3 ((0, 1))
              2 UNPACK_SEQUENCE          2
              4 STORE_FAST               0 (a)
              6 STORE_FAST               1 (b)

 10           8 LOAD_FAST                1 (b)
             10 LOAD_FAST                0 (a)
             12 LOAD_FAST                1 (b)
             14 BINARY_ADD
             16 ROT_TWO
             18 STORE_FAST               0 (a)
             20 STORE_FAST               1 (b)
             22 LOAD_CONST               0 (None)
             24 RETURN_VALUE
```
上面的汇编语句中，`STORE_*`代表存入堆栈，`LOAD_*`代表读出堆栈。`ROT_TWO`代表交换堆栈最后两位。因此从结论出发，在python中，赋值语句的右边表达式是最先被运算的，并且运算结果都会暂存到堆栈中，而不受python的指针影响；在右边运算结束后，才会进行左边表达式的运算，不同于右边表达式，它是按照从左到右实时执行赋值语句的。举个例子，这个例子来源于知乎用户[@南葱](https://www.zhihu.com/people/shellaylee)的专栏[Python在unpacking上的一个小陷阱](https://zhuanlan.zhihu.com/p/25436739)中所写。
```python
j = 0, m = [1, 3, 5] 
j, m[j] = m[j], 99

print(j)    # 1
print(m)    # [1, 99, 5]
```
首先进行右边的运算，`j`和`m[j]`的运算结果为1和99；接着进行左边的运算，由于是实时的，因此先算出来`j`的值为1，再计算`m[j]`时即变为`m[1]`，最后的运算就变成`m[1] = 99`，将`m`数组第二位赋值99.得到了最后的结果。