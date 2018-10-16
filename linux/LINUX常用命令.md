## LINUX常用命令

查看文件的时间戳，stat：

```
[root@server0 tmp]# stat root-fileFile: ‘root-file’Size: 0         	Blocks: 0          IO Block: 4096   regular empty fileDevice: 801h/2049d	Inode: 20204276    Links: 1Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)Context: unconfined_u:object_r:user_tmp_t:s0Access: 2018-08-11 17:04:46.531472731 +0800Modify: 2018-08-11 17:04:46.531472731 +0800Change: 2018-08-11 17:04:46.531472731 +0800`
 Birth: -
```

压缩、查看、解压缩：

```
归档（压缩）
# tar –czvf : tar + gzip 
# tar –cjvf: tar + bzip2
# tar –cJvf: tar + xz

查看
# tar –tzvf : tar + gzip 
# tar –tjvf: tar + bzip2
# tar –tJvf: tar + xz
解压缩
# tar –xzvf : tar + gzip 
# tar –xjvf: tar + bzip2
# tar –xJvf: tar + xz

```

查看密码相关策略，如过期策略等。



