# Patient Management System

[Build & Deploy a Production-Ready Patient Management System with Microservices: Java Spring Boot AWS](https://www.youtube.com/watch?v=tseqdcFfTUY)

## Technologies involved

- Java 21
- Spring Boot 3.4.x
- Maven
- H2
- PostgreSQL
- Docker
- REST
- gRPC
- Kafka
- AWS

## Patient Service DB
---
Set up run configurations with the following details for Docker Image type in IntelliJ

- Image ID or name: postgres:latest
- Container name: patient-service-db
- Bind Ports: 5000:5432
- Bind mounts: C:/christy/workspace/db_volumes/patient-service-db:/var/lib/postgresql
- Run options: --network internal
- Environment variables: POSTGRES_DB=db;POSTGRES_PASSWORD=password;POSTGRES_USER=admin_user


##  Patient Service
---

Set up run configurations with the following details for Dockerfile type in IntelliJ

- Dockerfile: patient-service\Dockerfile
- Image tage: patient-service:latest
- Container name: patient-service
- Bind ports: 4000:4000
- Run options: --network internal
- Environment Variables (complete list)

```
BILLING_SERVICE_ADDRESS=billing-service;
BILLING_SERVICE_GRPC_PORT=9005;
JAVA_TOOL_OPTIONS=-agentlib:jdwp\=transport\=dt_socket,server\=y,suspend\=n,address\=*:5005;
SPRING_DATASOURCE_PASSWORD=password;
SPRING_DATASOURCE_URL=jdbc:postgresql://patient-service-db:5432/db;
SPRING_DATASOURCE_USERNAME=admin_user;
SPRING_JPA_HIBERNATE_DDL_AUTO=update;
SPRING_KAFKA_BOOTSTRAP_SERVERS=kafka:9092;
SPRING_SQL_INIT_MODE=always
```

*Small caveat: Once API Gatway is up we will remove the bind ports*

### gRPC Setup
---
Add the following to the <dependencies> section

```xml
<!--GRPC -->
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-netty-shaded</artifactId>
    <version>1.69.0</version>
</dependency>
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-protobuf</artifactId>
    <version>1.69.0</version>
</dependency>
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-stub</artifactId>
    <version>1.69.0</version>
</dependency>
<dependency> <!-- necessary for Java 9+ -->
    <groupId>org.apache.tomcat</groupId>
    <artifactId>annotations-api</artifactId>
    <version>6.0.53</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>net.devh</groupId>
    <artifactId>grpc-spring-boot-starter</artifactId>
    <version>3.1.0.RELEASE</version>
</dependency>
<dependency>
    <groupId>com.google.protobuf</groupId>
    <artifactId>protobuf-java</artifactId>
    <version>4.29.1</version>
</dependency>
```

Replace the <build> section with the following

```xml

<build>
    <extensions>
        <!-- Ensure OS compatibility for protoc -->
        <extension>
            <groupId>kr.motd.maven</groupId>
            <artifactId>os-maven-plugin</artifactId>
            <version>1.7.0</version>
        </extension>
    </extensions>
    <plugins>
        <!-- Spring boot / maven  -->
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>

        <!-- PROTO -->
        <plugin>
            <groupId>org.xolstice.maven.plugins</groupId>
            <artifactId>protobuf-maven-plugin</artifactId>
            <version>0.6.1</version>
            <configuration>
                <protocArtifact>com.google.protobuf:protoc:3.25.5:exe:${os.detected.classifier}</protocArtifact>
                <pluginId>grpc-java</pluginId>
                <pluginArtifact>io.grpc:protoc-gen-grpc-java:1.68.1:exe:${os.detected.classifier}</pluginArtifact>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>compile</goal>
                        <goal>compile-custom</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>

```

## Billing Service
---

Set up run configurations with the following details for Dockerfile type in IntelliJ

- Dockerfile: billing-service\Dockerfile
- Image tage: billing-service:latest
- Container name: billing-service
- Bind ports: 4001:4001 9001:9001
- Run options: --network internal

### gRPC Setup
---
Add the following to the <dependencies> section

```xml
<!--GRPC -->
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-netty-shaded</artifactId>
    <version>1.69.0</version>
</dependency>
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-protobuf</artifactId>
    <version>1.69.0</version>
</dependency>
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-stub</artifactId>
    <version>1.69.0</version>
</dependency>
<dependency> <!-- necessary for Java 9+ -->
    <groupId>org.apache.tomcat</groupId>
    <artifactId>annotations-api</artifactId>
    <version>6.0.53</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>net.devh</groupId>
    <artifactId>grpc-spring-boot-starter</artifactId>
    <version>3.1.0.RELEASE</version>
</dependency>
<dependency>
    <groupId>com.google.protobuf</groupId>
    <artifactId>protobuf-java</artifactId>
    <version>4.29.1</version>
</dependency>
```

Replace the <build> section with the following

```xml

<build>
    <extensions>
        <!-- Ensure OS compatibility for protoc -->
        <extension>
            <groupId>kr.motd.maven</groupId>
            <artifactId>os-maven-plugin</artifactId>
            <version>1.7.0</version>
        </extension>
    </extensions>
    <plugins>
        <!-- Spring boot / maven  -->
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>

        <!-- PROTO -->
        <plugin>
            <groupId>org.xolstice.maven.plugins</groupId>
            <artifactId>protobuf-maven-plugin</artifactId>
            <version>0.6.1</version>
            <configuration>
                <protocArtifact>com.google.protobuf:protoc:3.25.5:exe:${os.detected.classifier}</protocArtifact>
                <pluginId>grpc-java</pluginId>
                <pluginArtifact>io.grpc:protoc-gen-grpc-java:1.68.1:exe:${os.detected.classifier}</pluginArtifact>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>compile</goal>
                        <goal>compile-custom</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>

```

## Kafka Setup

Set up run configurations with the following details for Docker Image type in IntelliJ

- Image ID or name: apache/kafka:latest
- Container name: kafka
- Bind Ports: 9092:9092 9094:9094 9093:9093
- Run options: --network internal
- Environment variables: 

```
KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://kafka:9092,EXTERNAL://localhost:9094
KAFKA_CONTROLLER_LISTENER_NAMES=CONTROLLER
KAFKA_CONTROLLER_QUORUM_VOTERS=0@kafka:9093
KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS=0
KAFKA_INTER_BROKER_LISTENER_NAME=PLAINTEXT
KAFKA_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,EXTERNAL:PLAINTEXT
KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093,EXTERNAL://0.0.0.0:9094
KAFKA_NODE_ID=0
KAFKA_NUM_PARTITIONS=3
KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1
KAFKA_PROCESS_ROLES=broker,controller
KAFKA_TRANSACTION_STATE_LOG_MIN_ISR=1
KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR=1
```

## Analytics Service
---

### Protobuf/Kafka

Dependencies (add in addition to whats there)

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>3.3.0</version>
</dependency>

<dependency>
    <groupId>com.google.protobuf</groupId>
    <artifactId>protobuf-java</artifactId>
    <version>4.29.1</version>
</dependency>
```

Update the build section in pom.xml with the following

```xml
<build>
    <extensions>
        <!-- Ensure OS compatibility for protoc -->
        <extension>
            <groupId>kr.motd.maven</groupId>
            <artifactId>os-maven-plugin</artifactId>
            <version>1.7.0</version>
        </extension>
    </extensions>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>

        <plugin>
            <groupId>org.xolstice.maven.plugins</groupId>
            <artifactId>protobuf-maven-plugin</artifactId>
            <version>0.6.1</version>
            <configuration>
                <protocArtifact>com.google.protobuf:protoc:3.25.5:exe:${os.detected.classifier}</protocArtifact>
                <pluginId>grpc-java</pluginId>
                <pluginArtifact>io.grpc:protoc-gen-grpc-java:1.68.1:exe:${os.detected.classifier}</pluginArtifact>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>compile</goal>
                        <goal>compile-custom</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

Create Docker run configuration from IntelliJ

- Dockerfile: analytics-service\Dockerfile
- Image tag: analytics-service:latest
- Container name: analytics-service
- Bind Ports: 4002:4002
- Run options: --network internal
- Environment variables: 

```
SPRING_KAFKA_BOOTSTRAP_SERVERS=kafka:9092
```

## API Gateway
---

Set up run configurations with the following details for Dockerfile type in IntelliJ

- Dockerfile: analytics-service\Dockerfile
- Image tag: api-gateway:latest
- Container name: api-gateway
- Bind Ports: 4004:4004
- Run options: --network internal

## Auth Service

Set up run configuration with the following details for Dockerfile type in IntelliJ

- Dockerfile: auth-service\Dockerfile
- Image tag: auth-service:latest
- Container name: auth-service
- Bind Ports: 4005:4005
- Run options: --network internal
- Environment Variables

```
SPRING_DATASOURCE_PASSWORD=password
SPRING_DATASOURCE_URL=jdbc:postgresql://auth-service-db:5432/db
SPRING_DATASOURCE_USERNAME=admin_user
SPRING_JPA_HIBERNATE_DDL_AUTO=update
SPRING_SQL_INIT_MODE=always
JWT_SECRET=9c2ecef1916ccededc2bb3b3e74314be7a15f458c9e983a24a683ef72394f8c2 (some random string)
```