# Java文件上传与下载

使用Apache Commons Net包实现简单的文件上传与下载。

> Apache Commons Net™ library implements the client side of many basic Internet protocols. The purpose of the library is to provide fundamental protocol access, not higher-level abstractions. Therefore, some of the design violates object-oriented design principles. Our philosophy is to make the global functionality of a protocol accessible (e.g., TFTP send file and receive file) when possible, but also provide access to the fundamental protocols where applicable so that the programmer may construct his own custom implementations (e.g, the TFTP packet classes and the TFTP packet send and receive methods are exposed).

引用包：

```
<dependency>
	<groupId>commons-net</groupId>
	<artifactId>commons-net</artifactId>
	<version>3.6</version>
</dependency>
```



接口文档：http://commons.apache.org/proper/commons-net/javadocs/api-3.6/index.html



上传代码样例如下：

FtpUtil.java

```java

/**
 * @author <a href="mailto:zengxiangfei@chinamobile.com">zengxf</a>
 * @since 2020/2/11
 */
@Slf4j
public class FtpUtil {

    public static String host = PropertiesUtil.getProperty("ftpserver.host");
    public static String username = PropertiesUtil.getProperty("ftpserver.username");
    public static String password = PropertiesUtil.getProperty("ftpserver.password");
    public static String imgserverPrefix = PropertiesUtil.getProperty("ftpserver.imgserver.prefix");

    private static FTPClient ftpClient = new FTPClient();

    private static boolean connectServer() {
        boolean flag = false;
        try {
            ftpClient.connect(host);
            flag = ftpClient.login(username, password);
        } catch (IOException e) {
            log.error("Connect Ftp Server failed, host:{}, username:{}, password:{}", host, username, password, e);
        }
        return flag;
    }

    public static String uploadToImgserver(File file) {
        if (uploadFile("imgserver", file)) {
            return imgserverPrefix + file.getName();
        }
        return null;
    }

    public static boolean uploadFile(String remotePath, File file) {

        FileInputStream fis = null;
        boolean uploaded = false;

        boolean isConnect = connectServer();

        if(isConnect) {
            try {
                ftpClient.changeWorkingDirectory(remotePath);
                ftpClient.setBufferSize(1024);
                ftpClient.setControlEncoding("UTF-8");
                ftpClient.setFileType(FTPClient.BINARY_FILE_TYPE);
                ftpClient.enterLocalPassiveMode();

                fis = new FileInputStream(file);
                uploaded = ftpClient.storeFile(file.getName(), fis);

            } catch (IOException e) {
                log.error("Upload file failed!", e);
            } finally {
                try {
                    ftpClient.disconnect();
                    if(null != fis) {
                        fis.close();
                    }
                } catch (IOException e) {
                    log.error("Disconnect ftpserver or close file failed!", e);
                }

            }
        }
        return uploaded;
    }
}

```

PropertiesUtil.java

```java
/**
 * @author <a href="mailto:zengxiangfei@chinamobile.com">zengxf</a>
 * @since 2020/2/11
 */
@Slf4j
public class PropertiesUtil {

    private static Properties props;

    static {
        String fileName = "ftpserver.properties";
        props = new Properties();

        try {
            props.load(new InputStreamReader(PropertiesUtil.class.getClassLoader().getResourceAsStream(fileName), "UTF-8"));
        } catch (IOException e) {
            log.error("Load properties：{} failed： ", fileName, e);
        }
    }

    public static String getProperty(String key){
        String value = props.getProperty(key);
        if( !StringUtils.isBlank(value)){
            return value;
        }
        return null;
    }

    public static String getProperty(String key, String defaultValue){
        String value = props.getProperty(key);
        if( !StringUtils.isBlank(value)){
            return value;
        }
        return defaultValue;
    }
}

```

