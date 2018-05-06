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

## 用户组及权限

### 用户组管理

- `usermod -a -G group user`：把 user 用户添加到 group 组，例如：`usermod -a -G www-data jerry` （a 代表 append）；
- `groups user`：查看 user 用户所在的组；
- `vim /etc/group`：查看所有用户组的信息

### 文件所有者

- `chown user:group file` ：改变文件的所有者为指定的用户和组

### 权限

| 缩写 | 全拼   | 对应数字 |
| ---- | ------ | -------- |
| r    | read   | 4        |
| w    | write  | 2        |
| e    | excute | 1        |
| -    | 无权限 | 0        |

`chmod 764 file`：

- `7=r+w+e`：代表文件所属的 user 拥有所有权限；
- `6=4+2`：代表文件所属的 group 拥有读写权限；
- `4=r`：代表其他用户 others 只拥有读的权限



## vim

 vim 分为三个模式：

(1). 命令模式，刚进入 vim 时就是命令模式；

|     命令      |                 作用                  |
| :-----------: | :-----------------------------------: |
|     hjkl      |               左下上右                |
| ctrl+f/ctrl+b |             向下/向上翻页             |
|    G/nG/gg    | 移动到最后一行/到第n行/到第一行（1g） |
|  n\<ENTER\>   |              向下移动n行              |
|      dd       |              删除当前行               |
|      yy       |              复制当前行               |
|      p/P      |  将复制的数据粘贴在当前行的下方/上方  |
|   u/ctrl+r    |     还原前一个动作/重做前一个动作     |

(2). 输入模式，命令模式下 输入 `i` 就进入了普通编辑模式，此模式下按`esc`切换到命令模式 ；

(3). 底线命令模式，命令模式下输入 `:` 就进入了底线命令模式；

|     命令      |        作用         |
| :-----------: | :-----------------: |
|  :set number  |      显示行号       |
|    :q/:q!     | 退出/不保存强制退出 |
|    :w/:wq     |   保存/保存后退出   |
| :w [filename] |       另存为        |


## 竖线（|）

管道符号，用法：`command1 | command2`，把 command1 的执行结果作为 command2 的输入传递给 command2。

比如，`ls | grep server` ，首先执行 `ls` 命令，本来输出要通过 `stdout` 输出到屏幕上，加上 `|` 之后，就会把这个输出输入到 `grep` 中，效果是显示当前文件夹下包含 `server` 字符的文件/文件夹。

## grep

> **grep**（global search regular expression(RE) and print out the line），全面搜索正则表达式并把行打印出来。

```bash
grep [option] pattern file
```

`grep "name" a.txt` ：打印 a.txt 文件中包含 name 的行。 

|     option      |                     作用                     |
| :-------------: | :------------------------------------------: |
|       -i        |            大小写不敏感，默认敏感            |
|       -c        |                 匹配到的行数                 |
|       -n        |                匹配部分的行号                |
|       -e        | 包含多个匹配模式  <code>ls &#124;  grep -e 'se' -e 'v'</code> |
| -A/-B/-C number |          打印匹配行前/后/前后 n 行           |

