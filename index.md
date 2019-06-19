# 一、java io 概述

## 1.1 相关概念
### java io
Java IO即Java 输入输出系统。不管我们编写何种应用，都难免和各种输入输出相关的媒介打交道，其实和媒介进行IO的过程是十分复杂的，这要考虑的因素特别多，比如我们要考虑和哪种媒介进行IO（文件、控制台、网络），我们还要考虑具体和它们的通信方式（顺序、随机、二进制、按字符、按字、按行等等）。Java类库的设计者通过设计大量的类来攻克这些难题，这些类就位于java.io包中。

### 流
在Java IO中，流是一个核心的概念。流从概念上来说是一个连续的数据流。你既可以从流中读取数据，也可以往流中写数据。流与数据源或者数据流向的媒介相关联。在Java IO中流既可以是字节流(以字节为单位进行读写)，也可以是字符流(以字符为单位进行读写)。

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3
### IO相关的媒介
Java的IO包主要关注的是从原始数据源的读取以及输出原始数据到目标媒介。以下是最典型的数据源和目标媒介：

1.文件
2.管道
3.网络连接
4.内存缓存
5.System.in, System.out, System.error(注：Java标准输入、输出、错误输出)
- Bulleted
- List
# 二 、java io类库的框架
## 2.1 java io的类型
虽然java io类库庞大，但总体来说其框架还是很清楚的。从读媒介和写媒介的维度看，Java IO可以分为：

输入流：InputStream和Reader
输出流：OutputStream和Writer
而从其处理流的类型的维度上看，Java IO又可以分为：

字节流：InputStream和OutputStream
字符流：Reader和Writer
## 2.2 IO中的输入字节流
       下面是IO中输入字节流的继承图。
o  InputStream
  o  ByteArrayInputStream
  o  FileInputStream
  o  FilterInputStream
    o  BufferedInputStream
    o  DataInputStream
    o  LineNumberInputStream
    o  PushbackInputStream
  o  ObjectInputStream
  o  PipedInputStream
  o  SequenceInputStream
  o  StringBufferInputStream
 在上面的关系图中可以看出：
1.InputStream是所有的输入字节流的父类，它是一个抽象类。
2. ByteArrayInputStream、StringBufferInputStream、FileInputStream是三种基本的介质流，它们分别将Byte数组、StringBuffer、和本地文件中读取数据。PipedInputStream是从与其它线程共用的管道中读取数据，与Piped相关的知识会用专门的一小节讲解。
3. ObjectInputStream和所有FilterInputStream的子类都是装饰流（装饰器模式的主角）。
## 2.3 IO中的输出字节流
       下面是IO中输出字节流的继承图。
o  OutputStream
  o  ByteArrayOutputStream
  o  FileOutputStream
  o  FilterOutputStream
    o  BufferedOutputStream
    o  DataOutputStream
    o  PrintStream
  o  ObjectOutputStream
  o  PipedOutputStream
在上面的关系图中可以看出：
1.OutputStream是所有的输出字节流的父类，它是一个抽象类。
2. ByteArrayOutputStream、FileOutputStream是两种基本的介质流，它们分别向Byte数组、和本地文件中写入数据。PipedOutputStream是向与其它线程共用的管道中写入数据。
3. ObjectOutputStream和所有FilterOutputStream的子类都是装饰流。
## 2.4 IO中的输入字符流
       下面是IO中输入字符流的继承图。

o  Reader
  o  BufferedReader
    o  LineNumberReader
  o  CharArrayReader
  o  FilterReader
  o  PushbackReader
  o  InputStreamReader
  o  FileReader
  o  PipedReader
  o  StringReader
在上面的关系图中可以看出：
1.Reader是所有的输入字符流的父类，它是一个抽象类。
2.CharReader、StringReader是两种基本的介质流，它们分别将Char数组、String中读取数据。PipedReader是从与其它线程共用的管道中读取数据。
3. BufferedReader很明显就是一个装饰器，它和其子类负责装饰其它Reader对象。
4.FilterReader是所有自定义具体装饰流的父类，其子类PushbackReader对Reader对象进行装饰，会增加一个行号。
5.InputStreamReader是一个连接字节流和字符流的桥梁，它将字节流转变为字符流。FileReader可以说是一个达到此功能、常用的工具类，在其源代码中明显使用了将FileInputStream转变为Reader的方法。我们可以从这个类中得到一定的技巧。

## 2.5 IO中的输出字符流
下面是IO中输出字符流的继承图。

o  Writer
  o  BufferedWriter
  o  CharArrayWriter
  o  FilterWriter
  o  OutputStreamWriter
    o  FileWriter
  o  PipedWriter
  o  PrintWriter
  o  StringWriter
在上面的关系图中可以看出：
1.Writer是所有的输出字符流的父类，它是一个抽象类。
2. CharArrayWriter、StringWriter是两种基本的介质流，它们分别向Char数组、String中写入数据。PipedWriter是向与其它线程共用的管道中写入数据， 
3. BufferedWriter是一个装饰器为Writer提供缓冲功能。
4.PrintWriter和PrintStream极其类似，功能和使用也非常相似。
5.OutputStreamWriter是OutputStream到Writer转换的桥梁，它的子类FileWriter其实就是一个实现此功能的具体类（具体可以研究一下Source Code）。
## InputStream
InputStream 是输入字节流部分，装饰器模式的顶层类。主要规定了输入字节流的公共方法。


 

package java.io;
 
public abstract class InputStream implements Closeable {
 
    private static final int SKIP_BUFFER_SIZE = 2048;  //用于skip方法，和skipBuffer相关
 
    private static byte[] skipBuffer;    // skipBuffer is initialized in skip(long), if needed.
 
 
    public abstract int read() throws IOException;      //从输入流中读取下一个字节，
 
                                                        //正常返回0-255，到达文件的末尾返回-1
 
                                                        //在流中还有数据，但是没有读到时该方法会阻塞（block）
 
                                                        //Java IO和New IO的区别就是阻塞流和非阻塞流
 
                                                        //抽象方法哦！不同的子类不同的实现哦！
 
 
 
         //将流中的数据读入放在byte数组的第off个位置先后的len个位置中
 
         //放回值为放入字节的个数。
 
    public int read(byte b[], int off, int len) throws IOException {         
 
         if (b == null) {
 
             throw new NullPointerException();
 
         } else if (off < 0 || len < 0 || len > b.length - off) {
 
             throw new IndexOutOfBoundsException();
 
         } else if (len == 0) {
 
             return 0;
 
         }  //检查输入是否正常。一般情况下，检查输入是方法设计的第一步
         int c = read();                         //读取下一个字节
         if (c == -1) {    return -1;   }        //到达文件的末端返回-1
         b[off] = (byte)c;                       //放回的字节downcast
         int i = 1;                              //已经读取了一个字节
         try {
             for (; i < len ; i++) {             //最多读取len个字节，所以要循环len次
                   c = read();                   //每次循环从流中读取一个字节
 
                                                 //由于read方法阻塞，
                                                 //所以read(byte[],int,int)也会阻塞
                   if (c == -1) {  break;  }     //到达末尾，理所当然放回-1
                   b[off + i] = (byte)c;         //读到就放入byte数组中
             }
         } catch (IOException ee) {     }
         return i;
 
         //上面这个部分其实还有一点比较重要，int i = 1;在循环的外围，或许你经常见到，
 
         //或许你只会在循环是才声明，为什么呢？
 
         //声明在外面，增大了变量的生存周期（在循环外面），所以后面可以return返回
 
         //极其一般的想法。在类成员变量生命周期中使用同样的理念。
         //在软件设计中，类和类的关系中也是一样的。
    } //这个方法在利用抽象方法read，某种意义上简单的Templete模式。
    public int read(byte b[]) throws IOException {
          return read(b, 0, b.length);
    } //利用上面的方法read(byte[] b)
 
    public long skip(long n) throws IOException {
         long remaining = n;                                  //方法内部使用的、表示要跳过的字节数目，
//使用它完成一系列字节读取的循环
         int nr;
         if (skipBuffer == null)
             skipBuffer = new byte[SKIP_BUFFER_SIZE];         //初始化一个跳转的缓存
         byte[] localSkipBuffer = skipBuffer;                 //本地化的跳转缓存
         if (n <= 0) {    return 0;      }                    //检查输入参数，应该放在方法的开始
         while (remaining > 0) {                              //一共要跳过n个，每次跳过部分，循环
             nr = read(localSkipBuffer, 0, (int) Math.min(SKIP_BUFFER_SIZE, remaining));
                                                              //利用上面的read(byte[],int,int)方法尽量读取n个字节  
             if (nr < 0) {  break;    }                       //读到流的末端，则返回
             remaining -= nr;                                 //没有完全读到需要的，则继续循环
         }       
         return n - remaining;                                //返回时要么全部读完，要么因为到达文件末端，读取了部分
    }
 
 
 
    public int available() throws IOException {               //查询流中还有多少可以读取的字节
                   return 0;
    }
 
 //该方法不会block。在java中抽象类方法的实现一般有以下几种方式：
 
//1.抛出异常（java.util）；2.“弱”实现。象上面这种。子类在必要的时候覆盖它。
 
//3.“空”实现。下面有例子。
 
    public void close() throws IOException {}
         //关闭当前流、同时释放与此流相关的资源
 
    public synchronized void mark(int readlimit) {}
 
         //在当前位置对流进行标记，必要的时候可以使用reset方法返回。
 
         //markSupport可以查询当前流是否支持mark
 
    public synchronized void reset() throws IOException {
 
                   throw new IOException("mark/reset not supported");
 
    }
 
         //对mark过的流进行复位。只有当流支持mark时才可以使用此方法。
 
         //看看mark、available和reset方法。体会为什么？！
 
    public boolean markSupported() {           //查询是否支持mark
 
                   return false;
 
    }                 //绝大部分不支持，因此提供默认实现，返回false。子类有需要可以覆盖。
 
}

2 FilterInputStream
       这是字节输入流部分装饰器模式的核心。是我们在装饰器模式中的Decorator对象，主要完成对其它流装饰的基本功能。下面是它的源代码:

package java.io;
 
//该类对被装饰的流进行基本的包裹。不增加额外的功能。
 
//客户在需要的时候可以覆盖相应的方法。具体覆盖可以在ByteInputStream中看到！
 
public class FilterInputStream extends InputStream {
 
    protected volatile InputStream in;                       //将要被装饰的字节输入流
 
    protected FilterInputStream(InputStream in) {   //通过构造方法传入此被装饰的流
 
                   this.in = in;
 
    }
         //装饰器的代码特征：被装饰的对象一般是装饰器的成员变量
 
         //上面几行可以看出。
 
         //下面这些方法，完成最小的装饰――0装饰，只是调用被装饰流的方法而已
 
    public int read() throws IOException {
 
                   return in.read();
 
    }
 
    public int read(byte b[]) throws IOException {
 
                   return read(b, 0, b.length);
 
    }
 
    public int read(byte b[], int off, int len) throws IOException {
 
                   return in.read(b, off, len);
 
    }
 
    public long skip(long n) throws IOException {
 
                   return in.skip(n);
    }
 
    public int available() throws IOException {
 
                   return in.available();
 
    }
 
    public void close() throws IOException {
 
                   in.close();
 
    }
 
    public synchronized void mark(int readlimit) {
 
                   in.mark(readlimit);
 
    }
 
    public synchronized void reset() throws IOException {
 
                   in.reset();
 
    }
 
    public boolean markSupported() {
 
                   return in.markSupported();
 
}
 
//以上的方法，都是通过调用被装饰对象in完成的。没有添加任何额外功能
 
//装饰器模式中的Decorator对象，不增加被装饰对象的功能。
 
//它是装饰器模式中的核心。
}


       以上分析了所有字节输入流的公共父类InputStream和装饰器类FilterInputStream类。他们是装饰器模式中两个重要的类。更多细节请阅读博客中装饰器模式的文章。下面将讲解一个具体的流ByteArrayInputStream，不过它是采用适配器设计模式。


3 ByteArray到ByteArrayInputStream的适配
// ByteArrayInputStream内部有一个byte类型的buffer。
 
//很典型的适配器模式的应用――将byte数组适配流的接口。
 
//下面是源代码分析：
 
package java.io;
 
 
public class ByteArrayInputStream extends InputStream {
 
    protected byte buf[];                //内部的buffer，一般通过构造器输入
 
protected int pos;                   //当前位置的cursor。从0至byte数组的长度。
 
//byte[pos]就是read方法读取的字节
 
    protected int mark = 0;           //mark的位置。
 
    protected int count;                          //流中字节的数目。不一定与byte[]的长度一致？？？
 
    public ByteArrayInputStream(byte buf[]) {//从一个byte[]创建一个ByteArrayInputStream
 
         this.buf = buf;                                                      //初始化流中的各个成员变量
 
        this.pos = 0;
 
         this.count = buf.length;                              //count就等于buf.length
 
    }
 
    public ByteArrayInputStream(byte buf[], int offset, int length) {                //构造器
 
         this.buf = buf;
 
        this.pos = offset;                                                                                      //与上面不同
 
         this.count = Math.min(offset + length, buf.length);
 
        this.mark = offset;                                                                                             //与上面不同
 
    }
 
    public synchronized int read() {                                           //从流中读取下一个字节
 
                   return (pos < count) ? (buf[pos++] & 0xff) : -1; //返回下一个位置的字节
 
                                                                                                                //流中没有数据则返回-1
 
    }
 
         //下面这个方法很有意思！从InputStream中可以看出其提供了该方法的实现。
 
         //为什么ByteArrayInputStream要覆盖此方法呢？
 
         //同样的我们在Java Collections Framework中可以看到：
 
//AbstractCollection利用iterator实现了Collecion接口的很多方法。但是，
 
//在ArrayList中却有很多被子类覆盖了。为什么如此呢？？
 
    public synchronized int read(byte b[], int off, int len) {
 
         if (b == null) {                                                               //首先检查输入参数的状态是否正确
 
             throw new NullPointerException();
 
         } else if (off < 0 || len < 0 || len > b.length - off) {
 
             throw new IndexOutOfBoundsException();
 
         }
 
         if (pos >= count) {             return -1;             }
 
         if (pos + len > count) {      len = count - pos;         }
 
         if (len <= 0) {           return 0;     }
 
         System.arraycopy(buf, pos, b, off, len);                     //java中提供数据复制的方法
 
         pos += len;
 
         return len;
 
    }
 
         //出于速度的原因！他们都用到System.arraycopy方法。想想为什么？
 
         //某些时候，父类不能完全实现子类的功能，父类的实现一般比较通用。
 
//当子类有更有效的方法时，我们会覆盖这些方法。这样可是不太OO的哦！
 
         //下面这个方法，在InputStream中也已经实现了。
 
//但是当时是通过将字节读入一个buffer中实现的，好像效率低了一点。
 
//看看下面这段代码，是否极其简单呢？！
 
    public synchronized long skip(long n) {
 
         if (pos + n > count) {    n = count - pos;       }        //当前位置，可以跳跃的字节数目
 
         if (n < 0) {       return 0;     }                                    //小于0，则不可以跳跃
 
         pos += n;                                                                              //跳跃后，当前位置变化
 
         return n;
 
    }                                    //比InputStream中的方法简单、高效吧！
 
    public synchronized int available() {
 
                   return count - pos;
 
    }
 
         //查询流中还有多少字节没有读取。
 
//在我们的ByteArrayInputStream中就是当前位置以后字节的数目。  
 
    public boolean markSupported() {                   
 
                   return true;
 
    }        //ByteArrayInputStream支持mark所以返回true
 
    public void mark(int readAheadLimit) {            
 
                   mark = pos;
 
    }
 
//在流中当前位置mark。
 
//在我们的ByteArrayInputStream中就是将当前位置赋给mark变量。
 
//读取流中的字节就是读取字节数组中当前位置向后的的字节。
 
    public synchronized void reset() {
 
                   pos = mark;
 
    }
 
         //重置流。即回到mark的位置。
 
    public void close() throws IOException {   }
 
         //关闭ByteArrayInputStream不会产生任何动作。为什么？仔细考虑吧！！
 
}


上面我们分3小节讲了装饰器模式中的公共父类（对应于输入字节流的InputStream）、Decorator（对应于输入字节流的FilterInputStream）和基本被装饰对象（对应于输入字节流的媒体字节流）。下面我们就要讲述装饰器模式中的具体的包装器（对应于输入字节流的包装器流）。

