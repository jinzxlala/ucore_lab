##lab6实验报告
##靳子翔 2012011365

##练习一 使用 Round Robin 调度算法
问题回答：RR_init函数为初始化调度队列，RR_enqueue为将一个进程放入队列中，RR_dequeue为将一个进程从队列中弹出，RR_pick_next为从队列中选择合适的进程来下次运行。

调度过程为，系统在初始化后通过schedule来调度进程。schedule首先将当前进程放入队列，然后挑选合适的进程弹出队列继续运行。round robin算法维护一个队列，但时间片用完时，调用schedule将其放入队尾，再取出队头作为下一个执行的进程。

实现”多级反馈队列调度算法“，可以维护n个队列，在proc_struct中新增一个que_record记录上次的进程位于哪个队列，enqueue时将其push到第que_record个队列中，tick时re_sche设为1,调用schedule。dequeue时将其从队列中取出，并将que_record加一。

##练习二 实现 Stride Scheduling 调度算法
实现过程：按照注释实现即可，init函数中将队头设置为null，进程计数器设置为0。enqueue时用斜堆的merge来进行合并，更新其time_slice和进程计数器。dequeue时调用斜堆的删除，将计数器减1。pick_next时，选出队头，将stride加上BIG_STRIDE/priority。

在实现时需要注意的是，内核线程的priority为0。

##重要的知识点：
原理课中的多级反馈队列调度算法在实验中并没有进行实现。
