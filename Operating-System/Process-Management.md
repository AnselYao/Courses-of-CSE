第二部分 进程管理
===
第五章 CPU调度
5.1 关键词
 
CPU utilization  CPU利用率
CPU(I/O) burst  CPU（I/O）时间
CPU(I/O)-bound  CPU（I/O）绑定
CPU Scheduler  CPU调度器
Idle  空闲
Ready queue  就绪队列
Preemptive scheduling  抢占式调度
Dispatcher module  分配器
FIFO  先入先出
Starvation  饥饿
 
5.2 详细内容
5.2.1 基础概念
I/O-bound和CPU-bound
进程执行时重复CPU burst和I/O burst的循环，CPU-bound的进程花更多的时间计算，I/O-bound的进程用更多的时间I/O。
CPU调度器
当CPU空闲时，OS通过CPU scheduler创建selection process，从ready queue中选择一个process来run，并且分配CPU资源给它。
发生CPU调度的条件
1.	一个process从running态转换为waiting态（进行I/O）；
2.	一个process从running态转换为ready态（发生interrupt）；
3.	一个process从waiting态转换为ready态（I/O完成）；
4.	一个process终止。
抢占式调度与非抢占式调度
非抢占式调度发生于process主动进入waiting态或终止，该方式简单但效率低下（多道系统multi-programed OS）；
抢占式调度在任何时刻都有可能发生（抢占是系统如：Windows）。
分配器
将CPU控制权交给短期调度程序选择的进程，包括三个步骤：
1.	保存现场；
2.	切换到用户态；
3.	跳到用户程序中的正确位置以重新启动该程序。
5.2.2 调度准则
CPU调度的性能指标
CPU utilization  CPU利用率
	我们希望让CPU尽可能多地处于工作状态；
Throughput  吞吐量
	每个单位时间完成的进程数称为吞吐量；
Turnaround time  周转时间
从工作开始到完成的时间段是周转时间；
对于用户来说，周转时间比CPU利用率和吞吐量都重要；
周转时间=进入系统前的等待时间+在ready queue中的等待时间+在其他活动（I/O等）中的等待时间+在CPU中运行的时间；
Waiting time  等待时间
	等待时间是一个进程在ready queue中等待的时间总和；
CPU调度算法不影响进程等待I/O和其他事件的时间量，但会影响进程处于ready queue中的时间；
Response time  响应时间
在交互系统中，从提交请求到第一次响应的时间称为响应时间；
它不包括输出响应所需的时间。
CPU调度准则
CPU调度程序进行一系列“移动”，以决定进程的交错，调度程序的“移动”由调度策略决定；
CPU调度希望达到：
	Max CPU utilization
Max throughput
Min turnaround time
Min waiting time
Min response time
5.2.3 调度算法
First-Come，First-Served（FCFS）
一种先到先得的非抢占式调度算法，一旦一个获得CPU使用权，它将占用CPU直到该进程完成或自动进入等待状态；
可以用队列实现；
缺点：容易出现舰队效应，所有进程等待一个大进程离开CPU。这对于分时系统来说是一个很大的问题。
Round Robin时间片轮转（RR）
RR与FCFS类似，只是每个进程都分配了一个时间片；
处在ready queue中的所有进程构成了一张FIFO表；
当CPU空闲时，调度器选择第一个进程并且让它运行一个时间片的时间；
如果一个进程使用CPU的时间小于一个时间片，它会被移至list的尾端；
如果一个进程在一个时间片的时间内没有结束，CPU资源将被调度器抢占，且该进程被移至list的尾端；
一些问题：
如果时间片太长，RR退化为FCFS；如果时间片太短，RR会变成处理器共享；
内容的切换会影响RR算法的效率；
周转时间由时间片大小决定；
80%的CPU burst应该比时间片时间短；
平均周转时间比SJF长，但是响应时间短；
可以看出，任何公平的策略在性能指标（如周转时间）上都表现不佳，需要在公平与绩效之间权衡。
Lottery
比例共享调度程序（公平共享调度程序）；
调度程序可能会尝试保证每个作业获得一定比例的CPU时间，而不是优化周转时间或响应时间；
基本理念：每隔一段时间，就进行一次抽奖，以确定下一步应该运行哪个进程，运行时间长的进程应有更多机会赢得彩票。彩票用于表示进程（或用户）应获得的资源的份额；
彩票分发是一个难以解决的问题。只有当作业需要大量时间片来运行时，彩票调度算法才能接近期望的结果；
所以，Lottery调度算法并没有被广泛采用，然而在彩票分发比较容易的情况下，它会是一个很好的算法。
Short Job First短作业优先（SJF）
将每个进程与其下一个CPU burst的长度相关联，就绪队列中的进程按CPU burst长度排序，以避免舰队效应。当需要从ready queue中选择进程时，优先选择下一个CPU突发时长最短的进程；
计算一组给定进程的最小平均等待时间，SJF是最优的。
SJF既可以是抢占式又可以是非抢占式：
非抢占式SJF：一旦给予进程CPU权限，它就不会被抢占，直到完成其CPU burst；
抢占式SJF：如果新进程到达（或进入ready queue），CPU burst长度小于当前执行进程的剩余时间，则抢占。该方案被称为最短剩余时间优先SRTF。
难以预测下一个CPU burst的时长（e.g. 指数平均）；
会出现饥饿的情况，一些长作业可能根本不会有机会运行。
Priority Scheduling优先级调度
每个进程拥有它的优先级：
内部优先级：由时间限制、内存要求、文件数等决定；
外部优先级：不受操作系统控制（如：进程的重要性）；
调度器优先选择ready queue中最高优先级的进程来运行；
FCFS和SJF是优先级调度的特殊案例；
优先级调度既可以是抢占式又可以是非抢占式：
非抢占式优先级调度：不会被抢占，直到完成CPU burst；
抢占式优先级调度：当新到达的进程拥有更高的优先级时，正在运行的进程将被抢占；
会出现饥饿的情况，一些长作业可能根本不会有机会运行。
解决饥饿问题的方法——Aging机制：根据waiting time逐渐提升优先级；
Multilevel Queue多级队列
将ready queue划分为单独的队列，各个队列使用自己的调度算法：
交互式系统：foreground，RR；
批处理系统：background，FCFS；
根据进程的某些属性将每个进程永久分配给一个队列（如：内存使用，优先级，进程类型）
只有当进程所在队列前面的所有队列都为空时，该进程才可以运行；当更高优先级队列中有新的进程进入时，正在运行的进程被抢占；
调度必须在队列之间进行：
固定优先级调度：可能导致饥饿；
Lottery调度：每个队列得到一定的CPU时间，可以用来调度该队列中的进程；
Multilevel Feedback Queue多级反馈队列
允许进程在队列之间移动，通过该种方法可以实现Aging机制；
基本思想：需要更短CPU burst的进程被给予更高的优先级。如果一个进程占用较多CPU时间，它将被移至优先级低的队列中；
因此I/O bound进程将在高优先级队列中； 
当一个进程行为有所改变时，它有可能会被移动到其他队列中，如：一个I/O bound的进程开始需要更多的CPU时间，它将会被降级，放到等级较低的队列中。
多级反馈队列调度器的参数：
	队列数量；
	每个队列的调度算法；
	升级进程的方法；
	降级进程的方法；
	决定哪个队列中的进程进入CPU的方法；
5.3 典型例题
调度算法计算题 ppt5.28、5.31、5.34
5.4 扩展
作业调度按一定的算法从磁盘上的“输入井”中选择资源能得到满足的作业装入内存，使作业有机会去占用处理器执行。
但是，一个作业能否占用处理器，什么时间能够占用处理器，必须由进程调度来决定。所以，作业调度选中了一个作业且把它装入内存时，就应为该作业创建一个进程，若有多个作业被装入内存，则内存中同时存在多个进程，这些进程的初始状态为就绪状态，然后，由进程调度来选择当前可占用处理器的进程，进程运行中由于某种原因状态发生变化，当它让出处理器时，进程调度就再选另一个作业的进程运行。
因此，作业调度与进程调度相互配合才能实现多道作业的并行执行。
一般来说，处理机调度可分为三个级别，分别是高级调度、中级调度和低级调度。
高级调度又称作业调度，作业就是用户程序及其所需的数据和命令的集合，作业管理就是对作业的执行情况进行系统管理的程序的集合。作业调度程序的主要功能是审查系统是否能满足用户作业的资源要求以及按照一定的算法来选取作业。
引入中级调度的主要目的是为了提高内存的利用率和系统吞吐量，使得暂时不运行的进程从内存对换到外存上。
低级调度又称进程调度，其主要功能是根据一定的算法将cpu分派给就绪队列中的一个进程。进程调度是操作系统中最基本的一种调度，其调度策略的优劣直接影响整个系统的性能。
 
第六章 进程同步
6.1 关键词
 
Race condition  竞争条件
Access and manipulate  访问和操作
Critical shared data  关键共享数据
Data inconsistency  数据不一致
Assembly language 汇编语言
Process synchronization  进程同步
Interleaving 交错
Exclusive lock  互斥锁
Shared lock  共享锁
Mutex  互斥
Critical section  临界区
Dead lock  死锁
Semaphore  信号量
Condition variable  条件变量
Monitor  
 
6.2 详细内容
6.2.1 基础概念
临界共享数据
值的修改必须被原子地执行；
原子操作指完整地完成而不中断的操作；
e.g. 对于指针counter，counter++在机器语言中为：
register1 = counter
register1 = register1 + 1
counter = register1
当producer和consumer同时修改缓冲区的值时，汇编语言中可能产生交错。
竞争条件
多个processes（threads）同时访问和操作统一数据执行的结果取决于访问发生的顺序。需要通过同步并发进程来避免竞争条件的出现。
6.2.2 临界区问题
进程同步的三种经典模式
共享内存编程的locks：排他锁/写锁exclusive lock、共享锁/读锁shared lock；
其他同步原语，如：barrier。
线程排他锁实现的系统支持
使用mutex：使线程睡眠而不是循环（浪费CPU周期）；
POSIX线程用于互斥锁的API：
int pthread_mutex_lock(pthread_mutex_t* mutex)
int pthread_mutex_unlock(pthread_mutex_t* mutex)；
e.g.	pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_lock(&lock);
… //临界区
pthread_mutex_unlock(&lock);
临界区
多个进程竞争使用某个共享数据，他们每个进程都有一个访问共享数据的代码段，称为critical section；
需要保证，若一个进程正处于它的临界区，没有其他进程可以进入临界区。因此，临界区代码的执行必须互斥；
临界区问题即设计一种进程协作的协议。
临界区协议
一个critical section protocol包含两部分：进入区（lock）、退出区（unlock）；
临界区处于这两部分之间。
临界区问题的解决方案
解决方案不能基于进程相对速度或调度机制，且需要满足三个条件：
1.	互斥
如果进程P在其临界段中执行，那么其他进程就不能在其临界段中执行；
要求进入协议（entry protocol）能够阻止进程进入临界区，并且须在一个进程退出临界区时及时使一个等待进程进入临界区；
2.	进步（progress？）
如果没有进程正处于临界区，并且有其他进程想要进入他们的临界区，那么只有处于等待中的进程才可以参与竞争；
此决策不会被其他进程影响，且不能被无限期推迟。
3.	有限等待
一个进程在请求进入临界区后，在被允许进入之前，其他进程被允许进入临界区的次数是有限的；
因此，即使一个进程可能被其他等待进程阻塞，它也不会永远等待（假定每个进程执行速度不为零且不考虑n个进程的相对速度）。
	Peterson算法：用于解决两个进程的临界区问题。对于Process Pi，有
do {
flag[i] = true; //进入区 I am interested
victim = i; //进入区 you go first
while (flag[j] and victim == i) ; //进入区
… //临界区
flag[i] = false; //退出区 I am not interested any more
… //剩余区
} while (1);
 
 
图6.1 Peterson锁：序列化获取资源
 
图6.2 Peterson锁：并发获取资源
 
	Bakery算法：用于解决多个进程的临界区问题。在进入临界区之前，进程会获得一个数字（升序分发），最小数字持有者进入临界区。若Pi和Pj获得相同数字，则Pmin(i,j)先进入。对于Process Pi，有
do {
choosing[i] = true;
number[i] = max(number[0], number[1], …, number[n – 1]) + 1;
choosing[i] = false;
for (j = 0; j < n; j++) {
while (choosing[j]) ;
while ((number[j] != 0) && ((number[j], j) < (number[i], i))) ;
}
… //临界区
number[i] = 0;
… //剩余区
} while (1);
6.2.3 同步硬件
6.2.4 信号量：通用同步工具
信号量的概念
	一种同步工具，不需要忙等（busy waiting）但需要系统支持；
只能通过wait()和signal()来改变操作信号量的值，除了初始化时赋初值外，其他任何情况都不能再次赋值。
信号量的实现
 
定义：
typedef struct {
int counter;
struct process *L;
an in-kernel exclusive lock;
} semaphore;
两种操作：
	block：阻塞进程P；
	wakeup(P)：恢复备阻塞进程P的执行；



 
图6.3 Semaphore图解
 
对于单处理器，分配器通过中断或显式请求获得控制权：
 
wait(S):
Disable interrupts;
S.value--;
if (S.value < 0) {
add this process to S.L;
block;
}
Enable interrupts;
signal(S):
Disable interrupts;
S.value++;
if (S.value <= 0) {
remove a process P from S.L;
wakeup(P);
}
Enable interrupts;
 
对于多处理器，不能通过关中断来保证互斥，我们假设硬件提供了Test-and-Set原子指令：
 
wait(S):
while(TAS(S.lock));
S.value--;
if (S.value < 0) {
add this process to S.L;
block;
}
lock = 0;
signal(S):
while(TAS(S.lock));
S.value++;
if (S.value <= 0) {
remove a process P from S.L;
wakeup(P);
}
lock = 0; 
应用二进制信号量解决临界区问题
	将信号量用作事件通知工具；
e.g. 共享数据：
		semaphore lock; //初始值为1
	对于进程Pi：
do {
wait(lock);
//临界区
signal(lock);
//剩余区
} while (1);
Semaphore和Mutex的区别：信号量是通过进程/线程阻塞和唤醒来实现的；互斥锁可以由一些内核内部实现为自旋锁，这在多处理器系统上可能更有效，但会降低单处理器机器的速度。
使用信号量的副作用
	死锁：两个或多个进程无限期地等待一个事件，该事件只能由其中一个进程引起。
饥饿：无限期阻塞，进程可能永远不会从其正在等待的信号量队列中删除。
6.2.5 同步经典问题
生产者-消费者（有限缓冲区）问题PC
共享变量：semaphore fillCount, emptyCount；
		fillCount：缓冲区内物品数量；
		emptyCount：缓冲区内空槽数量；
初值：fillCount = 0，emptyCount = n；
 
生产者：
do {
…
//produce an item in nextp
…
wait(emptyCount);
wait(mutex);
//add nextp to buffer
signal(mutex);
signal(fillCount);
} while (1);
消费者：
do {
wait(fillCount);
wait(mutex);
//remove an item from buffer to nextc
signal(mutex);
signal(emptyCount);
…
//consume the item in nextc
…
} while (1);
 
	使用环形队列；
信号量：producer关注emptyCount；consumer关注fillCount；mutex（0/1）保证缓冲区（临界资源）的互斥访问；
临界区（存取产品）越精简越好，取出之后的操作应在临界区外；
需要用mutex确保对临界资源的呼哧访问；若没有mutex，可能在全局环形队列中产生race condition；若buffer size=1，可以去掉mutex；
wait的顺序很重要：signal可以调换顺序，而wait不可以；因为wait里面会有block，如果wait(mutex)顺序调换，可能产生死锁；通常写代码使用压栈逻辑，所以使signal(mutex)和wait(signal)靠近buffer操作。
读者-写者（共享-锁）问题RW
共享变量：semaphore mutex, wrt；int readcount；
		readcount：浏览共享内容的读者数量；
		mutex：保证对readcount的互斥访问；
		wrt：修改共享内容的权限；
初值：mutex = 1, wrt = 1, readcount = 0；
 
写者：
wait(wrt);
…
//写入数据
…
signal(wrt);






读者：
wait(mutex);
readcount++;
if (readcount == 1)
wait(wrt);
signal(mutex);
//读数据
wait(mutex);
readcount--;
if (readcount == 0)
signal(wrt);
signal(mutex);
 
第一个读者拿到wrt权力之后，所有读者才可以开始读；
读者可以并发访问，写者须等待读者读完之后才可以访问；
读者间共享，写者与读者互斥。
哲学家进餐问题DP
桌子上总共只有5支筷子，在每人两边分开各放一支。
条件：
1. 只有拿到两支筷子时，哲学家才能吃饭。
2. 如果筷子已在他人手上，则该哲学家必须等待到他人吃完之后才能拿到筷子。
3. 任一哲学家在自己未拿到两支筷子吃饭之前，决不放下自己手中的筷子。
共享变量：semaphore chopstick[5]；
初值： 全为1；
对于Philosopher i：
do {
wait(chopstick[i]);
wait(chopstick[(i+1) % 5]);
…
//吃
…
signal(chopstick[i]);
signal(chopstick[(i+1) % 5]);
…
//思考
…
} while (1);
 
6.2.6 条件变量
条件变量
允许线程在条件为真之前一直等待（挂起）；
把所有调用的进程挂在waiting list后面，直到signal()；
必须在mutex内部使用；
判断条件是否满足时一定要用while；
障碍物同步问题
	
管程Monitor
	面向对象；
	互斥：至多只有一个进程在管程内执行；
	事件通知属性；
Entry queue
6.3 典型例题
进程同步习题课ppt
 
第七章 死锁
7.1 关键词
 
Deadlocks  死锁

 
7.2 详细内容
7.2.1 基础概念
系统模式
请求（request）：如果一个进程请求使用某个不能立即授予的系统资源，那么该进程将被阻塞（block）知道它可以获得该资源；
使用（use）：进程可以对资源进行操作；
释放（release）：进程释放资源；
死锁
死锁是一组进程（或线程）之间资源稀缺的特殊现象：一组被阻塞的进程，每个进程持有一个资源，并等待获取集合中另一个进程持有的资源；
产生死锁的四个必要条件
互斥条件（mutual exclusion）：一次只有一个进程可以使用某个资源；
不剥夺条件（no preemption）：一个资源只能被占有它的进程完成任务（task）后自愿地释放；
请求与保持条件（hold and wait）：进程在等待分配新资源的同时，继续占有以及分配到的资源；
环路等待条件（circular wait）：存在一种进程资源的循环等待链，链中的每一个进程已获得的资源同时被链中的下一个进程所请求。
资源分配图
   
 
当每个资源类型（resource type）只有一个实例（instance）时，资源分配图（resource-allocation graph）可以转化为进程等待图（wait-for graph）
如果一个资源分配图中不存在环=>没有死锁
如果一个资源分配图中存在环=>
如果每个资源类型（resource type）只有一个实例（instance），则有死锁；
如果每个资源类型有多个实例，则可能有死锁。
处理死锁的方法
预防（prevention）：保证四个必要条件之一失效；
避免（Avoidance）：操作系统需要更多信息，从而可以自行决定当前请求应该被实现还是推迟。
7.2.2 死锁预防
互斥条件
一些可共享资源需要互斥地访问，所以不能否认互斥条件；
请求与保持条件
严格禁止一个进程占有某些资源的同时请求其他资源；
两个策略：
	一个进程在它运行之前得到所有资源；
	当一个进程请求资源时，它当前不能拥有任何资源；
评价：
资源利用率低，因为很多资源会被长时间占用但不使用；
可能出现饥饿，需要一些使用较多资源的进程可能需要无限期地等待。
不剥夺条件
如果一个占有某些资源的进程请求某个不能立刻分配的资源，那么当前所有被占有的资源都将被释放；
如果请求的资源被占用：
	如果该资源被等待其他资源的进程占用，那么该资源会被请求进程抢占；
	否则请求进程会等待，直到请求进程可得。在它等待时，它的资源可能被抢占；
环路等待条件
给所有资源类型（如：磁带，打印机）排序，一个进程只可以请求比它拥有的资源类型高级的资源，这使得进程需要释放高级资源以获得低级资源；
哲学家问题的死锁预防
规定奇数号的哲学家先拿起他左边的筷子，然后再去拿他右边的筷子;而偶数号的哲学家则相反。
semaphore chopstick[5] = {1，1，1，1，1};
void philosopher(int i) {
do {
think();
if(i%2 == 0) { // 偶数哲学家，先右后左。
wait (chopstick[(i+1)%5]) ;
wait (chopstick[i]) ;
eat();
signal (chopstick[(i+1)%5]) ;
signal (chopstick[i]) ;
} else { // 奇数哲学家，先左后右。
wait (chopstick[i]) ;
wait (chopstick[(i+1)%5]) ;
eat();
signal (chopstick[i]) ;
signal (chopstick[(i+1)%5]) ;
}
} while(true);
}
7.2.3 死锁避免
最简单最有效的模式需要每个进程声明它可能需要的每种类型资源的最大数量；
死锁避免算法动态检测资源分配状态，以此来保证不会出现环路等待条件（当每个资源类型只有一个实例时）；
资源分配状态由可获得、已分配资源数量以及最大需求进程数量决定；
安全状态
当一个进程请求某个可获得的资源时，系统必须决定立即分配是否会让系统保持安全状态；
若存在一个安全的顺序使所有进程运行到最后，那么系统处于安全状态；
序列<P1, P2, …, Pn>是安全的当且仅当对每个Pi，Pi可请求的资源可以被当前可获得的资源+所有Pj（j<i）占有的资源满足；
死锁避免即确保系统永远不会进入不安全状态。
 
每个资源类型一个实例：资源分配图算法
声明边（claim edge）Pi→Rj表示进程Pi可能请求资源Rj，用虚线表示；
当进程请求资源时，声明边（claim edge）转换为请求边（request edge）；当进程释放资源时，分配边（assignment edge）将重新转换为声明边（claim edge）。

每个资源类型多个实例：银行家算法
银行家算法的三个假设：
	每个进程必须预先声明其对每个资源类型的最大使用量；
	当进程请求特定数量的资源时，它可能需要等待，即使系统有可用的资源；
	当一个进程得到它所需要的所有资源时，它必须在有限的时间内归还它们；
数据结构：
	n 进程数量；
m 资源类型数量；
available[ ] 长为m的向量，如果available[j]=k，说明资源类型Rj有k个实例是可获得的；
max[ ] n*m的矩阵，max[i,j]=k说明进程Pi可能请求至多Rj类型资源k个实例；
allocation[ ] n*m的矩阵，allocation[i,j]=k说明Pi现在被分配了Rj类型资源的k个实例；
need[ ] n*m的矩阵，need[i,j]=k说明Pi可能还需要k个Rj类型资源实例来完成它的任务（task）；
need=max-allocation；
算法描述：
定义request[ ]向量，对于每个Pi，如果requesti[j]=k，那么进程Pi想要Rj类型资源实例k个；
Step 1 如果requesti<=needi，step 2；否则将因Pi超过最大声明而出错；
Step 2 如果requesti<=available，step 3；否则Pi必须等待，因为请求资源不可得到；
Step 3 假设通过修改状态来分配请求资源给进程Pi：
		for (j=0; j<m; j++){
	availablej= availablej-requesti[j];
	allocationi [j]= allocationi[j] + requesti[j];
needi[j] = needi[j] – requesti[j] ;
}
		如果safe，资源分配给进程Pi，Pi进入Ready态；
		如果unsafe，Pi必须等待，另存旧的资源分配状态。
安全算法：
	目的：区别safe和unsafe状态；
悲观假设：所有进程最终都将尝试获取其声明的最大资源，并在不久之后终止；如果一个进程在没有获取其最大资源的情况下终止，那么它会使系统上的操作更加容易。
区分方法：确定状态是否安全，方法是尝试通过进程查找假定的请求序列，该序列允许每个进程获取其最大资源，然后终止（将其资源返回到系统）。任何不存在此类序列的状态都是不安全状态；
Step 1 声明长度分别为m和n的work[ ]和finish[ ]向量，初始化为：
		work[ ]=available[ ];
		finish[i]=false;	
Step 2 找到同时满足以下条件的i：
		finish[i]=false;
		needi<=work;
		如果不存在这样的i，转step 4；
Step 3 finish[i]=true;
		work=work+allocationi;//收回资源
		转step 2；
Step 4 如果对所有i，finish[i]=true，那么系统处于safe态。
注意：这些请求和分配都是假设的，该算法产生他们来检查安全性，但是实际上没有资源被分配也没有进程终止。
e.g. ppt p.7.52-7.58
7.2.4 死锁检测
死锁避免需要每个进程声明它可能需要的每种类型资源的最大数量，然而有事这种信息无从获得。相对地，我们可以使用死锁检测机制：允许系统进入死锁状态，周期性地运行死锁检测算法，死锁检测后恢复；
每个资源类型一个实例
	维护等待图：
		节点为进程；
		Pi→Pj表示Pi正在等待Pj；
	定期调用死锁检测算法，在图中搜索环；
检测图中环的算法需要n2次操作，其中n是图中顶点的数目。
每个资源类型多个实例
	available[ ] 长度为m的向量，表示每种类型可获得的资源数；
	allocation[ ] n*m的矩阵，定义了当前分配给每个进程的每种类型的资源数；
	request[ ] n*m的矩阵，表示当前每个进程的请求，如果request[I,j]=k，说明Pi正在请求Rj类型资源的k个实例；
死锁检测算法
Step 1 声明长度分别为m和n的work[ ]和finish[ ]向量，初始化为：
		work[ ]=available[ ];
		allocationi<>0，则finish[i]=false；否则finish[i]=true；	
Step 2 找到同时满足以下条件的i：
		finish[i]=false;
		requesti<=work;
		如果不存在这样的i，转step 4；
Step 3 finish[i]=true;
		work=work+allocationi;//收回资源
		转step 2；
Step 4 如果对1<=i<=n的某些i，finish[i]=false，那么系统处于死锁态。
