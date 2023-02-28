最新版记录，修正了第一版的错误<br>
第一版认为raid和rp应该独立，这个观点应该是错误的
<br>
<img src="pic/simple_ex.png">
<br>
目前的观点来看，rp应该在raid中，raid分配strip需要写P,复制写不需要如图，将P留出来便于以后的转换。
<br>所以复制写和raid_stripe的数据结构基本一致，只是在进行复制写的时候分配两个stripe写入。
<br>在进行转换时，删去一个stripe，并计算P。