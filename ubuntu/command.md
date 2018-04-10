# ubuntu命令

## 解压缩

> 打包是指将一大堆文件或目录变成一个总的文件，常用的打包程序是 tar；压缩是将一个大的文件通过一些压缩算法变成一个小文件。tar 工具本身不具有压缩功能，是调用其他压缩工具实现文件压缩的。

|   格式   |           解压            |               压缩                |
| :------: | :-----------------------: | :-------------------------------: |
|   tar    |   tar xvf fileName.tar    |   tar cvf fileName.tar dirName    |
| .tar.gz  | tar zxvf fileName.tar.gz  | tar zcvf fileName.tar.gz dirName  |
| .tar.bz2 | tar jxvf FileName.tar.bz2 | tar jcvf FileName.tar.bz2 DirName |
|   .zip   |    unzip FileName.zip     |     zip FileName.zip DirName      |

- A 新增压缩文件到已存在的压缩
- c 建立新的压缩文件
- d 记录文件的差别
- r 添加文件到已经压缩的文件
- u 添加改变了和现有的文件到已经存在的压缩文件
- x 从压缩的文件中提取文件
- t 显示压缩文件的内容
- z 支持 gzip 解压文件
- j 支持 bzip2 解压文件
- Z 支持 compress 解压文件
- v 显示操作过程
- k 保留原有文件不覆盖
- m 保留文件不被覆盖
- W 确认压缩文件的正确性
- C 切换到指定目录
- f 指定压缩文件

## vim

- 按`CTRL+V`启用块可视模式，之后移动光标选择块；
- 按`i`进入编辑模式，输入相关内容；
- 按`ESC`进入普通模式。