# 一、NIO的概述
JDK 1.4中的java.nio.*包中引入新的Java I/O库，其目的是提高速度。实际上，“旧”的I/O包已经使用NIO重新实现过，即使我们不显式的使用NIO编程，也能从中受益。  
**我们测试一下使用传统的IO复制文件和NIO复制文件性能：**
```java
import java.io.*;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class SimpleFileTransferTest{
    // 传统IO复制文件
    private long transferFile(File source,File des) throws IOException{
        long startTime = System.currentTimeMills();
        if(!des.exists()){
            des.createNewFile();
        }
        BUfferedInputStream bis = new BufferedInputStream(new FileInputStream(source));
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(des));
        
        byte[] bytes = new byte[1024*1024];
        int len;
        while((bis.read(bytes))!=-1){
            bos.write(bytes,0,len);
        }
        
        long endTime = System.currentTimeMills();
        return endTime-startTime;
    }
    
    //使用NIO复制文件
    private long transferFileWithNIO(File source,File des) throws IOEXception{
        long startTime = System.currentTimeMillis();
        if(!des.exists()){
            des.createNewFile();
        }
        FileInputStream fis = new FileInputStream(source);
        FileOutputStream fos = new FileOutputStream(des);
        
        FileChannel inChannel=fis.getChannel();
        FileChannel outChannel=fos.getChannel();
        // 1M缓冲区
        ByteBuffer byteBuffer=ByteBuffer.allocate(1024*1024);
        while(inChannel.read(byteBuffer)>0){
            // 切换为读模式
            byteBuffer.flip();
            outChannel.write(byteBuffer);
            byteBuffer.clear();
        }
        inChannel.close();
        outChannel.close();
        long endTime = System.currentTimeMillis();
        return endTime - startTime;
    }
    

    public static void main(String[] args) throws IOException {
 
          SimpleFileTransferTest simpleFileTransferTest = new SimpleFileTransferTest();
          File sourse = new File("F:\\电影\\[电影天堂www.dygod.cn]猜火车-cd1.rmvb");
          File des = new File("X:\\Users\\ozc\\Desktop\\io.avi");
          File nio = new File("X:\\Users\\ozc\\Desktop\\nio.avi");
          long time = simpleFileTransferTest.transferFile(sourse, des);
          System.out.println(time + "：普通字节流时间");
          long timeNio = simpleFileTransferTest.transferFileWithNIO(sourse, nio);
          System.out.println(timeNio + "：NIO时间");
          
    }
    
}
```
**我分别测试了文件大小为13M，40M，200M的运行结果：**  
![](https://github.com/panguojian/NIODemo/blob/master/img/img1.png)  
![](https://github.com/panguojian/NIODemo/blob/master/img/img2.png)  
![](https://github.com/panguojian/NIODemo/blob/master/img/img3.png)  
