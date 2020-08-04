[TOC]
| 修订记录   | 版本 | 是否发布 |
| ---------- | ---- | -------- |
| 2020/08/03 | v1.0 | 是       |

#### 一、SFTP工具类

```java

import com.*.*.common.util.FileUtil;
import com.*.*.common.util.StringUtil;
import com.google.common.collect.Lists;
import com.jcraft.jsch.Channel;
import com.jcraft.jsch.ChannelSftp;
import com.jcraft.jsch.JSch;
import com.jcraft.jsch.JSchException;
import com.jcraft.jsch.Session;
import com.jcraft.jsch.SftpATTRS;
import com.jcraft.jsch.SftpException;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.util.List;
import java.util.Properties;
import java.util.Vector;
import org.apache.commons.lang3.StringUtils;

/**
 * @author (kevin).
 * @Title superlop-parent.
 * @Description SftpTools.
 * @date 2020/8/3.
 */
public class SftpTools {

  private Session session;
  private Channel channel;
  /** ChannelSftp对象提供的所有底层方法. */
  private ChannelSftp chnSftp;
  /** 文件类型. */
  private static final int FILE_TYPE = 1;
  /** 目录类型. */
  private static final int DIR_TYPE = 2;
  /** 连接配置. **/
  private final SftpBean sftpBean;

  /**
   * 构造方法.
   *
   * @param sftpBean 连接配置.
   */
  public SftpTools(SftpBean sftpBean) {
    this.sftpBean = sftpBean;
  }

  public static void main(String[] args) throws IOException, SftpException, JSchException {

    SftpBean sftpBean = new SftpBean();
    sftpBean.setUserName("sftp");
    sftpBean.setSecretKey("superlop123456");
    sftpBean.setHost("10.60.69.184");
    sftpBean.setPort(22);
    sftpBean.setSftpPath("/inbox/emft");
    sftpBean.setSftpBackupPath("/inbox/backup");
    String fileName = "SOLBI_00MPF_20200713_20200717092327_MPF.xlsx";

    SftpTools sftpTools = new SftpTools(sftpBean);
    sftpTools.open();
    sftpTools.moveFile(sftpBean.getSftpPath() + File.separator + fileName, sftpBean.getSftpBackupPath(), "fail");
    sftpTools.close();
  }


  /**
   * 打开连接.
   *
   * @throws JSchException 异常.
   */
  public void open() throws JSchException {
    this.connect();
  }

  /**
   * 连接SFTP.
   *
   * @throws JSchException 异常.
   */
  private void connect() throws JSchException {
    if (StringUtils.isBlank(sftpBean.getPrivateKey()) && StringUtils.isBlank(sftpBean.getSecretKey())) {
      String tempPrivateKeyPath = System.getProperty("user.home") + File.separator + ".ssh" + File.separator + "id_rsa";
      if (FileUtil.isExisted(tempPrivateKeyPath)) {
        sftpBean.setPrivateKey(tempPrivateKeyPath);
      }
    }

    JSch jsch = new JSch();
    if (StringUtils.isNotBlank(sftpBean.getPrivateKey())) {
      jsch.addIdentity(sftpBean.getPrivateKey());
    }
    session = jsch.getSession(sftpBean.getUserName(), sftpBean.getHost(), sftpBean.getPort());
    session.setPassword(sftpBean.getSecretKey());
    Properties sshConfig = new Properties();
    sshConfig.put("StrictHostKeyChecking", "no");
    session.setConfig(sshConfig);
    session.connect(20000);
    channel = session.openChannel("sftp");
    channel.connect();
    chnSftp = (ChannelSftp) channel;
  }


  /**
   * 进入指定的目录并设置为当前目录。
   *
   * @param sftpPath 指定的目录.
   * @throws SftpException 异常.
   */
  private void cd(String sftpPath) throws SftpException {
    chnSftp.cd(sftpPath);
  }

  /**
   * 得到当前用户当前工作目录地址.
   *
   * @return 返回当前工作目录地址.
   * @throws SftpException 异常.
   */
  private String pwd() throws SftpException {
    return chnSftp.pwd();
  }


  /**
   * 根据目录地址,文件类型返回文件或目录列表.
   *
   * @param directory 如:/home/inber/bbb/202006/08/
   * @param fileType 如：FILE_TYPE或者DIR_TYPE
   * @return 文件或者目录列表 List.
   * @throws SftpException 异常.
   */
  public List<String> listFiles(String directory, int fileType) throws SftpException {
    List<String> fileList = Lists.newArrayList();
    if (isDirExist(directory)) {
      boolean itExist;
      Vector<?> vector;
      vector = chnSftp.ls(directory);
      for (Object obj : vector) {
        String str = obj.toString().trim();
        int tag = str.lastIndexOf(":") + 3;
        String strName = str.substring(tag).trim();
        itExist = isDirExist(directory + File.separator + strName);
        if (fileType == FILE_TYPE) {
          if (!(itExist)) {
            fileList.add(directory + File.separator + strName);
          }
        }
        if (fileType == DIR_TYPE) {
          if (itExist) {
            //目录列表中去掉目录名为.和..
            if (!(strName.equals(".") || strName.equals(".."))) {
              fileList.add(directory + File.separator + strName);
            }
          }
        }

      }
    }
    return fileList;
  }

  /**
   * 判断目录是否存在.
   *
   * @param directory 目录.
   * @return 异常.
   */
  private boolean isDirExist(String directory) {
    boolean isDirExistFlag = false;
    try {
      SftpATTRS sftpATTRS = chnSftp.lstat(directory);
      isDirExistFlag = true;
      return sftpATTRS.isDir();
    } catch (Exception e) {
      if (e.getMessage().toLowerCase().equals("no such file")) {
        isDirExistFlag = false;
      }
    }
    return isDirExistFlag;
  }

  /**
   * 下载文件后返回流文件
   *
   * @author inber
   * @since 2012.02.09
   */
  public InputStream getFile(String sftpFilePath) throws SftpException {
    if (isFileExist(sftpFilePath)) {
      return chnSftp.get(sftpFilePath);
    }
    return null;
  }

  /**
   * 获取远程文件流.
   *
   * @param sftpFilePath 远程路径.
   * @return InputStream
   * @throws SftpException 异常.
   */
  public InputStream getInputStreamFile(String sftpFilePath) throws SftpException {
    return getFile(sftpFilePath);
  }

  /**
   * 获取远程文件字节流。
   *
   * @param sftpFilePath 远程路径.
   * @return ByteArrayInputStream
   * @throws SftpException 异常.
   * @throws IOException 异常.
   */
  public ByteArrayInputStream getByteArrayInputStreamFile(String sftpFilePath) throws SftpException, IOException {
    if (isFileExist(sftpFilePath)) {
      byte[] srcFtpFileByte = inputStreamToByte(getFile(sftpFilePath));
      ByteArrayInputStream srcFtpFileStreams = new ByteArrayInputStream(srcFtpFileByte);
      return srcFtpFileStreams;
    }
    return null;
  }


  /**
   * 删除远程
   * 说明:返回信息定义以:分隔第一个为代码，第二个为返回信息
   *
   * @param sftpFilePath 远程路径.
   * @return String
   * @throws SftpException 异常.
   */
  public String delFile(String sftpFilePath) throws SftpException {
    String retInfo;
    if (isFileExist(sftpFilePath)) {
      chnSftp.rm(sftpFilePath);
      retInfo = "1:File deleted.";
    } else {
      retInfo = "2:Delete file error,file not exist.";
    }
    return retInfo;
  }


  /**
   * 移动远程文件到目标目录.
   *
   * @param srcSftpFilePath 远程路径.
   * @param distSftpFilePath 目标路径.
   * @param fileNameTag 文件标示.
   * @throws SftpException 异常.
   * @throws IOException 异常.
   */
  public void moveFile(String srcSftpFilePath, String distSftpFilePath, String fileNameTag)
      throws SftpException, IOException {
    boolean dirExist;
    boolean fileExist;
    fileExist = isFileExist(srcSftpFilePath);
    dirExist = isDirExist(distSftpFilePath);
    if (!fileExist) {
      //文件不存在直接反回.
      return;
    }
    if (!(dirExist)) {
      //1建立目录
      createDir(distSftpFilePath);
    }

    String fileName = srcSftpFilePath.substring(srcSftpFilePath.lastIndexOf(File.separator));
    ByteArrayInputStream srcFtpFileStreams = getByteArrayInputStreamFile(srcSftpFilePath);
    if (StringUtil.isNotBlank(fileNameTag)) {
      fileName = String.format("%s.%s", fileName, fileNameTag);
    }
    //二进制流写文件
    this.chnSftp.put(srcFtpFileStreams, distSftpFilePath + fileName);
    this.chnSftp.rm(srcSftpFilePath);
  }

  /**
   * 复制远程文件到目标目录.
   *
   * @param srcSftpFilePath 远程路径.
   * @param distSftpFilePath 目标路径.
   * @throws SftpException 异常.
   * @throws IOException 异常.
   */
  public void copyFile(String srcSftpFilePath, String distSftpFilePath) throws SftpException, IOException {
    boolean dirExist;
    boolean fileExist;
    fileExist = isFileExist(srcSftpFilePath);
    dirExist = isDirExist(distSftpFilePath);
    //文件不存在直接反回.
    if (!fileExist) {
      return;
    }
    if (!(dirExist)) {
      //1建立目录
      createDir(distSftpFilePath);
    }

    String fileName = srcSftpFilePath
        .substring(srcSftpFilePath.lastIndexOf(File.separator));
    ByteArrayInputStream srcFtpFileStreams = getByteArrayInputStreamFile(srcSftpFilePath);
    //二进制流写文件
    this.chnSftp.put(srcFtpFileStreams, distSftpFilePath + fileName);
  }

  /**
   * 创建远程目录。
   *
   * @param sftpDirPath 远程路径.
   * @throws SftpException 异常.
   */
  public void createDir(String sftpDirPath) throws SftpException {
    this.cd(File.separator);
    if (this.isDirExist(sftpDirPath)) {
      return;
    }
    String[] pathStr = sftpDirPath.split(File.separator);
    for (String path : pathStr) {
      if (path.equals("")) {
        continue;
      }
      if (isDirExist(path)) {
        this.cd(path);
      } else {
        //建立目录
        this.chnSftp.mkdir(path);
        //进入并设置为当前目录
        this.chnSftp.cd(path);
      }
    }
    this.cd(File.separator);
  }

  /**
   * 判断远程文件是否存在.
   *
   * @param srcSftpFilePath 远程路径.
   * @return isExitFlag.
   */
  public boolean isFileExist(String srcSftpFilePath) {
    boolean isExitFlag = false;
    // 文件大于等于0则存在文件
    if (getFileSize(srcSftpFilePath) >= 0) {
      isExitFlag = true;
    }
    return isExitFlag;
  }

  /**
   * 得到远程文件大小
   *
   * @param srcSftpFilePath 文件路径.
   * @return 返回文件大小，如返回-2 文件不存在，-1文件读取异常
   */
  public long getFileSize(String srcSftpFilePath) {
    long fileSize;//文件大于等于0则存在
    try {
      SftpATTRS sftpATTRS = chnSftp.lstat(srcSftpFilePath);
      fileSize = sftpATTRS.getSize();
    } catch (Exception e) {
      fileSize = -1;//获取文件大小异常
      if (e.getMessage().toLowerCase().equals("no such file")) {
        fileSize = -2;//文件不存在
      }
    }
    return fileSize;
  }

  /**
   * 关闭资源
   */
  public void close() {
    if (channel.isConnected()) {
      channel.disconnect();
    }
    if (session.isConnected()) {
      session.disconnect();
    }
  }

  /**
   * inputStream类型转换为byte类型.
   *
   * @param inputStream inputStream
   * @return byte
   * @throws IOException 异常.
   */
  public byte[] inputStreamToByte(InputStream inputStream) throws IOException {
    ByteArrayOutputStream bytestream = new ByteArrayOutputStream();
    int ch;
    while ((ch = inputStream.read()) != -1) {
      bytestream.write(ch);
    }
    byte[] imgData = bytestream.toByteArray();
    bytestream.close();
    return imgData;
  }
}
```



#### 二、配置文件Bean

```java

import com.*.toolset.BcToolSet;

import java.util.Objects;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

/**
 * a bean which contains some tips,such as host,port,userName,password,privateKey
 *
 * @author CangChen
 */
public class SftpBean {

  /**
   * 用户名。
   */
  @NotBlank(message = "userNamecan not be null")
  private String userName;
  /**
   * 加密key。
   */
  private String secretKey;
  /**
   * ip.
   */
  @NotBlank(message = "host not be null")
  private String host;
  /**
   * 端口.
   */
  @NotNull(message = "port not be null")
  private int port;

  /**
   * ssh.
   */
  private String privateKey;
  /**
   * sftp路径.
   */
  @NotBlank(message = "sftpPath not be null")
  private String sftpPath;

  /**
   * 备份路径.
   */
  private String sftpBackupPath;

  public SftpBean() {

  }

  /**
   * 构造方法.
   * @param userName
   * @param secretKey
   * @param host
   * @param port
   * @param privateKey
   */
  public SftpBean(String userName, String secretKey, String host, int port, String privateKey) {
    super();
    this.userName = userName;
    this.secretKey = secretKey;
    this.host = host;
    this.port = port;
    this.privateKey = privateKey;
  }

  public String getUserName() {
    return userName;
  }

  public void setUserName(String userName) {
    this.userName = userName;
  }

  public String getSecretKey() {
    return secretKey;
  }

  public void setSecretKey(String password) {
    this.secretKey = password;
  }

  public String getHost() {
    return host;
  }

  public void setHost(String host) {
    this.host = host;
  }

  public int getPort() {
    return port;
  }

  public void setPort(int port) {
    this.port = port;
  }

  public String getPrivateKey() {
    return privateKey;
  }

  public void setPrivateKey(String privateKey) {
    this.privateKey = privateKey;
  }

  public String getSftpPath() {
    return sftpPath;
  }

  public void setSftpPath(String sftpPath) {
    this.sftpPath = sftpPath;
  }

  @Override
  public boolean equals(Object o) {
    if (this == o) {
      return true;
    }
    if (o == null || BcToolSet.getClass(this) != BcToolSet.getClass(o)) {
      return false;
    }
    SftpBean sftpBean = (SftpBean) o;
    return port == sftpBean.port &&
        userName.equals(sftpBean.userName) &&
        secretKey.equals(sftpBean.secretKey) &&
        host.equals(sftpBean.host) &&
        privateKey.equals(sftpBean.privateKey) &&
        sftpPath.equals(sftpBean.sftpPath);
  }

  @Override
  public int hashCode() {
    return Objects.hash(userName, secretKey, host, port, privateKey, sftpPath);
  }

  /**
   * 获取
   *
   * @return sftpBackPath
   */
  public String getSftpBackupPath() {
    return this.sftpBackupPath;
  }

  /**
   * 设置
   *
   * @param sftpBackupPath
   */
  public void setSftpBackupPath(String sftpBackupPath) {
    this.sftpBackupPath = sftpBackupPath;
  }
}
```

