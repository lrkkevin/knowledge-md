[TOC]



### 一、RPM包命名规则

##### 1、RPM包在光盘中的位置
![image-20200722164305797](https://gitee.com/lrkkevin/oss/raw/master/uPic/2020/07/22/image-20200722164305797.png)

#####  2、RPM包命名原则

* 示例:httpd-2.2.15-15.e16.centos.1.i686.rpm
httpd       			软件包名
2.2.15     			软件版本
15            		   软件发布次数
e16.centos 		适合的Linux平台
i686 				   适合的硬件平台
rpm rpm             包扩展名
![image-20200722165351004](https://gitee.com/lrkkevin/oss/raw/master/uPic/2020/07/22/image-20200722165351004.png)

##### 3、RPM包依赖性
树形依赖：a >>> b >>> c
环形依赖：a >>> b >>> c >>> a

### 二、安装命令

##### 1、包全名与包名
包全名：操作的包是没有安装的软件包时，使用包全名。而且要注意路径。
包名：操作已经安装的软件包时，使用包名，是搜索/var/lib/rpm/中的数据库。

##### 2、RPM安装
rpm  -ivh     包全名
选项：
-i (install)      安装
-v (verbose) 显示详细信息
-h (hash)      显示进度
--nodeps     不检测依赖性（绝不允许使用）
![image-20200722170529795](https://gitee.com/lrkkevin/oss/raw/master/uPic/2020/07/22/image-20200722170529795.png)
**注：安装时要看到第二个100%才说明安装成功**

### 三、升级与卸载

##### 1、升级
**rpm -Uvh** **包全名**
选项：
-U （upgrade） 升级

##### 2、卸载
**rpm -e** **包名**
选项：
-e （erase）卸载
--nodeps 不检测依赖性（实际工作中也不允许使用）

### 四、RPM包查询

##### 1、查询是否安装
rpm  -q  包名
选项：
-q　　查询（query）

##### 2、 查询所有已经安装的RPM包
rpm  -qa 
选项：
-a　　所有（all）

##### 3、 查询软件包详细信息
rpm  -qi  包名
选项：
-i　　查询软件信息（information）
-p　  查询未安装包信息（package）

##### 4、 查询包中文件安装位置
rpm  -ql  包名
选项：
-l　　列表（list）
-p　  查询未安装包信息（package）

##### 5、 常规安装位置
![image-20200722171257803](https://gitee.com/lrkkevin/oss/raw/master/uPic/2020/07/22/image-20200722171257803.png)

##### 6、查询系统文件属于哪个RPM包
rpm  -qf  系统文件名
选项：
-f 　　查询系统文件属于哪个软件包（file）

##### 7、查询软件包的依赖性
rpm  -qR  包名
选项：
-R　　查询软件包的依赖性（requires）
-p　　查询未安装包信息（package）

### 五、RPM包校验
##### 1、RPM包校验
rpm  -V  已安装包的包名
选项：
-V 　　校验指定rpm包中的文件（verify）

验证内容中的8个信息的具体内容如下：
s　　 文件大小是否改变
M　   文件的类型或文件的权限（rwx）是否被改变
5　　文件MD5校验和是否改变（可以看成文件内容是否改变）
D　　设备的主从代码是否改变
L　　文件路径是否改变
U　　文件属性（所有者）是否改变
G　　文件属组是否改变
T　　文件的修改时间是否改变

文件类型：
c　　配置文件（config file）
d　　普通文档（documentation）
g　　“鬼” 文件（ghost file），很少见，就是该文件不应该被这个RPM包包含
L　　授权文件（license file）
r　　描述文件（read me）