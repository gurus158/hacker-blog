#computer-Arithmetics #floating-numbers #binary-representation

# Hi,

Recently I was working on development of some computer program which involved many mathematical computation. One of the computation required to add and compare floating numbers. 
I will not go to exact problem i was solving but it involved a very simple problem describe below,

there are two list named **a** and **b** having sets of a's and b's (floating numbers), and there is another list **c** which is set of some floating numbers. 

We have to three numbers **a,b and c** and have to check if **a+b ==c** or not, quite simple.

simple code in python can be :

```python   
>>> b=3.3       
>>> c=5.5       
>>> a+b == c 
True  
```

However things start getting messy when i tried **a=0.1 , b=0.2 and c=0.3** 

```python
>>> a=0.1
>>> b=0.2 
>>> c=0.3 
>>> a+b == c 
False
```

**WTH!!** i was expecting **True** but it returns **False** which i didn't understand why this happen. 

First I thought this is issue in python but when I researched more about this problem , **I found that this is more fundamental computer science problem than I thought** . It is not python problem but computer arithmetic itself.

Problem was , there are some decimal numbers which have repetitive decimal digits in **bicimal** (decimal in binary) representation   making it to infinite decimal point. 0.1 is one of them.

To understand this lets try to represent 0.1 in binary. For that we can simply divide 1 with 10 in binary format.

1 in binary is  1.000...

10 in binary is 1010

division can be done as 

![1.png](https://github.com/gurus158/blogs/blob/gh-pages/images/8.png?raw=true)



