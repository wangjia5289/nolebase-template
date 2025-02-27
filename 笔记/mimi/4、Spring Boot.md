



站点不安全，解决方案：


![[Pasted image 20241029213654.png]]

![[Pasted image 20241029213703.png]]


```
server.port=8443
server.ssl.key-store=classpath:keystore.jks
server.ssl.key-store-password=yourpassword
server.ssl.key-store-type=JKS
server.ssl.key-alias=youralias

```


![[Pasted image 20241029213724.png]]

```
keytool -genkeypair -alias youralias -keyalg RSA -keystore keystore.jks -keysize 2048
```
![[Pasted image 20241029213748.png]]
![[Pasted image 20241029213737.png]]
![[Pasted image 20241029214010.png]]
![[Pasted image 20241029214128.png]]
密钥库口令：wq666666

密钥对别称：Production_certificate

![[Pasted image 20241029214154.png]]

![[Pasted image 20241029214253.png]]

![[Pasted image 20241029214308.png]]
```
keytool -genkeypair -alias wj -keyalg RSA -keystore keystore.jks -keysize 2048

```
![[Pasted image 20241029214314.png]]

