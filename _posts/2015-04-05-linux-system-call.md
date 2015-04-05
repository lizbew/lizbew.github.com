---
layout: post
title: "[MOOC]分析linux system_call中断处理过程"
description: "system_call中断处理过程"
category: linux
tags: [linux, kernel]
---
{% include JB/setup %}

 -  周立维 原创作品转载请注明出处
 - 《Linux内核分析》MOOC课程http://mooc.study.163.com/course/USTC-1000029000

##  系统调用 getpid

选用的系统调用为`getpid`，调用号为20 (/linux-3.18.6/arch/x86/syscalls/syscall_32.tbl)：

    20	i386	getpid			sys_getpid

## 添加 MenuOS 菜单

在 menu/test.c 中添加如下两个函数： GetPid()是直接调用库函数， 而GetPidAsm()是用汇编方式实现。

{% highlight c %}
int GetPid(int argc, char *argv[])
{
    pid_t pid = getpid();
    printf("pid: %d\n", pid);
    return 0;
}

int GetPidAsm(int argc, char *argv[])
{
    pid_t pid;
    asm volatile(
        "mov $0x14, %%eax\n\t"
        "int $0x80\n\t"
        "mov %%eax, %0\n\t"
        : "=m"(pid)
    );
    printf("pid-asm: %d\n", pid);
    return 0;
}
{% endhighlight %}

同时在main()中添加新的菜单项。

{% highlight c %}
int main() 
{
...
    MenuConfig("pid", "Show Current PID", GetPid);
    MenuConfig("pid-asm", "Show Current PID(asm)", GetPidAsm);
    ExecuteMenu();
}
{% endhighlight %}

再 gcc 编译， 生成新的 rootfs.img， 启动 qemu。
如下是运行截图：

![MenuOS][1]

## system_call 流程分析

to be continue

  [1]: /images/post/2015-04-05/menuos-system_call.png

