# Maven Plugins Configuration

## Plugin Management
Configure plugin versions in pluginManagement:

```xml
<build>
    <pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>17</source>
                    <target>17</target>
                </configuration>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
```

## Common Plugin Configurations

### Compiler Plugin
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <source>17</source>
        <target>17</target>
        <compilerArgs>
            <arg>-parameters</arg>
        </compilerArgs>
    </configuration>
</plugin>
```

### Surefire Plugin (Unit Tests)
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <configuration>
        <includes>
            <include>**/*Test.java</include>
            <include>**/*Tests.java</include>
        </includes>
        <parallel>methods</parallel>
        <threadCount>4</threadCount>
    </configuration>
</plugin>
```

### Failsafe Plugin (Integration Tests)
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-failsafe-plugin</artifactId>
    <executions>
        <execution>
            <goals>
                <goal>integration-test</goal>
                <goal>verify</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### Resources Plugin
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-resources-plugin</artifactId>
    <configuration>
        <encoding>UTF-8</encoding>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <filtering>true</filtering>
            </resource>
        </resources>
    </configuration>
</plugin>
```

### Jar Plugin
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <configuration>
        <archive>
            <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.example.Main</mainClass>
            </manifest>
        </archive>
    </configuration>
</plugin>
```

## Plugin Executions
Bind plugin goals to lifecycle phases:

```xml
<plugin>
    <groupId>org.example</groupId>
    <artifactId>example-plugin</artifactId>
    <executions>
        <execution>
            <id>custom-execution</id>
            <phase>process-resources</phase>
            <goals>
                <goal>custom-goal</goal>
            </goals>
            <configuration>
                <customParameter>value</customParameter>
            </configuration>
        </execution>
    </executions>
</plugin>
```

## Plugin Dependencies
Add dependencies for plugins:

```xml
<plugin>
    <groupId>org.example</groupId>
    <artifactId>example-plugin</artifactId>
    <dependencies>
        <dependency>
            <groupId>org.additional</groupId>
            <artifactId>additional-lib</artifactId>
            <version>1.0.0</version>
        </dependency>
    </dependencies>
</plugin>
```

## Useful Commands
```bash
# List all plugins
mvn help:describe -Dcmd=plugins

# Describe specific plugin
mvn help:describe -Dplugin=compiler

# Execute specific plugin goal
mvn compiler:compile

# Skip plugin execution
mvn install -Dmaven.test.skip=true

# List plugin updates
mvn versions:display-plugin-updates
```