---
title: IO流 - 四、转换流
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流

---

字节流通向字符流的转换流:字符流Reader的子类InputStreamReader 

	public class Demo16 {
		public static void main(String[] args) throws IOException {
			//字节流
			InputStream in = System.in;
			
			//字节流转换成字符流，转换流
			InputStreamReader isr = new InputStreamReader(in);
			
			//高效字符流
			BufferedReader bufr = new BufferedReader(isr);
			
			String line = null;
			while((line = bufr.readLine()) != null) {
				if(line.equals("over"))
					break;
				System.out.println(line.toUpperCase());
			}
		}
	}

字符通向字节流的转换流:字符流Writer的子类outputStreamWriter

public class Demo17 {

	public static void main(String[] args) throws IOException {
		
		BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
		
		BufferedWriter bufw = new BufferedWriter(new OutputStreamWriter(System.out));
		
		String line = null;
		while((line=bufr.readLine()) != null) {
			if("over".equals(line))
				break;
			bufw.write(line.toUpperCase());
			bufw.newLine();
			bufw.flush();
		}
	}

}
