Are you a python developer?

Have you ever witness slow execution of your python code?

Have you ever wonder why numpy , pandas and similar packages are fast?



If answers of above question is Yes, then you come at right place.



Python is one of most popular programming language at present.

However Python was never created to become what it is right now.



**Guido van Rossum** created python for teaching programming. He want to create a language in which coding is as simple as possible and come  out with **python** as solution.



Developer fall in love with this language very easily as it make development of big project so fast and robust.



But this ease of coding has its price. To achieve this purpose, Guido  had to create dynamic interpreted language. For example : when you  declare a variable in python , you don't have to mention its type(int ,  char , string). Python, on run time, automatically find out its type.  Which is both it's power and limitation, as python has to find the type  of variable every time it encounter with one. This process take time  and is one of the main reason for slowness of python.



Guido know this and want to improve python. So he along with **Robert Bradshaw, Stefan Behnel, et al**

created a compiled version of python called **Cython**.




 **Cython** is particularly popular among scientific users of Python, where it has "the perfect audience" according to Python **creator** Guido van Rossum.



In this blog i will talk about Cython and its performance compared to Python.



 For this purpose i will take a popular mathematics problem of finding **standard deviation** and implement it in four ways.



1. Pure python

2. Using Numpy

3. Cython

4. Optimized Cython



And compare their performance



1. **Pure pyhton** 



create a file name StdDev.py with following code



[![img](https://1.bp.blogspot.com/-YPKR-SaNio4/XhrCYkuvEdI/AAAAAAAAAfQ/UsnZGjTUcIMUOmztxiPbTpFjZ1MMcVjzwCLcBGAsYHQ/s640/python.png)](https://1.bp.blogspot.com/-YPKR-SaNio4/XhrCYkuvEdI/AAAAAAAAAfQ/UsnZGjTUcIMUOmztxiPbTpFjZ1MMcVjzwCLcBGAsYHQ/s1600/python.png)



if you donot know how to find standard deviation go [here](https://www.mathsisfun.com/data/standard-deviation-formulas.html)

test this code with below command:
python3 -m timeit -s "import StdDev; import numpy as np; a = [float(v) for v in range(1000000)]" "StdDev.pyStdDev(a)" 

I use timeit to find time taken per loop

output of above command is :

[![img](https://1.bp.blogspot.com/-l_dcm0VMxeg/XhrE1sNHRtI/AAAAAAAAAfc/FYgcXEeydLQYnMNvcf5c2e6YuiPNESf6QCLcBGAsYHQ/s640/pythonTime.png)](https://1.bp.blogspot.com/-l_dcm0VMxeg/XhrE1sNHRtI/AAAAAAAAAfc/FYgcXEeydLQYnMNvcf5c2e6YuiPNESf6QCLcBGAsYHQ/s1600/pythonTime.png)





 so it take **84.4 msec** per loop

 2. **Python with numpy**
 File name NumpyStdDev.py 
 code is:

[![img](https://1.bp.blogspot.com/-wDCPOu4doS4/XhrFjGP8peI/AAAAAAAAAfw/6QvTRPcKQO0nz7ctLfh5OkMgPG7da2unwCLcBGAsYHQ/s320/numpy.png)](https://1.bp.blogspot.com/-wDCPOu4doS4/XhrFjGP8peI/AAAAAAAAAfw/6QvTRPcKQO0nz7ctLfh5OkMgPG7da2unwCLcBGAsYHQ/s1600/numpy.png)

performance:

[![img](https://1.bp.blogspot.com/-gUnmZy2F1UE/XhrGc5D2k-I/AAAAAAAAAf8/ywYQUv5usdE-fUvaLF7XXEt9U7-pn9YLgCLcBGAsYHQ/s640/numpyTime.png)](https://1.bp.blogspot.com/-gUnmZy2F1UE/XhrGc5D2k-I/AAAAAAAAAf8/ywYQUv5usdE-fUvaLF7XXEt9U7-pn9YLgCLcBGAsYHQ/s1600/numpyTime.png)

 It take **2.12 msec** per loop ... surprised ??
 But how numpy making thing fast??
 NumPy is written (mostly) in C which is a low-level language, makes it  very fast. It hides all this complexity under an easy to use module of  Python

 3. **Cython(Naive)**
 File name: cyStdDev.pyx
**code:**

[![img](https://1.bp.blogspot.com/-CS7JdWuqmnE/XhrIGou9UgI/AAAAAAAAAgU/MW5rJTKCJGcT64tMfc6Bj5k-iv0jw2e-ACLcBGAsYHQ/s640/cythonNaive.png)](https://1.bp.blogspot.com/-CS7JdWuqmnE/XhrIGou9UgI/AAAAAAAAAgU/MW5rJTKCJGcT64tMfc6Bj5k-iv0jw2e-ACLcBGAsYHQ/s1600/cythonNaive.png)

 Cython is super set of python i.e code is similar to python.
 however to compile it we have to create a setup.py with code below:

**from distutils.core import setup
from Cython.Build import cythonize
setup(
    ext_modules = cythonize("cyStdDev.pyx")
    )** 



and run it with command: **python3 setup.py build_ext --inplace**

 this will create c implementation of cython code with a module named cyStdDev to import in python code.
 
**performance:**

[![img](https://1.bp.blogspot.com/-m5mrRcJXsLE/XhrKKt7yOMI/AAAAAAAAAgs/sPG6GDvEEVIAHK9dZKvw0QjhLObvKDopQCLcBGAsYHQ/s640/cythonTime.png)](https://1.bp.blogspot.com/-m5mrRcJXsLE/XhrKKt7yOMI/AAAAAAAAAgs/sPG6GDvEEVIAHK9dZKvw0QjhLObvKDopQCLcBGAsYHQ/s1600/cythonTime.png)

 

 It take **3.42 msec** per loop.
 However I didn't even use the power of cython. Only compiled it with cython.

 4. **Optimized Cython**
 file name: **cyStdDevOptimized.pyx**
**code:**

[![img](https://1.bp.blogspot.com/-stGM0tzfXdU/XhrM5q9j6bI/AAAAAAAAAhQ/i4DYiIXIR6oo3l5hxyLqqxKkoQvnytH5gCLcBGAsYHQ/s640/optCython.png)](https://1.bp.blogspot.com/-stGM0tzfXdU/XhrM5q9j6bI/AAAAAAAAAhQ/i4DYiIXIR6oo3l5hxyLqqxKkoQvnytH5gCLcBGAsYHQ/s1600/optCython.png)

 

Code looks scary at first but it is not.
 Main thing here is how i declare variable as **cdef** with statically defined type(double,Py_ssize_t).
 By doing this we are able to achieve C-level performance(slightly slower than C).
**performance:**

[![img](https://1.bp.blogspot.com/-VKHR4Bvp8FQ/XhrN_xAA_4I/AAAAAAAAAhY/2ZaniOHFN_cSCJNKNYeG9m8xsW80MVjAQCLcBGAsYHQ/s1600/optCythonTime.png)](https://1.bp.blogspot.com/-VKHR4Bvp8FQ/XhrN_xAA_4I/AAAAAAAAAhY/2ZaniOHFN_cSCJNKNYeG9m8xsW80MVjAQCLcBGAsYHQ/s1600/optCythonTime.png)

 

it take **2.22 msec** 
 which is quite an improvement. 


 **comparision:** 
 pure python: 84.4 msec
 numpy: 2.12 msec
 cython(naive): 3.42 msec
 cython Optimized: 2.22 msec

 of-course numpy beat my code as it was purely written in C. However optimized Cython code is quite close to Numpy.
 So if you have time consuming task in your python program .. you can  create a module in Cython to perform that task and import it in main  code. This will help to gain performance.
 Also as Cython is superset of python, all python code is valid cython code.
 There is so much ways to use cython in python codes.
 Hope you learn something in this blog.