创建swap分区

1、查看内存的情况（此时swap行为0）

```
free -m
```

初始时`Swap` 相关数据都是0。

2、创建swap文件（本文创建了2G），并使之生效
a.创建用于交换分区的文件:
`dd if=/dev/zero of=/mnt/swap bs=1M count=2048`
*注：block_size、number_of_block 大小可以自定义，比如 bs=1M count=1024 代表设置 1G 大小 SWAP 分区。*
b.设置交换分区文件：
`mkswap /mnt/swap`

3、查看内核参数vm.swappiness中的数值是否为0，如果为0则根据实际需要调整
查看参数：`cat /proc/sys/vm/swappiness`

当 swappiness 内容的值为 0 时，表示最大限度地使用物理内存，物理内存使用完毕后，才会使用 SWAP 分区。当 swappiness 内容的值为 100 时，表示积极地使用 SWAP 分区，并且把内存中的数据及时地置换到 SWAP 分区。
可以使用下述方法临时修改此参数，假设我们配置为空闲内存少于 10% 时才使用 SWAP 分区：

设置参数：`sysctl -w vm.swappiness=10`

4、启用 swap 分区
`swapon /mnt/swap`
`echo “/data/swap swap swap defaults 0 0” >> /etc/fstab`

5、再次使用`cat /proc/swaps` 查看swap分区是否启动

### 关闭 SWAP

当系统出现内存不足时，开启 SWAP 可能会因频繁换页操作，导致 IO 性能下降。如果要关闭 SWAP，可以采用如下方法。
1、`free -m` 查询 SWAP 分区设置：

2、使用命令`swapoff` 关闭 SWAP，比如：
`swapoff /mnt/swap`

3、修改 `/etc/fstab` 文件，删除或注释相关配置，取消 SWAP 的自动挂载：

4、 通过 `free -m` 确认 SWAP 已经关闭。

5、 swappiness 参数调整：
可以使用下述方法临时修改此参数，这里配置为 0%：
`echo 0 >/proc/sys/vm/swappiness`
若需要永久修改此配置，在系统重启之后也生效的话，可以修改 `/etc/sysctl.conf`文件，并增加以下内容：
`vm.swappiness=0`