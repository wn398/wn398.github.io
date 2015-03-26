title: Java多线程下载实例
date: 2015/3/24 14:58:41 
updated: 2015/3/24 14:58:55 
tags: 
- 技术
- 并发
- 实例
categories: JavaSE
---
#Java多线程下载实例
>多线程下载一个文件，主要有几个难点：
1. 要知道下载文件的大小，这可以通过网络连接获取
2. 每个线程需要下载多少，可以根据线程数计算
3. 如何请求下载文件的特定部分， 利用网络请求参数 range
4. 如何写入到数据到文件特定部分  利用RandomAccessFile类

下面是一个简单的例子
```{bash}
import java.io.File;
import java.io.InputStream;
import java.io.RandomAccessFile;
import java.net.HttpURLConnection;
import java.net.URL;

public class TestMulDownloadThread {
	public static void main(String[] args) throws Exception{
		URL url = new URL("http://dldir1.qq.com/qqtv/TencentVideo9.6.695.0.exe");
		downloadWithMulThread(url, 3);
	}
	
	public static void downloadWithMulThread(final URL url,int threadNum)throws Exception{
	    final HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
	    if(urlConnection.getResponseCode()==200) {
	    	//获取要下载文件的大小
	        final int length = urlConnection.getContentLength();
	        //获取要下载的文件名
	        String fileName = url.getPath().substring(url.getPath().lastIndexOf("/") + 1);
	        final File file = new File(fileName);
	        //RandomAccessFile randomAccessFile = new RandomAccessFile(file,"rwd");
	        //randomAccessFile.setLength(length);
	        //randomAccessFile.close();
	        urlConnection.disconnect();
	        //计算每个线程下载量大小，如果能整除则直接用结果可以使用整除结果，如果不能则使用除的结果加1，这样保证能下载到全部数据
	        final int blockLength = length % threadNum == 0 ? length / threadNum : length / threadNum + 1;

	        for (int i = 0; i < threadNum; i++) {
	        	//blockNum 是按线程分成的块次序，也可以理解为线程号
	            final int blockNum = i;
	            new Thread(new Runnable() {
	                @Override
	                public void run() {
	                    try {
	                    	//建立一个RandromAccessFile对象，可以从文件的任何位置读起
	                        RandomAccessFile randomAccessFile = new RandomAccessFile(file, "rwd");
	                        //写入文件开始位置   
	                        int start = blockNum * blockLength;
	                        //写入文件的结束位置
	                        int end = start + blockLength-1;
	                        //不能整除的最后一个块处理的结束位置
	                        if (start + blockLength < length) {
	                            end = length - 1;
	                        }
	                        //设置文件指针到开始位置
	                        randomAccessFile.seek(start);
	                        HttpURLConnection urlConnection2 = (HttpURLConnection) url.openConnection();
	                        urlConnection2.setRequestMethod("GET");
	                        //按范围请求相对应的文件流
	                        urlConnection2.setRequestProperty("range", "bytes=" + start + "-" + end);
	                        InputStream in = urlConnection2.getInputStream();
	                        byte[] buffer = new byte[102400];
	                        int len = 0;
	                        while ((len = in.read(buffer)) != -1) {
	                            randomAccessFile.write(buffer, 0, len);
	                        }
	                        in.close();
	                        randomAccessFile.close();
	                        urlConnection2.disconnect();
	                    } catch (Exception e) {
	                        e.printStackTrace();
	                    }
	                }

	            }).start();
	        }
	    }
	}

}
```