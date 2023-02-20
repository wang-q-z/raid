假设：
上层给定一些请求,设定md会把请求分配给raid5来执行
FusionRAID全在raid5的基础上修改
# 接口
RAID5 I/O 从 raid5.c 5766行开始   

/*
*md_write_start函数，该函数判断是否需要更新元数据。由于一些raid算法带有冗余特性，比如raid1，raid5，那么开始写数据时，就要进行更新元数据，
*这防止写数据不成功导致数据不正确，在阵列再次启动时就会发起同步操作。写完之后还要更新元数据。
*MD是通过in_sync字段来判断的。如果in_sync=1，说明阵列是同步的。这时就有一个疑问，不能来一次写请求就更新二次元数据吧？
*确实，md为了防止这样事情发生，引入了一个定时器，即200ms内没有连续的写请求发过来就更新元数据。这个功能通过safemode值的重载来完成。
*/

在md_write_start()函数后判断大小写

```c
raid5.c 5801行第一个TODO
bool is_large(bi)判断大小写  如果是大写 就返回1 否则返回0
对于小写，进入init_small_write()函数中，对应raid中的分配，要更新map
```
# 映射表

raid5.h  

对于map,map应该像r5conf一样是个全局的
raid5.h 717行
## BMP
论文中通过User logical address space 来对应查表， 实现中根据下发md的bio
主要要保存
```c
P W FLBN  文章中说  P(1) W(1) FLBN(38)一共40bits 可调整   
W  0---RAID   1---replicated
P 用来记录更新  0---未更新   1---记录链表头的地址保存更新的数据页     链表
```
## BPT

# 复制写处理 
在raid5.h中第717行rpconf需要完成复制写的全局config
在raid5.c 5771行is_large判断大小


## 未考虑加锁，同步等细节
### init_small_write()
在这个函数中根据bio完成

# 读处理

# 转换处理

# 错误管理

Q：<br>
1.大小写的转换发生在什么时候
2.bio记录了什么东西可以提供小写的转换



bio
https://blog.csdn.net/geshifei/article/details/119959905
