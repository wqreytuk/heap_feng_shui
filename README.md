参考文章：https://www.rapid7.com/blog/post/2019/06/12/heap-overflow-exploitation-on-windows-10-explained/

测试环境：windows 10 21H2 19044.2604 x64

刚接触堆的利用相关的知识，正好老板让研究winword的一个由整数溢出产生的堆相关的漏洞，网上搜了一下堆利用相关的文章，看到了上面这篇，感觉还不错，在这里做一下笔记


文中有一部分介绍了如何操作堆的布局

大概意思就是我们在触发LFH之前连续申请几块内存，然后释放中间的一块，之后再申请内存，然后再释放一块，然后再申请内存，变化过程如下：

![image](https://user-images.githubusercontent.com/48377190/224503037-478a4a05-a70f-44a7-bfc8-17988dd93eaf.png)

![image](https://user-images.githubusercontent.com/48377190/224503044-185d52f5-4ba0-4cdb-9989-35648fe3a3b7.png)

![image](https://user-images.githubusercontent.com/48377190/224503058-39e9b19f-a354-4bc4-908c-9f5cce5453ac.png)

![image](https://user-images.githubusercontent.com/48377190/224503067-a4722caa-00bb-42a5-9e4d-647da4ed8bec.png)

![image](https://user-images.githubusercontent.com/48377190/224503074-2fc076e0-caf8-49ef-a8c3-008079122123.png)


[源代码](https://github.com/wqreytuk/heap_feng_shui/blob/main/manipulate_heap.cpp)

对代码进行了一点改动，在几个关键操作前后加上了用于下断点的test函数调用

五次断点触发之后，我搜集到了[内存布局相关信息](https://github.com/wqreytuk/heap_feng_shui/tree/main/heap_memory_layout_of_every_step)

**备注，之前的代码有点问题，就是获取对象的地址值的时候我用的是int，但是int只有4bytes（地址长度是8字节），所以最后那个数组存储的值不太正确，后面改成了long**

可以看到，bstr（的一部分）和数组（的一部分）都按照预想被分配到了前面被释放的第四块和第五块内存中
