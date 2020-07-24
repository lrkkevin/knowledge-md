[TOC]

| 修订记录   | 版本 | 是否发布 |
| ---------- | ---- | -------- |
| 2020/07/23 | v1.0 | 是       |

### thread 1  命令

thread 1 命令会打印线程ID 1的栈。
Arthas支持管道，可以用 thread 1 | grep 'main(' 查找到main class。
可以看到main class是demo.MathGame：
```
$ thread 1 | grep 'main('
   at demo.MathGame.main(MathGame.java:17)
```

### Sc 命令
可以通过 sc 命令来查找JVM里已加载的类：
```
sc -d *MathGame
```

### **Jad**  命令
可以通过 jad 命令来反编译代码：
```
jad demo.MathGame
```

### watch命令
通过watch命令可以查看函数的参数/返回值/异常信息。
```
watch demo.MathGame primeFactors returnObj
```
输入 Q 或者 Ctrl+C 退出watch命令。

### Exit/Stop 退出 Arthas
用 exit 或者 quit 命令可以退出Arthas。
退出Arthas之后，还可以再次用 java -jar arthas-boot.jar 来连接。

### 彻底退出Arthas
exit/quit命令只是退出当前session，arthas server还在目标进程中运行。
想完全退出Arthas，可以执行 stop 命令。