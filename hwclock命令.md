# hwclock命令



Linux系统里有两个时钟，一个是硬件时钟，一个则是系统时钟。硬件时钟是通过主板的CMOS控制，而系统时钟则通过CPU来控制。

在Linux中我们可以通过使用hwclock命令来查询和设置时钟。

格式：
```bash
hwclock [function] [option...]


参数:
	clock/hwclock 显示硬件时间
	
	-s (--hctosys）以硬件时间为准，校正系统时间
	
	-w (--systohc) 以系统时间为准，校正硬件时间
	
	-r (--show) 读取并打印硬件时钟
```

