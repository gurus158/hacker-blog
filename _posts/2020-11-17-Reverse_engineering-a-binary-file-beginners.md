**Hi everyone!!**
 In this blog i will discuss the basic of **reverse engineering** of a **binary file** in linux.
 The binary file is output of compilation of a [c program](https://drive.google.com/open?id=1wOug8L1jdlJo0C83xWLxF6IT2N-fnEoR) using GCC compiler in 64bit Kali Linux. 
 Binary file can be find [here](https://drive.google.com/open?id=1h4nBwT4vHPz4LN2K4qqrJz7jDJVcCDlJ). This file will be run on a Linux machine only(sorry for windows users)  but if you use windows compile code in your machine without looking at  source code.
 Tool we gonna use to reverse engineering is gdb(a gnu debugger for Linux) and [peda](https://github.com/longld/peda) (a python assistance for gdb) 

**Our task will be to find the hidden flag in binary by reverse engineering the binary.**

**I will give 2 methods to reverse engineering the binary** 

 so lets start **reverse engineering a binary file**....

 first execute the binary using
 '**./binary**' command
 it will ask to input the magic string

 input "aaaaaa" and enter

 it give "wrong flag message"



[![reverse engineering1](https://1.bp.blogspot.com/-Xx-oQdHkjto/XDoFGR53tNI/AAAAAAAAAOk/NB6IGfFK_kw3OLTJ_8qea5DjzS71qem2gCLcBGAs/s1600/1.jpeg)](https://1.bp.blogspot.com/-Xx-oQdHkjto/XDoFGR53tNI/AAAAAAAAAOk/NB6IGfFK_kw3OLTJ_8qea5DjzS71qem2gCLcBGAs/s1600/1.jpeg)


 So if we enter correct string we will get the flag. Question is how we get the string?

1. Its time to fire up the gdb (do not forget to add peda extension)

2. In terminal type '**gdb ./binary**' and enter

 To get output of disassembly in Intel format (as Intel format is easy to read).....

 in gdb type '**set disassembly-flavor Intel**' and enter 



[![reverse engineering1](https://1.bp.blogspot.com/-PiDgbFpxMMs/XDoXNonx_wI/AAAAAAAAAPA/ejLcQF3aQXMhP0QOiJZ4r5HpiF0uldrdgCLcBGAs/s1600/2.jpeg)](https://1.bp.blogspot.com/-PiDgbFpxMMs/XDoXNonx_wI/AAAAAAAAAPA/ejLcQF3aQXMhP0QOiJZ4r5HpiF0uldrdgCLcBGAs/s1600/2.jpeg)



3. Next step is to see what functions are present in program and if there is main(although in **C** you will have main function most of the time but not all time so it is good to get list of all functions),

 in gdb type '**info functions**' and enter



[![reverse engineering1](https://2.bp.blogspot.com/-4NE6AVFkCSU/XDoYmdB5qYI/AAAAAAAAAPM/Cvo9ZJBoIKgdtweu-vghlRvsIsyA5hV2ACLcBGAs/s1600/3.jpeg)](https://2.bp.blogspot.com/-4NE6AVFkCSU/XDoYmdB5qYI/AAAAAAAAAPM/Cvo9ZJBoIKgdtweu-vghlRvsIsyA5hV2ACLcBGAs/s1600/3.jpeg)


 as **main** is present, we start analysis from here as in **C,** **main** is executed first

4. ow we disassemble the main function to see assembly code for it.
    To disassemble **main** in **gdb** type
    '**disassemble main**' and enter



[![reverse engineering1](https://4.bp.blogspot.com/-PChScOJrBJ4/XDogobBKpFI/AAAAAAAAAPo/tYoUpzzlngYMgXFnE_X0ejJqBSEiweILQCLcBGAs/s1600/4.jpeg)](https://4.bp.blogspot.com/-PChScOJrBJ4/XDogobBKpFI/AAAAAAAAAPo/tYoUpzzlngYMgXFnE_X0ejJqBSEiweILQCLcBGAs/s1600/4.jpeg)


 our main Interest will be at **call, cmp, jmp** ,**etc** as they are decision branches and hence execution flow...

**#METHOD 1**

 As you can see first there is call to **puts()** at **main+26** execution and then to **fgets()** at **main+53** and then to **strlen()** at **main+68** and there is **cmp** (compare) execution after it.

 Remember, the execution of binary first print the line 'enter the magic string' and then wait for input which is just call to **puts()** and **fgets()**

 after that it call **strlen()** to get length of input and compare it with 0xb in '**cmp rax, 0xb'** line

 so we know that length of input should be **0xb** i.e 11 in decimal
 after **cmp** line there is '**jbe**' command at **main+77** that means it jumps if **rab** is not bigger or equal to **0xb**(11)
 so if input is smaller than 11 it jumps to **main+101**
 lets see what is at **main+101** line



[![reverse engineering1](https://4.bp.blogspot.com/-ReRqBJRt_c4/XDokCDjbG0I/AAAAAAAAAP8/-q2g3ZLfXu4zuTvItniVtE2FhIaH6t2JgCLcBGAs/s1600/5.jpeg)](https://4.bp.blogspot.com/-ReRqBJRt_c4/XDokCDjbG0I/AAAAAAAAAP8/-q2g3ZLfXu4zuTvItniVtE2FhIaH6t2JgCLcBGAs/s1600/5.jpeg)


 so at **main+101** it do something and jump to **main+133** from **main+108**. 



[![reverse engineering1](https://3.bp.blogspot.com/-aF88KPf2nvU/XDorhyKtFNI/AAAAAAAAAQQ/siaAzRoxNCAL3EUcHMMgrR5QBw1eCaFjgCLcBGAs/s1600/6.jpeg)](https://3.bp.blogspot.com/-aF88KPf2nvU/XDorhyKtFNI/AAAAAAAAAQQ/siaAzRoxNCAL3EUcHMMgrR5QBw1eCaFjgCLcBGAs/s1600/6.jpeg)


 at **main+133** it execute some lines and reach at **main+149** calling **strlen()** and **cmp** at **main+154** and at **main+157** it jumps to **main+110** if **cmp** is false.
 Now at **main+110** it do some stuff and reach **main+115**
 and **mov [rbp+rax\*1-0x90]**to **eax,**
 at this moment **rax** is **0** ,
 also **rbp-0x90** contained the input string(see **main+53** and **main+58**),
 so **main+115** is moving first char of input string to **eax**....
 at **main+126** it adding **eax**(first char of string) to **rbp-0x14**(0 at this time, see **main+12**)
 at **main+129** it adding **0x1**(one) to **rbp-0x18**(has zero in it see **main+101**)
 than again at **main+133** same happens
 so **main+110** to **main+157** is a loop in which chars of string are summed one by one and result is stored in **rbp-0x14**

 it is like
 x=0 **//rbp-0x14 ...main+12**
 .
 .
 . 
 var=0 **//rbp-0x18 ....main+101**
 while(var < strlen(input){
 x =x+input[var]; **// main+126**
 var = var + 1;  **// main+129**
 }

 damm its so simple in c and totally alien in assembly.😓
 reverse engineering a binary will be hard if you do not understand assembly.
 if you want to know more about assembly go [here](https://en.wikibooks.org/wiki/X86_Assembly)

 so after coming out of loop it reaches **main+159** which compare **rbp-0x14**(x i.e sum of all chars) to **0x3e8**(1000).

 at **main+166** if not equal it jump to **main+263** from where it exit after print some line
 and if it is equal then it go to **main+173** and call **strcat_str** and then it reaches **main+194** calling **printf()** i.e printing something.....



[![reverse engineering1](https://2.bp.blogspot.com/-ic3enqQ1M6o/XDo3-PyZ3gI/AAAAAAAAAQk/iY19ovcxkG4_1ItNWbLp_NSN4TPM4UoqACLcBGAs/s1600/7.jpeg)](https://2.bp.blogspot.com/-ic3enqQ1M6o/XDo3-PyZ3gI/AAAAAAAAAQk/iY19ovcxkG4_1ItNWbLp_NSN4TPM4UoqACLcBGAs/s1600/7.jpeg)



 at **main+206** it jumps to **main+238** and again a loop come in picture which just printing something....
 and finally after printing lot of things it reaches **main+289** and exit from program....


 so what we got so far....
**1. input length check with 11**
**2. if input length greater than 11 print some thing**
**3. if input length less than 11 it save sum of all chars in x**
**4. if x is equal to 1000 (0x3e8) it call strcat_str and print something in loop and exit**
**5. if x is not equal to 1000 it print something and exit**


 so lets check if we get it right

**we gonna input 3 test input**
**1. greater than 11 chars**
**2. less than 11 chars and sum of chars is not equal to 1000**
**3. less than 11 chars and sum of chars is equal to 1000**

**input1: aaaaaaaaaaaa  (11 times a's and one '\n')**
**input2: aaaaaaaa      (8 times a's and one '\n' )**
**input3: hhhhhhllm)     [6 h's , 2 l's , 1 m's , 1 ('s ]= [104\*6 + 108\*2 + 109 + 41 = 990 )**

**NOTE:** Input is taken using **fgets()** which means ,new line char , '**\n**' is also part of input string and '**\n**' has ascii value 10 that is why we using string with total ascii **990**.

 lets see output



[![reverse engineering1](https://4.bp.blogspot.com/-QYRmPUGWq-g/XDpG1gqr_DI/AAAAAAAAAQ4/lg-zX4Yg1ok-T1MCHRxaNttciSqASKuegCLcBGAs/s1600/8.jpeg)](https://4.bp.blogspot.com/-QYRmPUGWq-g/XDpG1gqr_DI/AAAAAAAAAQ4/lg-zX4Yg1ok-T1MCHRxaNttciSqASKuegCLcBGAs/s1600/8.jpeg)


 as you can see all 3 input give different output as there are **only three branches in main** which explained above.

 The last output gives the flag which was our target.

**also the input3 can be different**... only thing to remember is that its **length should be less than 10**(as '\n' is always there) and **sum of ascii of chars must be 1000 including ascii of '\n'** so '**nnnnnnnnn**' will give the flag too...try yourself...


**#METHOD 2**

 This method is very straight forward as compared to above as you do not  have to analyze and understand whole assembly rather just see important  execution

 We know that it take input and do some stuff with it.
 Also at main+159 it compare sum with 1000 which led to important branch of execution tree where flag is printed hence we need to jump to that  branch even if we do not provide correct input.
 yes we can do that in gdb ..... force jump at any point of execution can be performed

 for that we have to put some break points and run this binary in gdb itself

 so disassemble main again in gdb and put break point at main by typing ..
 'break main' and enter

 also break the first check point i.e main+73 (cmp rax, 0xb) by typing 'break *main+73'

 remember the line main+159 where important check take place we need to  break at that point too and also note the next execution which is  main+168

 three break points in total



[![reverse engineering1](https://4.bp.blogspot.com/-a-nWmsSTk5o/XDpTILYISmI/AAAAAAAAARM/yLHiCDij740h0U25VOEHrPRQLQ63mPSdwCLcBGAs/s1600/9.jpeg)](https://4.bp.blogspot.com/-a-nWmsSTk5o/XDpTILYISmI/AAAAAAAAARM/yLHiCDij740h0U25VOEHrPRQLQ63mPSdwCLcBGAs/s1600/9.jpeg)



 
 now run the program in gdb by typing
**'run**' and enter
 first break point will reach i.e first line of main
 now type continue and enter any string



[![reverse engineering1](https://2.bp.blogspot.com/-iOUiyrBhGjU/XDpVvsnXQUI/AAAAAAAAARg/C9M9yzajwU0WzTYAzs59TGEzQDiSn9WMACLcBGAs/s1600/10.jpeg)](https://2.bp.blogspot.com/-iOUiyrBhGjU/XDpVvsnXQUI/AAAAAAAAARg/C9M9yzajwU0WzTYAzs59TGEzQDiSn9WMACLcBGAs/s1600/10.jpeg)


 now enter and we will be at break point 2 from where we have to jump to specific location i.e **main+101** 

 to jump type
**'jump \*main+101'** and enter



[![reverse engineering1](https://1.bp.blogspot.com/-73ZPJ0wRZUA/XDpYK5R-DiI/AAAAAAAAAR8/MF4oqo1v3x4HdYNbn7IyplwnKIVr4Ea0QCLcBGAs/s1600/11.jpeg)](https://1.bp.blogspot.com/-73ZPJ0wRZUA/XDpYK5R-DiI/AAAAAAAAAR8/MF4oqo1v3x4HdYNbn7IyplwnKIVr4Ea0QCLcBGAs/s1600/11.jpeg)




  
 now we will be at break point 3 and lastly jump to *main+168 as discussed above and we will have the flag..



[![img](https://4.bp.blogspot.com/-1QhjTVSlmT0/XDpXcuQy4QI/AAAAAAAAARw/YufoC3XwocMq8Hf-zBd-TKQpbWM5Ce0cACEwYBhgL/s1600/11.jpeg)](https://4.bp.blogspot.com/-1QhjTVSlmT0/XDpXcuQy4QI/AAAAAAAAARw/YufoC3XwocMq8Hf-zBd-TKQpbWM5Ce0cACEwYBhgL/s1600/11.jpeg)


 **This time we don't even care what input was ...still we are able to get flag isn't it amazing...**

 so this is all about reverse engineering a binary file.


**HAPPY HACKING!!**  