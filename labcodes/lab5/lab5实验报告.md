#lab5实验报告
##靳子翔 2012011365

##练习一  加载应用程序并执行
实现思路：根据注释中的提示将tf的变量设置好即可。tf的cs段为用户代码段，ds、es、ss段为用户数据段，eip为程序开头，esp为用户栈顶，eflags为允许中断。

回答问题：在调用了switch_to之后，返回到了fortret函数，并且以其中的tf为参数调用fortrets。在iret之后，就会使用这个tf返回tf指向的地方。由于初始化时将tf设置为用户态的段，所以返回后就变成了用户态的程序，并继续执行。

##练习二 父进程复制自己的内存空间给子进程
实现思路：根据注释的提示，找到src和dst的kvaddr(kernel virtual address)，对其进行复制，大小为一个页。再用page_insert修改页表的映射关系。

回答问题：在dup_mmap中复制mm_struct时，将vma指针复制过去，这样就相当于两个mm_struct共享到同一个vma表，将该vma设为只读。发现缺页时，如果检测到在尝试写一个只读页，那么此时新建一个vma并用copy_range复制数据，修改相应的mm_struct。

##练习三  阅读分析源代码，理解进程执行 fork/exec/wait/exit 的实现，以及系统调用的实现
fork: 创建新的进程，复制父进程当前的状态后，令新的进程状态为runable。
exec:通过load_icode将新的程序复制进来，然后执行。如果复制失败，那么调用do_exit退出。
wait：找到符合条件的子进程，则回收子进程的资源并正常返回。如果子进程正在运行，那么调用schedule让自己变为SLEEPING,等到下次被唤醒时寻找子进程。
exit:回收大部分资源，设定自己的状态为ZOMBIE,查看父进程，如果在等待，那么唤醒父进程；接下来遍历自己的子进程，将他们的父进程设为initproc，如果某个子进程为ZOMBIE且initproc为等待状态，那么唤醒initproc。最后通过schedule调度执行其他进程。
>
```
process state changing:
                                            
  alloc_proc                                 RUNNING
      +                                   +--<----<--+
      +                                   + proc_run +
      V                                   +-->---->--+ 
PROC_UNINIT -- proc_init/wakeup_proc --> PROC_RUNNABLE -- try_free_pages/do_wait/do_sleep --> PROC_SLEEPING --
                                           A      +                                                           +
                                           |      +--- do_exit --> PROC_ZOMBIE                                +
                                           +                                                                  + 
                                            -----------------------wakeup_proc----------------------------------
-----------------------------
```

##重要的知识点
do_exit的相关内容在原理课中并没有详细讲解。



