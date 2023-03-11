参考文章：https://www.rapid7.com/blog/post/2019/06/12/heap-overflow-exploitation-on-windows-10-explained/


刚接触堆的利用相关的知识，正好老板让研究winword的一个由整数溢出产生的堆相关的漏洞，网上搜了一下堆利用相关的文章，看到了上面这篇，感觉还不错，在这里做一下笔记


文中有一部分介绍了如何操作堆的布局

大概意思就是我们在触发LFH之前连续申请几块内存，然后释放中间的一块，之后再申请内存，然后再释放一块，然后再申请内存，变化过程如下：



[源代码](https://github.com/wqreytuk/heap_feng_shui/blob/main/manipulate_heap.cpp)

对代码进行了一点改动，在几个关键操作前后加上了用于下断点的test函数调用

五次断点触发之后，我搜集到了[内存布局相关信息](https://github.com/wqreytuk/heap_feng_shui/tree/main/heap_memory_layout_of_every_step)

可以看到，bstr和数组都按照预想落入到了被释放的第四块和第五块内存中
