---
title: Pingctf 2023 dangleme
date: 2023-12-19 
categories: [CTF, PWN]
tags: [pingctf2023,writeup]
description: writeup for Pingctf 2023 pwn challenge--dangleme
---

## 环境搭建

用官方wp在本地发现打不通，只能试着去复现官方题目的环境，下载文件查看dockerfile后发现，他们使用了[redpwnjail](https://github.com/redpwn/jail/tree/main)搭建题目环境,

[FAQ](https://github.com/redpwn/jail/blob/main/docs/competitors.md)里有如何更改dockerfile辅助调试的方法,其实也就是撤销将环境迁移进jail的过程，然后再安装一些调试所需要的工具，python3环境，gdb，pwntools，pwndbg这些必要的工具



## 复现

整个复现的过程还是参考了[这篇wp](https://ctftime.org/writeup/38337)，官方wp并没有说的很清楚，导致我一开始的想法陷入了误区。

### 静态分析

首先肯定是要拖入ida看一看的

```
__int64 __fastcall main(int a1, char **a2, char **a3)
{
  char *v3; // rax

  setvbuf(stdout, 0LL, 2, 0LL);
  setvbuf(stdin, 0LL, 2, 0LL);
  setvbuf(stderr, 0LL, 2, 0LL);
  v3 = get_random_name();
  menu(v3);
  return 0LL;
}
```



在进入菜单函数之前，首先执行了一个函数来获取一个随机的名字，函数内部是长这样的:



```
char *sub_2309()
{
  int v0; // eax
  char dest[48824]; // [rsp+10h] [rbp-BEC0h] BYREF
  unsigned __int64 v3; // [rsp+BEC8h] [rbp-8h]

  v3 = __readfsqword(0x28u);
  v0 = random(&seed) & 3;
  if ( v0 == 3 )
  {
    strcpy(dest, "Hryzia");
  }
  else if ( v0 == 2 )
  {
    strcpy(dest, "Ewelinka");
  }
  else if ( v0 )
  {
    strcpy(dest, "Beatka");
  }
  else
  {
    strcpy(dest, "Sabinka");
  }
  return dest;
}
```



其实我第一次就发现了，这个函数把栈帧撑的很大，但是后面就是想不起来，注意到返回的对象是一个指向这个函数自身的栈的内存，并且这个位置很深，函数返回时栈帧收回，名字这个变量的指针被带出去了，但是本体还在栈上很深的地方。

ps:ida反汇编出来的这个变量的大小其实可以拿来静态估算需要多少个栈帧的偏移，在调试时我们可以知道栈帧一跳就是0x70,

```
Python>48824/0x70
435.92857142857144
```

这样粗略的算了一下大概就是435帧左右，我引用的wp他们在本地算出来的也就是436帧和433帧

然后就是菜单界面了：

```
unsigned __int64 __fastcall menu(char *a1)
{
  unsigned int v1; // eax
  char v3; // [rsp+17h] [rbp-49h] BYREF
  char *v4; // [rsp+18h] [rbp-48h]
  unsigned __int64 v5; // [rsp+58h] [rbp-8h]

  v5 = __readfsqword(0x28u);
  printf(
    "My name is %s and I am your savior...\n"
    "Now, lay on me your greatest desire:\n"
    "\n"
    "1. I want to hear your name once again\n"
    "2. I want to ascend\n"
    "3. I want a blessing\n"
    "4. You're a false prophet\n"
    "5. I want to return to reality\n"
    "\n"
    "> ",
    a1);
  while ( 1 )
  {
    v3 = 48;
    __isoc99_scanf(" %c", &v3);
    switch ( v3 )
    {
      case '1':
        printf("My name is %s, dear\n", a1);
        goto LABEL_8;
      case '2':
        puts(
          "┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼\n"
          "You're one step closer in your path to redemption...\n"
          "┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼ ┼\n");
        menu(a1);
        return v5 - __readfsqword(0x28u);
      case '3':
        v1 = random((std::random_device *)&seed);
        puts(blessings[v1 % 5uLL]);
        goto LABEL_8;
      case '4':
        fwrite("Hmph! In that case, choose someone better: ", 1uLL, 0x2BuLL, stdout);
        getchar();
        fgets(a1, 258, stdin);
        v4 = strrchr(a1, '\n');
        if ( v4 )
          *v4 = 0;
        goto LABEL_8;
      case '5':
        return v5 - __readfsqword(0x28u);
      default:
LABEL_8:
        printf("> ");
        break;
    }
  }
}
```

显而易见，选项1可以打印name的内存，选项二可以递归跳栈帧，选项四可以直接写name变量，思路就是通过不断跳栈帧，使得栈帧不断增长，直到覆盖name变量，然后通过选项一打印出来泄露的栈内存，这个内存又刚好是一个libc内存，也就可以获得libc基址，由于栈帧已经涨过了name的内存，我们又可以在name里写0x2b的内存，其实就是一个简单的ret2libc的板子题了



