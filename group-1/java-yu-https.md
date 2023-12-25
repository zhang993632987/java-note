# java与HTTPS

使用 restTemplate 访问 https 协议的路径时，控制台输出了如下的异常信息：

<details>

<summary>错误日志</summary>

```log
Caused by: javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
	at java.base/sun.security.ssl.Alert.createSSLException(Alert.java:131) ~[na:na]
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:378) ~[na:na]
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:321) ~[na:na]
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:316) ~[na:na]
	at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1357) ~[na:na]
	at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.onConsumeCertificate(CertificateMessage.java:1232) ~[na:na]
	at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.consume(CertificateMessage.java:1175) ~[na:na]
	at java.base/sun.security.ssl.SSLHandshake.consume(SSLHandshake.java:396) ~[na:na]
	at java.base/sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:480) ~[na:na]
	at java.base/sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:458) ~[na:na]
	at java.base/sun.security.ssl.TransportContext.dispatch(TransportContext.java:201) ~[na:na]
	at java.base/sun.security.ssl.SSLTransport.decode(SSLTransport.java:172) ~[na:na]
	at java.base/sun.security.ssl.SSLSocketImpl.decode(SSLSocketImpl.java:1506) ~[na:na]
	at java.base/sun.security.ssl.SSLSocketImpl.readHandshakeRecord(SSLSocketImpl.java:1421) ~[na:na]
	at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:455) ~[na:na]
	at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:426) ~[na:na]
	at java.base/sun.net.www.protocol.https.HttpsClient.afterConnect(HttpsClient.java:589) ~[na:na]
	at java.base/sun.net.www.protocol.https.AbstractDelegateHttpsURLConnection.connect(AbstractDelegateHttpsURLConnection.java:187) ~[na:na]
	at java.base/sun.net.www.protocol.https.HttpsURLConnectionImpl.connect(HttpsURLConnectionImpl.java:142) ~[na:na]
	at org.springframework.http.client.SimpleBufferingClientHttpRequest.executeInternal(SimpleBufferingClientHttpRequest.java:75) ~[spring-web-6.0.15.jar:6.0.15]
	at org.springframework.http.client.AbstractBufferingClientHttpRequest.executeInternal(AbstractBufferingClientHttpRequest.java:48) ~[spring-web-6.0.15.jar:6.0.15]
	at org.springframework.http.client.AbstractClientHttpRequest.execute(AbstractClientHttpRequest.java:66) ~[spring-web-6.0.15.jar:6.0.15]
	at org.springframework.web.client.RestTemplate.doExecute(RestTemplate.java:862) ~[spring-web-6.0.15.jar:6.0.15]
	... 118 common frames omitted
Caused by: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
	at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:439) ~[na:na]
	at java.base/sun.security.validator.PKIXValidator.engineValidate(PKIXValidator.java:306) ~[na:na]
	at java.base/sun.security.validator.Validator.validate(Validator.java:264) ~[na:na]
	at java.base/sun.security.ssl.X509TrustManagerImpl.checkTrusted(X509TrustManagerImpl.java:231) ~[na:na]
	at java.base/sun.security.ssl.X509TrustManagerImpl.checkServerTrusted(X509TrustManagerImpl.java:132) ~[na:na]
	at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1341) ~[na:na]
	... 136 common frames omitted
Caused by: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
	at java.base/sun.security.provider.certpath.SunCertPathBuilder.build(SunCertPathBuilder.java:148) ~[na:na]
	at java.base/sun.security.provider.certpath.SunCertPathBuilder.engineBuild(SunCertPathBuilder.java:129) ~[na:na]
	at java.base/java.security.cert.CertPathBuilder.build(CertPathBuilder.java:297) ~[na:na]
	at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:434) ~[na:na]
	... 141 common frames omitted

Disconnected from the target VM, address: '127.0.0.1:60627', transport: 'socket'

Process finished with exit code 0

```

</details>

上述异常是由于SSL证书验证问题引起的。<mark style="color:blue;">**当使用HTTPS时，Java需要验证服务器的SSL证书以确保安全连接。**</mark>**需要在java中手动配置 ssl 证书：**

1. 首先，需要下载服务器的SSL证书，通过浏览器手动下载即可。
2.  然后，将下载下来的SSL证书导入到Java信任库，可以使用 **keytool** 命令来导入证书：

    ```properties
    keytool -import -alias keycloak -file server.pem -keystore cacerts
    ```

    将**server.crt**替换为服务器证书的实际文件名。默认情况下，**cacerts**位于Java安装目录的**lib/security目录中。**

{% hint style="warning" %}
## <mark style="color:orange;">注意</mark>

* <mark style="color:orange;">**如果 java安装路径位于C盘，在执行 keytool 时需要超级用户权限，否则会报错**</mark>
* <mark style="color:orange;">**执行上述的 keytool 命令时，需要输入一个密码，这个密码默认为：changeit**</mark>
{% endhint %}
