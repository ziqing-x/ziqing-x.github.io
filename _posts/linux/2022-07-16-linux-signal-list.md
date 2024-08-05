---
title: linux 信号列表
tags: [signal]
category: linux
image:
  path: /assets/img/headers/signal.webp
---

开发中经常会用到 linux 信号，但是总忘记信号的编号，所浅浅的记录一下部分常用的信号。

```bash

1. HUP      # 终端挂起或控制进程终止 
2. INT      # 外部发出的中断，通常由Ctrl+C触发 
3. QUIT     # 外部发出的退出，通常由Ctrl+\触发 
4. ILL      # 非法指令 
5. TRAP     # 追踪/断点 
6. IOT      # I/O trap指令 
7. BUS      # 总线错误 
8. FPE      # 浮点异常 
9. KILL     # 强制终止 
10. USR1    # 用户自定义信号1 
11. SEGV    # 无效内存引用 
12. USR2    # 用户自定义信号2 
13. PIPE    # 管道破裂 
14. ALRM    # 定时器超时 
15. TERM    # 终止请求 
16. STKFLT  # 协处理器栈错误 
17. CHLD    # 子进程状态改变 
18. CONT    # 继续执行暂停的进程 
19. STOP    # 暂停进程 
20. TSTP    # 终端停止（Ctrl+Z） 
21. TTIN    # 后台进程尝试读取 
22. TTOU    # 后台进程尝试写入 
23. URG     # 紧急条件 
24. XCPU    # 超时软限制 
25. XFSZ    # 文件大小限制超过 
26. VTALRM  # 虚拟定时器超时 
27. PROF    # 专用定时器超时 
28. WINCH   # 窗口大小调整 
29. POLL    # 可轮询文件描述符 
30. PWR     # 电源故障 
31. SYS     # 非法系统调用 
```