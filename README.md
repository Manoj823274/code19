<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>packaging-demo</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>

  <modules>
    <module>shared-lib</module>
    <module>boot-module</module>
    <module>ear-assembly</module>
  </modules>
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>3.2.5</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>
</project>
<project …>
  <parent>
    <groupId>com.example</groupId>
    <artifactId>packaging-demo</artifactId>
    <version>1.0.0</version>
  </parent>

  <artifactId>shared-lib</artifactId>
  <packaging>jar</packaging>
</project>
<project …>
  <parent>
    <groupId>com.example</groupId>
    <artifactId>packaging-demo</artifactId>
    <version>1.0.0</version>
  </parent>

  <artifactId>boot-module</artifactId>
  <packaging>jar</packaging>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- optional linkage to the shared library inside the EAR -->
    <dependency>
      <groupId>com.example</groupId>
      <artifactId>shared-lib</artifactId>
      <version>${project.version}</version>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <!-- produces the “fat” JAR with all deps -->
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
// boot-module/src/main/java/com/example/bootmodule/BootModuleApplication.java
package com.example.bootmodule;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BootModuleApplication {
    public static void main(String[] args) {
        SpringApplication.run(BootModuleApplication.class, args);
    }
}
<project …>
  <parent>
    <groupId>com.example</groupId>
    <artifactId>packaging-demo</artifactId>
    <version>1.0.0</version>
  </parent>

  <artifactId>ear-assembly</artifactId>
  <packaging>ear</packaging>

  <dependencies>
    <!-- Pull in the modules we want to bundle -->
    <dependency>
      <groupId>com.example</groupId>
      <artifactId>boot-module</artifactId>
      <version>${project.version}</version>
      <type>jar</type>
    </dependency>

    <dependency>
      <groupId>com.example</groupId>
      <artifactId>shared-lib</artifactId>
      <version>${project.version}</version>
      <type>jar</type>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-ear-plugin</artifactId>
        <version>3.1.0</version>
        <configuration>
          <!-- Place libs under /lib in the EAR -->
          <defaultLibBundleDir>lib</defaultLibBundleDir>

          <modules>
            <jarModule>
              <groupId>com.example</groupId>
              <artifactId>boot-module</artifactId>
              <bundleFileName>boot-module.jar</bundleFileName>
            </jarModule>

            <jarModule>
              <groupId>com.example</groupId>
              <artifactId>shared-lib</artifactId>
              <bundleFileName>shared-lib.jar</bundleFileName>
            </jarModule>
          </modules>

          <!-- Java EE version the EAR targets -->
          <version>8</version> <!-- adjust for Jakarta 9+ if needed -->
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
