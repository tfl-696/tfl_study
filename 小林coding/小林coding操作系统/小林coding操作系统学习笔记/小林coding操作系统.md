# 操作系统

---

## 1. 小林coding：如何避免死锁 ?

### 1.1 死锁的概念

多个进程/线程都在等待对方释放锁，在没有外力介入的情况下，这些进程/线程会一直相互等待，没办法执行下去，这种情况就是死锁。

### 1.2 死锁发生的条件

#### 1. 互斥条件

- 多个进程/线程不能同时使用同一个资源。

#### 2.  持有并等待条件

- 某个进程/线程阻塞等待某个资源的同时，还持有另外一个资源。

#### 3. 不可剥夺条件

- 线程持有的资源在自己用完之前是不能被其他线程获取的。

#### 4. 环路等待条件

- 多个线程获取资源的顺序构成了环形链。

![](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E6%AD%BB%E9%94%81/%E7%8E%AF%E8%B7%AF%E7%AD%89%E5%BE%85%E6%9D%A1%E4%BB%B6.png)

### 1.3 模拟死锁问题的产生

```cpp
- 创建 2 个线程，分别为线程 A 和 线程 B
- 然后有两个互斥锁，分别是 mutex_A 和 mutex_B

pthread_mutex_t mutex_A = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t mutex_B = PTHREAD_MUTEX_INITIALIZER;

int main()
{
    pthread_t tidA, tidB;

    //创建两个线程
    pthread_create(&tidA, NULL, threadA_proc, NULL);
    pthread_create(&tidB, NULL, threadB_proc, NULL);

    pthread_join(tidA, NULL);
    pthread_join(tidB, NULL);

    printf("exit\n");

    return 0;
}
```

```cpp
//线程 A 函数
void *threadA_proc(void *data)
{
    printf("thread A waiting get ResourceA \n");
    pthread_mutex_lock(&mutex_A);
    printf("thread A got ResourceA \n");

    sleep(1);

    printf("thread A waiting get ResourceB \n");
    pthread_mutex_lock(&mutex_B);
    printf("thread A got ResourceB \n");

    pthread_mutex_unlock(&mutex_B);
    pthread_mutex_unlock(&mutex_A);
    return (void *)0;
}

- 线程 A 函数的过程：
  1. 先获取互斥锁 A，然后睡眠 1 秒；
  2. 再获取互斥锁 B，然后释放互斥锁 B；
  3. 最后释放互斥锁 A；
```

```cpp
//线程B函数
void *threadB_proc(void *data)
{
    printf("thread B waiting get ResourceB \n");
    pthread_mutex_lock(&mutex_B);
    printf("thread B got ResourceB \n");

    sleep(1);

    printf("thread B waiting  get ResourceA \n");
    pthread_mutex_lock(&mutex_A);
    printf("thread B got ResourceA \n");

    pthread_mutex_unlock(&mutex_A);
    pthread_mutex_unlock(&mutex_B);
    return (void *)0;
}

- 线程 B 函数的过程：
  1. 先获取互斥锁 B，然后睡眠 1 秒；
  2. 再获取互斥锁 A，然后释放互斥锁 A；
  3. 最后释放互斥锁 B；
```

运行这个程序，运行结果如下:

thread B waiting get ResourceB 
thread B got ResourceB 
thread A waiting get ResourceA 
thread A got ResourceA 
thread B waiting get ResourceA 
thread A waiting get ResourceB 
// 阻塞中... 

线程 B 在等待互斥锁 A 的释放，线程 A 在等待互斥锁 B 的释放，双方都在等待对方资源
的释放，很明显，产生了死锁问题。

### 1.4 避免死锁问题的产生

办法：破坏产生死锁的条件中的任何一个条件即可，最常见的并且可行的就是**使用资源有序分配法，来<font color="red">破坏环路等待条件</font>。**

#### 1.4.1  什么是资源有序分配法？

线程 A 和 线程 B 总是以相同的顺序申请自己想要的资源。

![](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E6%AD%BB%E9%94%81/%E8%B5%84%E6%BA%90%E6%9C%89%E5%BA%8F%E5%88%86%E9%85%8D.png)

#### 1.4.2  修改1.3代码消除死锁

```cpp
- 修改后：
//线程 B 函数访问资源的顺序，同线程 A 一样，先获取互斥锁 A，然后获取互斥锁 B
void *threadB_proc(void *data)
{
    printf("thread B waiting get ResourceA \n");
    pthread_mutex_lock(&mutex_A);
    printf("thread B got ResourceA \n");

    sleep(1);

    printf("thread B waiting  get ResourceB \n");
    pthread_mutex_lock(&mutex_B);
    printf("thread B got ResourceB \n");

    pthread_mutex_unlock(&mutex_B);
    pthread_mutex_unlock(&mutex_A);
    return (void *)0;
}

- 线程 B 函数的过程：
  1. 先获取互斥锁 A，然后睡眠 1 秒；
  2. 再获取互斥锁 B，然后释放互斥锁 B；
  3. 最后释放互斥锁 A；
```

```cpp
修改前：
//线程B函数
void *threadB_proc(void *data)
{
 printf("thread B waiting get ResourceB \n");
 pthread_mutex_lock(&mutex_B);
 printf("thread B got ResourceB \n");

sleep(1);

printf("thread B waiting get ResourceA \n");
 pthread_mutex_lock(&mutex_A);
 printf("thread B got ResourceA \n");

pthread_mutex_unlock(&mutex_A);
 pthread_mutex_unlock(&mutex_B);
 return (void *)0;
}

- 线程 B 函数的过程：

  1. 先获取互斥锁 B，然后睡眠 1 秒；
  2. 再获取互斥锁 A，然后释放互斥锁 A；
  3. 最后释放互斥锁 B；
```

--- 

# 2. 小林coding：什么是悲观锁、乐观锁？

## 2.1 互斥锁与自旋锁

### 基础概念

- 互斥锁和自旋锁是**最底层的锁**，是其他高级锁的基础。

- 互斥锁和自旋锁的目的是**保证共享资源**在任何时候都**只能被一个进/线程访问**，避免共享数据因为多进/线程的竞争而错乱。

### 加锁失败的处理方式

- 当已经有一个线程获取锁后，其他线程获取锁会失败，互斥锁和自旋锁对于获取锁失败后的处理方式是不一样的。

#### 互斥锁的处理方式

- 获取锁失败的线程会释放 CPU ，给其他线程使用。(线程切换)

<img title="" src="https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost2/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E9%94%81/%E4%BA%92%E6%96%A5%E9%94%81%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png" alt="" width="414" data-align="left">

- 线程获取锁/释放锁时，会发起系统调用，cpu的运行模式会从用户态切换到内核态，让相关内核程序上cpu运行来完成上锁或解锁操作，这样虽然简化了使用锁的难度，但也带来了性能开销成本：**两次线程上下文切换的成本**：
  
  1. 当线程获取锁失败时，内核会把该线程的状态从「运行」状态设置为「睡眠」状态，然后切换其他线程上cpu运行。
  
  2. 当锁被释放时，之前「睡眠」状态的线程会变为「就绪」状态，内核会在合适的时间，切换该线程上cpu运行。

- 线程的上下文切换的是什么？切换的是线程的私有数据、寄存器等不共享数据。

- 上下切换的耗时大概在几十纳秒到几微秒之间，如果<u>锁住的代码</u>（访问共享资源的那段代码）执行时间比较短，那可能上下文切换的时间都比你锁住的代码执行时间还要长。所以，**如果你能确定被锁住的代码执行时间很短，就不应该用互斥锁，而应该选用自旋锁。**

#### 自旋锁的处理方式

- 线程会忙等待，直到它拿到锁。另外，忙等待可以用 `while` 循环实现，不过最好是使用 CPU 提供的 `PAUSE` 指令来实现忙等待，可以减少循环等待时的耗电量。

- 自旋锁是通过 CPU 提供的 `CAS` 函数（*Compare And Swap*），在用户态完成加锁和解锁操作，不会主动产生线程上下文切换，所以相比互斥锁来说，会快一些，开销也小一些。

- 一般加锁的过程，包含两个步骤：
  
  第一步，查看锁的状态，如果锁是空闲的，则执行第二步。
  
  第二步，将锁设置为当前线程持有。
  
  CAS 函数把这两个步骤合并成一条硬件级指令，形成**原子指令**，这样就保证了这两个步骤是不可分割的，要么一次性执行完两个步骤，要么两个步骤都不执行。比如，设**锁为变量 lock**，整数 **0 表示锁是空闲状态**，整数 **pid 表示线程 ID**，那么 **CAS(lock, 0, pid) 就表示自旋锁的加锁操作，CAS(lock, pid, 0) 则表示解锁操作。**
  
  解释说明：
  
  - CAS 操作的一般形式是：CAS(location, expected, new_value)，其中 location 是要修改的共享变量，expected 是期望的值，new_value 是要设置的新值。CAS 操作只有在 location 的当前值等于 expected 时才会成功，否则它将失败。如果成功，它会将 location的值设置为 new_value。
  
  - CAS(lock, 0, pid)表示尝试将 lock的值从 0（表示未锁定状态）更改为pid（表示线程的标识符或锁定者的标识符），以此来实现锁的加锁操作，CAS(lock, pid, 0)表示尝试将 lock的值从 pid 更改回 0，以此来实现锁的解锁操作。

- **需要注意，在单核 CPU 上，需要抢占式的调度器（即不断通过时钟中断一个线程，运行其他线程）。否则，自旋锁在单 CPU 上无法使用，因为一个自旋的线程永远不会放弃 CPU。**

- 自旋锁开销少，在**多核系统下**一般**不会主动进行线程切换**，适合异步、协程等在用户态切换请求的编程方式，但如果某个线程在临界区执行的时间过长，那么自旋的线程会长时间占用 CPU 资源，所以自旋的时间和线程在临界区执行的时间是成「正比」关系。

## 2.2.  读写锁

### 基础概念

读写锁从字面意思我们也可以知道，它由「读锁」和「写锁」两部分构成，如果只读取共享资源那么就用「读锁」来加锁，如果要修改共享资源则用「写锁」来加锁。所以，**读写锁适用于能明确区分读操作和写操作的场景**。    

### 工作原理

当「写锁」没有被线程持有时，多个线程能够<u>同时持有读锁</u>（因为「读锁」是用于读取共享资源的场景，所以多个线程同时持有读锁也不会破坏共享资源的数据。），这大大提高了共享资源的访问效率。但是，一旦「写锁」被线程持有后，读线程的获取读锁的操作会被阻塞，而且其他写线程的获取写锁的操作也会被阻塞。

所以，**写锁是独占锁**，任何时刻只能有一个线程持有写锁，类似互斥锁和自旋锁，而**读锁是共享锁**，可以被多个线程同时持有。可以发现，**读写锁在读多写少的场景，能发挥出优势**。

### 读写锁的分类

根据实现的不同，读写锁可以分为「读优先锁」和「写优先锁」。

#### 读优先锁

- 特点：读优先锁希望读锁能被更多的线程持有，**以便提高读线程的并发性**。

- 工作方式：当读线程 A 先持有了读锁，写线程 B 在获取写锁的时候，会被阻塞，在线程B阻塞过程中，后续来的读线程 C 仍然可以成功获取读锁，最后直到读线程 A 和 C 释放读锁后，写线程 B 才可以成功获取写锁。

<img src="https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost2/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E9%94%81/%E8%AF%BB%E4%BC%98%E5%85%88%E9%94%81%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png" title="" alt="" width="383">

#### 写优先锁

- 特点：写优先锁希望先服务写线程。

- 工作方式：当读线程 A 先持有了读锁，写线程 B 在获取写锁的时候，会被阻塞，在阻塞过程中，后续来的读线程 C 获取读锁时会失败，于是读线程 C 被阻塞在获取读锁的操作，这样只要读线程 A 释放读锁后，写线程 B 就可以成功获取写锁。

<img title="" src="https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost2/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E9%94%81/%E5%86%99%E4%BC%98%E5%85%88%E9%94%81%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png" alt="" width="397">

#### 写优先锁和读优先锁的比较

- 读优先锁可以保证读线程不会饿死，但如果一直有读线程获取读锁，那么写线程将永远获取不到写锁，这就造成了写线程会被饿死。

- 写优先锁可以保证写线程不会饿死，但是如果一直有写线程获取写锁，读线程也会被饿死。

#### 公平读写锁

既然不管读优先锁还是写优先锁，都会造成对方可能会出现饿死的问题，那么我们就不偏袒任何一方，搞个「公平读写锁」。

##### 一种简单的实现方式

用队列把获取锁的线程放在里面排队，不管是写线程还是读线程都按照先进先出的原则加锁即可，这样读线程可以并发并且不会出现饥饿现象。

互斥锁和自旋锁都是最基本的锁，读写锁可以根据场景来选择这两种锁其中的一个进行实现。

---

## 2.3 乐观锁与悲观锁

### 悲观锁

#### 基本认识

前面提到的互斥锁、自旋锁、读写锁，都是属于悲观锁。

#### 特点

它认为多线程同时修改共享资源的概率比较高，很容易出现冲突，所以访问共享资源前，先要上锁。

### 乐观锁

#### 基本认识

介于前面提到的悲观锁的特点，那么如果多线程同时修改共享资源的概率比较低，就可以采用乐观锁。

#### 特点

它假多线程访问共享资源起冲突的概率很低，并在此基础上工作。

#### 工作方式

先修改完共享资源，再验证这段时间内有没有发生冲突，如果没有其他线程在修改资源，那么操作完成，如果发现有其他线程已经修改过这个资源，就放弃本次操作。

由此可见，乐观锁的心态是，不管三七二十一，先改了资源再说。另外，**乐观锁全程并没有加锁，所以它也叫无锁编程**。

#### 场景应用例子：在线文档

在线文档可以同时被多人编辑，如果在线文档这个共享资源使用了悲观锁，那么只要有一个用户正在编辑在线文档，此时其他用户就无法打开相同的文档了（因为访问在线文档代码被上锁了），这用户体验当然不好了，所以实现多人同时编辑，实际上是用了乐观锁，它允许多个用户打开同一个文档进行编辑，编辑完提交之后才验证修改的内容是否有冲突。

- 怎么样才算发生冲突？
  
  比如用户 A 先在浏览器上编辑在线文档，之后用户 B 也在浏览器上打开了相同的文档进行编辑，但是用户 B 比用户 A 提交早，这一过程用户 A 是不知道的，当 A 提交修改完的内容时，那么 A 和 B 之间<u>并行修改</u>（同时修改）的地方就会发生冲突。

- 服务端要怎么验证是否冲突了呢？通常方案如下：
  
  由于发生冲突的概率比较低，所以先让用户编辑文档，浏览器在下载文档时会记录服务端发过来的响应中包含的文档版本号。当用户提交修改时，发给服务端的请求会带上记录下来的文档版本号，服务器收到后将自己这里的文档的版本号与用户提交的文档的版本号进行比较，如果版本号不一致则提交失败，如果版本号一致则修改成功，然后服务端文档的版本号更新为新的版本号。

实际上，我们常见的 SVN 和 Git 也是用了乐观锁的思想，先让用户编辑代码，然后提交的时候，通过版本号来判断是否产生了冲突，发生了冲突的地方，需要我们自己修改后，再重新提交。

#### 乐观锁的使用建议

乐观锁虽然去除了加锁解锁的操作，但是一旦发生冲突，重试的成本非常高，所以**只有在冲突概率非常低，且加锁成本非常高的场景时，才考虑使用乐观锁。**

## 总结

不管使用的哪种锁，代码的加锁范围应该尽可能的小，这样执行速度会比较快，再来，使用上了合适的锁，就会快上加快了。
