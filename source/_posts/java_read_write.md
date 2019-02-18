---
title: Java读写文本文件
categories:
- 编程语言
tags:
- Java
- IO
- 文件读写
toc: true
---
<Excerpt in index | > 
本文讲述Java如何读取文本文件<!-- more -->
<The rest of contents | 余下全文>



下面实现将一个目录下的多个文本文件，读取，然后写入到一个文本文件中.

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;

/**
* @author tensory
* @date 2018年9月29日 下午8:21:21
*/
public class ReadWrite {

	public static void main(String[] args) {
		
		// Input目录下有两个文本文件。可以读取目录的所有文件，也可以只读取一个文件
		String read_path="E:\\JavaWorkspace\\JavaIOTest\\src\\Input";
		//文件result不用事先存在
  		String write_path = "E:\\JavaWorkspace\\JavaIOTest\\src\\Output\\result"; 
		
		File file = new File(read_path);	// 此处路径是一个目录
		// 如果只读取一个文件，则路径就是文件路径+文件名，后面的FileReader(file)就可以
		File[] fileList = file.listFiles();	// 读取目录下的所有文件
												
		BufferedReader reader=null;
		BufferedWriter writer = null;
		String temp=null;
		
		for(int i=0;i<fileList.length;i++){
			
			String file_name = fileList[i].getName();
			System.out.println("fileName:"+file_name);
			try{
				reader = new BufferedReader(new FileReader(fileList[i]));
				writer = new BufferedWriter(new FileWriter(write_path, true));	
				// true表示向文件追加内容
				
				int num = 0;
				while((temp=reader.readLine())!=null){
					writer.write(temp+"\n");	//追加写入文件
				}
			}catch(Exception e){
				System.out.println(e.toString());
			}
			finally{
				if(reader!=null){
					try{
						reader.close();
						writer.close();
					}
					catch(Exception e){
						e.printStackTrace();
					}
				}
			}
		}	
	}
}
```