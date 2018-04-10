# IO

## 字符流

> 操作文本文件时使用字符流比较方便，含有编码表，默认系统编码表。

### Reader

#### FileReader

> Reader 是一个抽象类，FileReader 是 InputStreamReader的子类，就是封装了码表的 InputStreamReader。

- int read()：读取下个字符，如果已到达流的末尾，则返回 -1 ；
- int read(char[] chars)：将字符读入字符数组，返回读取的字符数，如果到达流的末尾，则返回 -1 。

```java
Reader reader=new FileReader("/test.txt");
int num=0; 
char[] chs=new char[1024];//一般设置为1024，大小为2k 
while((num=reader.read(chs))!=-1){ 
	System.out.println(String.valueOf(chs, 0, num)); 
}
reader.close();

/*
使用 BufferedReader 缓冲流读取流效率高
*/
BufferedReader bufReader=new BufferedReader(reader);
String lindeStr=null;
//读取一行字符串，不包含终止符，如果已达到流末尾，则返回 null
while((lindeStr=bufReader.readeLine())!=null){ 
	System.out.println(lindeStr); 
} 
```

#### InputStreamReader

InputStreamReader 是字符流，是字节流通向字符流的桥梁，它使用指定的 charset 读取字节并将其解码为字符，即把字节流转换为字符流。 

- InputStreamReader(InputStream in)  ：创建一个使用默认字符集的 InputStreamReader。 


- InputStreamReader(InputStream in, String charsetName)  ： 创建使用指定字符集的 InputStreamReader。 

### Writer

#### FileWriter

> Writer 是抽象类，FileWriter 是 OutputStreamWriter 的子类，就是封装了码表的OutputStreamWriter。

```java
FileWriter fw=new FileWriter("/test.txt");//文件会被创建到指定目录下  
fw.write("hello"); //将数据写到流里 
fw.flush(); //将流里的数据写入到文件
fw.close();

/*
使用 BufferedWriter 缓冲写入流效率高
*/ 
BufferedWriter writer=new BufferedWriter(Writer writer); 
writer.write("abcd"); 
writer.newLine(); 
```

## 字节流

> 当操作图像、视频等非文本文件时要使用字节流。操作字节，不含编码表。

### InputStream

#### FileInputStream

- int read()：读取下个字节，如果已到达流的末尾，则返回 -1 ；
- int read(byte[] b)：将字节读入字节数组，返回读取的字节数数，如果到达流的末尾，则返回 -1 。

### OutputStream

#### FileOutputStream

- void write( int b )：往流中写一个字节b ；
- void write( byte b[ ] )：往流中写一个字节数组b ；
- void write( byte b[ ], int off, int len )：把字节数组 b 中从下标 off 开始长度为 len 的字节写入流中 。

#### BufferedOutputStream

OutputStream 也有 void write( byte b[ ], int off, int len ) 方法，也就是具有缓存器功能，原理都是一样的。不同的是 BufferedOutputStream 中会将数据凑整之后再一起写入到底层 IO ，这样就减少了 IO 操作（在内存中操作比在硬盘 IO 操作快很多）。

?> 如果要写入的数据不满 8 kb，则 BufferOutputStream 不会将这些数据写入到底层 IO ，造成数据丢失，因此需要调用 flush（）方法，强制将 byte[] 数组中的数据写入到底层 IO。FileOutputStream 类中的 flush() 方法为空，因此使用 FileOutputStream 对象进行读写操作时不需要调用 flush() 方法。

!> PrintWriter 里面其实操作的就是一个 BufferedWriter 对象，因此也需要调用 flush() 方法来刷新。

```java
class BufferedOutputStream extends FilterOutputStream {
    protected byte buf[]=new byte[8192]; //创建一个 8k 大小的 byte[] 数组，用来充当缓存器
    protected int count; //用来记录当前缓存器（byte[] 数组）中的字节数量的

	public synchronized void write(byte b[], int off, int len) throws IOException {
        
        //如果传进来的数组长度大于buf 数组的长度，则直接调用 OutputStream 对象的write方法
        if (len >= buf.length) {
            flushBuffer();
            out.write(b, off, len);
            return;
        }
        
        //验证BufferedOutputStream 对象中 byte[] 数组剩下的空间能否装得下传进来的数组，如果不能则先将当前buf数组中数据写入底层io流中
        if (len > buf.length - count) {
            flushBuffer();
        }
        
        // 下面是与 FileOutputStream.write(byte[] bytes) 方式不同的地方
        //如果当前 BufferedOutputStream 对象中 byte[] 数组没有被填充满，则将传进来的数组复制到当前类对象buf数组中，同时更新count的值
        System.arraycopy(b, off, buf, count, len);
        count += len;
    }
    
   //将不满8192个字节数组中的数据发送出去，同时将count置零
    private void flushBuffer() throws IOException {
        if (count > 0) {
            out.write(buf, 0, count);
            count = 0;
        }
    }

    //将 bye[] 数组中未满8192个字节的数据写入底层 io 中
    public synchronized void flush() throws IOException {
        flushBuffer();
        out.flush();
    }
}
```

## File

> 用来将文件或者文件夹封装成对象，方便对文件与文件夹的属性信息进行操作。因为流对象不能操作文件夹及属性信息，所以 File 对象弥补了流的不足。File 对象可以作为参数传递给流的构造方法。 

```java
String separator=File.separator; //与系统有关的路径分割符
File[] files=File.listRoots(); //列出电脑里的所有盘符 
String name=file.getName(); //只获取文件或者文件夹名 
String path=file.getPath(); //返回构造 File对象 时的字符串,比如new File("something"),返回something
File[] files=file.listFiles(); //列出当前文件夹下的所有 File 对象 
```

打印文件下的目录及子目录下的所有文件：

```java
public class Test {
    public static void main(String[] args) throws Exception {
        File file = new File("/home/jerry/桌面/dir");
        listFile(file,0);
    }
    public static void listFile(File file,int level) {
        if (file == null || !file.exists())
            System.out.println("文件不存在");
        System.out.println(printSeparator(level++)+file.getName());
        if (file.isDirectory()) {
            for (File childFile : file.listFiles())
                listFile(childFile,level); //递归调用
        }
    }
    public static String printSeparator(int level){
        StringBuilder sBuilder=new StringBuilder();
        for (int i = 0; i < level; i++) {
            sBuilder.append(" ");
        }
        return sBuilder.toString();
    }
}
```

