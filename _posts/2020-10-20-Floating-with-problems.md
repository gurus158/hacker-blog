#computer-Arithmetics #floating-numbers #binary-representation

# Hi,

Recently I was working on development of some computer program which involved many mathematical computation. One of the computation required to add and compare floating numbers. 
I will not go to exact problem i was solving but it involved a very simple problem describe below, 

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

**WTH!!** i was expecting **True** but it returns **False** which i didn't understand why this happened. 

First I thought this is issue in python but when I researched more about this problem , **I found that this is more fundamental computer science problem than I thought** . It is not python problem but computer arithmetic itself.

Problem was , there are some decimal numbers which have repetitive decimal digits in **bicimal** (decimal in binary) representation   making it to infinite decimal point and  0.1 is one of them.

To understand this lets try to represent 0.1 in binary. For that we can simply divide 1 with 10 in binary format.

1 in binary is  1.000...

10 in binary is 1010

division can be done as 

![8.png](https://github.com/gurus158/blogs/blob/gh-pages/images/8.png?raw=true)

as you can see pattern start repeating after 0.0001100110011...... so on  forever. 

and floating-points are stored as 53-bits values so 54 and onward points will be rounded up and 0.1 will become 
**0.0001100110011001100110011001100110011001100110011001101** in 53-bit format which when converted to decimal would be 

**0.1000000000000000055511151231257827021181583404541015625**  which is greater than **0.1** .

And it doesn't matter if we increased number of bits in floating-point representation there will be never enough bits to stores infinite repetitive bicimal points like 0.1 itself. 
0.1 is one of many floating-points which cause this trouble, for example  **2.1 + 4.2 > 6.3** and many more.

**NOTE:** Also as we fixed the number of bits to represent floating-point (53 in case of python) , so numbers which do not have infinite  repetitive pattern and can be represent in finite decimal points but has more bits than fixed value , say 60 or >53 , will be rounded of and cause problems 

That is why **0.1+0.2 != 0.3** as it will be  greater than **0.3** , hence **a+b == c**  returned False.

 



