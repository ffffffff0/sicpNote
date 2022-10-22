#### 实验环境

1. 在Linux x86_64上完成作业，具体系统信息为：` ‌‍‍‏Linux VM-16-3-ubuntu 4.15.0-180-generic #189-Ubuntu SMP Wed May 18 14:13:57 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux` 。
2. GCC 版本为 `gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04) `， GDB版本为` ‌‍‍‏GNU gdb (Ubuntu 8.1.1-0ubuntu1) 8.1.1` 。

#### 作业C

使用程序如下:

```c
#include<stdio.h>
#include<string.h>

int canAccess(){
    char pwd[12];
    gets(pwd);
    // 修改返回地址
    pwd[20] = 0x731;
    return strcmp(pwd, "password");
}

int main(int argc, char * argv[]){
    int access = canAccess();
    if (access) {
        printf("can not access \r\n");
        return -1;
    }else{
        printf("can access\r\n");
    }
    printf("do something...\r\n");
    return 0;
}
```

首先关闭Linux地址随机化，编译c程序：

```bash
# 关闭地址随机化
sudo sysctl -w kernel.randomize_va_space=0
# 可执行栈和关闭保护
gcc -z execstack -fno-stack-protector -g -o main_dbg main_dbg.c
```

使用GDB调试程序, 查看编译结果:

<img src="assets/image-20221011104556143.png" alt="image-20221011104556143" style="zoom: 50%;" />

<img src="assets/image-20221011121920382.png" alt="image-20221011121920382" style="zoom:50%;" />

在canAccess中打断点，**rsp:指向当前栈帧的顶部, rbp:指向当前栈帧的底部, rip:指向当前栈帧中执行的指令**，可以查看栈地址：

<img src="assets/image-20221011104814564.png" alt="image-20221011104814564" style="zoom: 50%;" />

由栈的布局可以知道, 栈指针地址为`0x7fffffffe340`：

<img src="assets/image-20221011104932893.png" alt="image-20221011104932893" style="zoom: 33%;" />

查看栈指针地址, 可以看出其跳转地址就是密码正确分支：

<img src="assets/image-20221011105312894.png" alt="image-20221011105312894" style="zoom:50%;" />

所以在代码中修改其返回地址，可以直接跳转到密码正确的分支，其结果如下：

<img src="assets/image-20221011105624319.png" alt="image-20221011105624319" style="zoom:50%;" />



#### 作业A

可以看出缓冲区地址为：`0x7fffffffe380` 到 ` 0x7fffffffe390` ， pwd 数组的起始地址为：`0x7fffffffe384` 。



可以看到运行时pwd的地址为：`0x7fffffffe384`, rip 的地址 `0x7fffffffe39c` 。

<img src="assets/image-20221015132146810.png" alt="image-20221015132146810" style="zoom:50%;" />







