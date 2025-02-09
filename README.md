# Spring-Core-RCE Spring Framework 远程命令执行漏洞（CVE-2022-22965）
Spring-Core-RCE堪比关于 Apache Log4j2核弹级别漏洞exp的rce一键利用
## 概述
近日，Spring 官方 GitHub issue中提到了关于 Spring Core 的远程命令执行漏洞，该漏洞广泛存在于Spring 框架以及衍生的框架中。
![image](https://user-images.githubusercontent.com/53851034/161377466-d1e363d5-f2f0-4498-84af-7ebf6fb58d8b.png)

## 漏洞描述

Spring core是Spring系列产品中用来负责发现、创建并处理bean之间的关系的一个工具包，是一个包含Spring框架基本的核心工具包，Spring其他组件都要使用到这个包。

---

未经身份验证的攻击者可以使用此漏洞进行远程任意代码执行。 该漏洞广泛存在于Spring 框架以及衍生的框架中，JDK 9.0及以上版本会受到影响。使用旧JDK版本的产品不受影响。建议存在该漏洞的企业在防火墙处阻止带有特殊字符串的请求，以免受到该漏洞的攻击。

## 漏洞复现

![image](https://user-images.githubusercontent.com/53851034/161377224-5650ec2d-62f6-4aee-9fc9-636c5a4d652c.png)
## poc一键利用
### 搭建漏洞环境
#### 0x01、使用docker下载docker pull vulfocus/spring-core-rce-2022-03-29
![image](https://user-images.githubusercontent.com/53851034/161377854-cfc06073-8012-4721-bdf9-9a8295ec449a.png)
#### 0x02、运行靶机容器
docker run -d -p 8080:8080 vulfocus/spring-core-rce-2022-03-29:latest
![image](https://user-images.githubusercontent.com/53851034/161377862-6fb71921-9723-4673-8c43-41f0f7694a0c.png)
#### 0x03、访问http://127.0.0.1:8080/ 出现ok即表示服务正常
![image](https://user-images.githubusercontent.com/53851034/161377867-04e86223-6e08-4f7f-b671-9a7dc82d5157.png)
#### 0x04、使用exp进行攻击
![image](https://user-images.githubusercontent.com/53851034/161377876-5340b9ba-0fbc-4335-a52c-e98c97b2ca73.png)
#### 0x05、获取到最高权限:
访问http://127.0.0.1:8080/tomcatwar.jsp?pwd=j&cmd=whoami
![image](https://user-images.githubusercontent.com/53851034/161377897-21954838-9a20-49d7-a579-b31347ac0532.png)

## 影响范围

Spring 框架以及衍生的框架会受到影响。（JDK 版本需在9.0及以上。）

根据目前FOFA系统最新数据（一年内数据），显示全球范围内（上述FOFA查询语句）共有 7,023,506 个相关服务对外开放。中国使用数量最多，共有 2,987,121 个；美国第二，共有 1,215,955 个；巴西第三，共有 381,319 个；韩国第四，共有 219,201 个；德国第五，共有 213,097 个。

全球范围内分布情况如下（仅为分布情况，非漏洞影响情况）

## 修复建议

升级Spring Framework 版本

```
Spring Framework == 5.3.18 

Spring Framework == 5.2.20
```
## 临时防御方案：

### 1、 WAF防御。

可以在WAF中添加以下规则对特殊输入的字符串进行过滤：

```
Class.*

class.*

*.class.*

*Class.*
```
### 2、通过黑名单策略进行防护。

您可在受影响产品代码中搜索@InitBinder注解，判断方法体内是否有dataBinder.serDisallowerFields方法，若发现存在该方法，则在黑名单中添加如下过滤规则：

```
Class.*

class.*

*.class.*

*Class.*
```
## 参考

[1] https://github.com/spring-projects/spring-framework/issues/27483

[2] https://github.com/google/tsunami-security-scanner-plugins/issues/234

