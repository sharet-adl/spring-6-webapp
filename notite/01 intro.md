MY LEARNING NOTES
-----------------
Intro

License Trial extra 120 zile : https://springframework.guru/udemy-90-day-trial-license-intellij-spring-5/ ( doar pt conturi noi de JetBrain )
Slack follow-up: https://go.springframework.guru/spring-framework-6-udemy-slack-signup
spring-6-udemy channel
https://spring-framework-guru.slack.com/


Spring Framework : is a collection of framework libraries, like: Dependecy Injection, Web, Transaction Management, etc
Spring Boot      : is automated tooling for SFW apps

Spring Framework areas:
- Data Access / Integration : JDBC, ORM, JMS, Transactions
- Web                       : WebSocket, Servlet, Web
- AOP
- Aspects
- Instrumentation
- Messaging
- Testing
- Core Container            : Beans, Core, Context, SpEL

Spring Boot Features:
- curated 'starter' depenndencies for common components
- sensible 'auto-configuration' based on classes found on the classpath. Eg: it will auto-configure an in-memory DB if H2 is present in the classpath
- externalized configuration, via files and environment variables
- logging auto-configuration
- performance metrics
- healthcheck metrics
- enhanced failure information

Spring projects:
- Spring Data          - collection of prj for persisting data to SQL/NoSQL DBs
- Spring Cloud         - tools for distributed systems
- Spring Security      - A&A
- Spring Session       - distributed web application sessions
- Spring Integration   - EIP ( Enterprise Integration Patterns )
- Spring Batch.        - Batch processing
- Spring State Machine - open source SM

----------------------------
Noutati : Spring Framework 6
----------------------------
- links:
    - https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Release-Notes
    - https://www.baeldung.com/spring-boot-3-migration
    - video & presentation SFW 6.1: https://www.youtube.com/watch?v=_o7NIaOVjNM
- extra links:
    - code refactor Java17: https://github.com/openrewrite/rewrite
    - reteta REWRITE: https://docs.openrewrite.org/recipes/java/spring/boot3/upgradespringboot_3_0
    - migrare catre micro-meter: https://github.com/micrometer-metrics/tracing/wiki/Spring-Cloud-Sleuth-3.1-Migration-Guide
    - SCS:
      https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#spring-cloud-stream-preface-notable-deprecations
      https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_producing_and_consuming_messages
    - configuration:
      https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Configuration-Changelog

- Spring Boot 2.7/8 -> 3.1+ [2018/2022]
- Java17+
    - Java EE 8 --> Jakarta EE 9
        - javax.*. --> jakarta.* (ex: Servlet API 5.0, JPA 3.0, Bean validation 3.0, Dependecy injection 2.0 : javax.servlet, javax.persistence, javax.validation, javax.inject )
    - spring.factories --> spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
    - @Configuration -> @AutoConfiguration. ( recomandat )
    - REM EhCache2 -> 3
    - Spring Cloud Sleuth -> Micrometer
      https://docs.spring.io/spring-cloud-sleuth/docs/current-SNAPSHOT/reference/html/
      https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.micrometer-tracing
      https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.micrometer-tracing.tracer-implementations.otel-otlp
    - Jetty -> 11/12, Tomcat -> 10, Undertow 2.3, Hibernate ORM 6.1, Hibernate validator 7/8
    - HazelCast3 -> 5
    - Mockito -> 5
    - REM ActiveMQ
    - REM Jersey temporar
    - SCS 4.0.x+ - scos total modelul de programare bazat pe anotatii, suporta doar programare functionala
- imbunatatiri:
    - Spring AOT ( ahead-of-time - preprocesare de Beans la build-time )
        - reducere timp startup si memorie pt productii, hinturi la runtime pt reflection/resurse/serializare/proxies
        - preconditie pentru GraalVM. Extra setup la build cu mai putina flexibilitate la runtime
    - virtual threads ('project loom')
        - mode de threading 'lightweight' in JVM => alt ordin de magnitude pt scalabilitate pt programare imperativa
        - impl ca si variante virtuale ale java.lang.Thread
    - core:
        - determinare proprietati Bean cu jaba.beans.Introspector
        - scanare calsspath bazata pe NIO
        - scanare path modul, pt prima clasa
        - interfata client-side HTTP bazata pe @HttpExchange, code status HTTP unificat, procesare multi-part WebFlux, JDK HttpClient integrare cu WebClient, ..
- ordine upgrade:
    - upgrade la ultimul patch de SB 2.7.x / SFW 5.3.x
    - helper -> https://github.com/spring-projects-experimental/spring-boot-migrator
    - fixat toate problemele - eg. AutoConfiguration din spring.factories
    - upgradat la SB 3.x / SFW 6.x

