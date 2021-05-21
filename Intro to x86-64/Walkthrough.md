For tasks 1-3 there are no questions to answer. So, moving straight to task 4:  
Task 4:  
Let's use commands that were introduced in first 3 tasks.

```r2 -d if2```  
```aaa```  
```pdf @main```  

![Image1](https://user-images.githubusercontent.com/15310399/119133081-ae14e400-ba65-11eb-8528-d6c8b2197199.jpg)  

First 3 questions for this task can be answered by placing break point on the command ```movl $0 $eax``` and looking at the value of *var_ch*, *var_4h*, *var_8h*  
For me, this commands will be:  

```db 0x55b8f7777637```  
```dc```  
```px @rbp-0x8```  
```px @rbp-0xc```  
```px @rbp-0x4```  

Looking at the first byte we can see the values we need. (p.s. the answer should be in decimals)  

For the last question we need to look at the last command used to change the value of *var_8h* (I used red marker to show it). You can look at what this command is 
doing in the Task 2(Introduction)

Task 5:  

```r2 -d loop2```  
```aaa```  
```pdf @main```  

![Image2](https://user-images.githubusercontent.com/15310399/119135840-2630d900-ba69-11eb-8185-7ef855a7b548.jpg)  

So, for the first and second questions we need to look at the second iteration of the loop. As we can see, our only condition checking operations are ```cmpl``` and ```jle```. 
Both of the given variables are changed on the 0x55be1f27e61c and 0x55be1f27e620. So we can place our first break point on the address above the red line. In addition, we need to check those variables at the end of the program. So, it is better to place additional breakpoint at the address above the blue line on the picture above.

```db 0x55be1f27e623```  
```db 0x55be1f27e635```   
Questions 1-2:  
```dc```  
```px @rbp-0x8```  
```px @rbp-0xc```  
Questions 3-4:  
```dc```  
```dc```  
```px @rbp-0x8```  
```px @rbp-0xc```  

Task 6:  

```r2 -d crackme1```  
```aaa```  
```pdf @main```  

![Image3](https://user-images.githubusercontent.com/15310399/119138476-22eb1c80-ba6c-11eb-8427-5c0a598d4a43.jpg)  

So this one is longer then the previous ones. We need to pass correct password for this file. Let's start analyzing the program from the end, where we get "You've got the correct password". 
Before this we have the ```cmpl $0, var_48h``` and ```je 0x5584543868eb```, which don't get us right to the exit. Then, we compare ```cmpl $3, var_54h``` and do ```jle 0x558454386890``` if the *var_48h*
is lower or equal to 3. Then we have some operations on our variables and registers, but the most interesting part for us is *callq sym.imp.strcmp*. Lets set our break
point on this command and analyze our registers.  

```db 0x55fa7bfbe8a4```  
```dc```  

strcmp in *C* language compares 2 strings whether they are equal or not. In our case, we have the input string in *rdi* and string to check to in *rsi*. We can view the string it points to using the next command:  

```pf S @rsi```  

![Image4](https://user-images.githubusercontent.com/15310399/119140593-8aa26700-ba6e-11eb-923f-868fadff3d2b.jpg)  

We can decode this value to askii and get the answer. (p.s. the value is stored in the reversed order)
But this is still not quite an answer. If we try this password, it won't work. The reason for this is esi doesn't change the address from the second to third iteration. This means that the 0 is
checked 2 times. We need to add extra .0 in the middle of the answer.

Task 7:  

```r2 -d crackme2```  
```aaa```  
```pdf @main```  

![Image5](https://user-images.githubusercontent.com/15310399/119143275-9f342e80-ba71-11eb-9b70-dd0ce31a3099.jpg)  

In this one we can see a function to open file. Unfortunately, I didn't try to read this as I thought author won't give us permissions to do so. But, there is a way to bruteforse
password for this one.  

![Image6](https://user-images.githubusercontent.com/15310399/119143281-a22f1f00-ba71-11eb-91fa-dd47d9f8d115.jpg)  

We can see that when we fail the comparison  ```cmpb %al, %dl```, we immediately get kicked from binary. Let's send a string to this program and check the value of these registers.

```db 0x55e818afc8ad```  
```dc```  
```aaaaaa```  

I found pretty good command for showing the value of registers. Let's run *drr*:  

```drr```  

![Image7](https://user-images.githubusercontent.com/15310399/119144281-b7f11400-ba72-11eb-9b49-3474e9afe957.jpg)  

We can see that rdx have "d". After rerunning this program and looking at the value of rdx a few times we will get our password.
