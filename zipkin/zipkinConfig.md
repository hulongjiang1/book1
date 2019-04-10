**zipkin server**

zipkin最新版本下载地址：<https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=LATEST&c=exec>

STORAGE_TYPE=mysql MYSQL_HOST=10.22.29.139 MYSQL_TCP_PORT=3306  MYSQL_DB=cloud_marketplace MYSQL_USER=root MYSQL_PASS=1QAZxsw@ nohup java -jar  zipkin-server-2.9.4-exec.jar &

 

**zipkin client**

**1. pom**

<parent>

<groupId>org.springframework.boot</groupId>

<artifactId>spring-boot-starter-parent</artifactId>

<version>2.0.0.RELEASE</version>

</parent>

<dependencyManagement>

<dependencies>

<dependency>

<groupId>org.springframework.cloud</groupId>

<artifactId>spring-cloud-dependencies</artifactId>

<version>Finchley.SR1</version>

<type>pom</type>

<scope>import</scope>

</dependency>

</dependencies>
</dependencyManagement> 

 

<dependency>

<groupId>org.springframework.cloud</groupId>

<artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency> 

 

**2. application.properties配置**

spring.zipkin.base-url=http://10.22.29.139:9411  

spring.sleuth.sampler.percentage=1  

spring.sleuth.web.skipPattern=/api-docs.*|/zipkin/*|/autoconfig|/configprops|/dump|/health|/info|/metrics.*|/mappings|/trace|/swagger.*|.*\\.png|.*\\.css|.*\\.js|.*\\.html|/favicon.ico|/hystrix.stream

spring.sleuth.web.matchPattern=

spring.application.name=market

**3. zipkinConfig**

**package  com.enn.cloud.marketplace.config;**



**import  java.util.regex.Pattern;**



**import  org.apache.commons.lang.StringUtils;**

**import  org.springframework.beans.factory.annotation.Value;**

**import  org.springframework.cloud.sleuth.instrument.web.ServerSampler;**

**import  org.springframework.cloud.sleuth.instrument.web.SkipPatternProvider;**

**import  org.springframework.context.annotation.Bean;**

**import  org.springframework.context.annotation.Configuration;**



**import  brave.http.HttpAdapter;**

**import  brave.http.HttpSampler;**



/**

\*  配?置?url需?要?跳?过?或?仅?仅?匹?配?的?才?能?通?过?，?配?置?在?application.yml中?<br  />

\*  例?如?：?spring.sleuth.web.skipPattern = "/urlpath/*"<br />

\*  spring.sleuth.web.matchPattern：?匹?配?的?url

\*  **@author** hlj

*

*/

@Configuration

**public class ZipkinConfig {**

**private** Pattern pattern;



@Value("${spring.sleuth.web.matchPattern}")

**private** String matchPattern;



@Bean(name = ServerSampler.***NAME***)

HttpSampler  myHttpSampler(SkipPatternProvider provider)  {

pattern = provider.skipPattern();

**return** **new** HttpSampler()  {

@Override

**public** <Req> Boolean  trySample(HttpAdapter<Req, ?> adapter,  Req request) {

//  假?如?设?置?的?matchPatter，?则?skipPattern不?起?作?用?，?只?有?匹?配?matachPattern的?url才?会?被?追?踪?

String url = adapter.path(request);

**boolean** shouldSkip = **false**;

**if** (StringUtils.*isEmpty*(matchPattern)) {

shouldSkip = pattern.matcher(url).matches();

}  **else** {

shouldSkip = !Pattern.*compile*(matchPattern).matcher(url).matches();

}

**if** (shouldSkip) {

**return** **false**;

}

**return** **true**;

}

};

}



**public** **static** **void** main(String[] args) {

Pattern  pattern = Pattern.*compile*("http:/kin/*");

System.***out***.println(pattern.matcher("http:/kin").matches());

}

}  