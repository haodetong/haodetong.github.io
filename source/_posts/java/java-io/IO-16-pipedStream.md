---
title: IO流 - 十六、管道流 - PipedStream
date: 2018-01-10 14:47:12
tags: IO
category: java
keywords: java, IO
description: java-IO流
---

PipedInputStream和PipedOutputStream

输入输出可以直接进行连接，通过结合线程使用。

	public class PipedStreamDemo1 {
	
		public static void main(String[] args) throws IOException {
			PipedInputStream input = new PipedInputStream();
			PipedOutputStream out = new PipedOutputStream();
			input.connect(out);
			
			new Thread(new Input(input)).start();
			new Thread(new Output(out)).start();
			
		}
		
	}
	
	class Input implements Runnable {
		private PipedInputStream in;
		Input(PipedInputStream in) {
			this.in = in;
		}
		
		public void run() {
			try {
				byte[] buf = new byte[1024];
				int length = in.read(buf);
				String s = new String(buf,0,length);
				System.out.println("s="+s);
				in.close();
			} catch (Exception e) {
				// TODO: handle exception
			}
		}
	}
	
	class Output implements Runnable {
		private PipedOutputStream out;
		Output(PipedOutputStream out) {
			this.out = out;
		}
	
		public void run() {
			try {
				out.write("aaaaaaaaa".getBytes());
			} catch (Exception e) {
				// TODO: handle exception
			}
		}
	}
