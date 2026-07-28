# Popular Maven Plugins

## Testing Plugins

### Surefire Plugin (Unit Tests)
**Latest Version**: 3.2.5

**Purpose**: Runs unit tests during the test phase.

**Configuration**:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.2.5</version>
    <configuration>
        <includes>
            <include>**/*Test.java</include>
            <include>**/*Tests.java</include>
            <include>**/Test*.java</include>
        </includes>
        <excludes>
            <exclude>**/*IT.java</exclude>
            <exclude>**/*IntegrationTest.java</exclude>
        </excludes>
        <parallel>methods</parallel>
        <threadCount>4</threadCount>
        <forkCount>1C</forkCount>
        <reuseForks>true</reuseForks>
        <argLine>-Xmx1024m -XX:+UseG1GC</argLine>
        <systemPropertyVariables>
            <spring.profiles.active>test</spring.profiles.active>
        </systemPropertyVariables>
        <environmentVariables>
            <TEST_ENV>true</TEST_ENV>
        </environmentVariables>
    </configuration>
</plugin>
```

**Common Options**:
- `parallel`: Enable parallel test execution
- `threadCount`: Number of threads for parallel execution
- `forkCount`: Number of forked JVMs
- `reuseForks`: Reuse forked JVMs
- `argLine`: JVM arguments for test execution

### Failsafe Plugin (Integration Tests)
**Latest Version**: 3.2.5

**Purpose**: Runs integration tests during the integration-test and verify phases.

**Configuration**:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-failsafe-plugin</artifactId>
    <version>3.2.5</version>
    <configuration>
        <includes>
            <include>**/*IT.java</include>
            <include>**/*IntegrationTest.java</include>
        </includes>
        <skipAfterFailureCount>3</skipAfterFailureCount>
        <argLine>-Xmx1024m</argLine>
    </configuration>
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

**Key Features**:
- Runs after package phase
- Separate from unit tests
- Can use different configuration
- Failures don't fail the build until verify phase

### JaCoCo Plugin (Code Coverage)
**Latest Version**: 0.8.11

**Purpose**: Provides code coverage analysis for Java projects.

**Configuration**:
```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.11</version>
    <executions>
        <execution>
            <id>prepare-agent</id>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
        </execution>
        <execution>
            <id>report</id>
            <phase>test</phase>
            <goals>
                <goal>report</goal>
            </goals>
        </execution>
        <execution>
            <id>check</id>
            <goals>
                <goal>check</goal>
            </goals>
            <configuration>
                <rules>
                    <rule>
                        <element>BUNDLE</element>
                        <limits>
                            <limit>
                                <counter>LINE</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.80</minimum>
                            </limit>
                            <limit>
                                <counter>BRANCH</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.70</minimum>
                            </limit>
                        </limits>
                    </rule>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```

**Coverage Metrics**:
- `LINE`: Line coverage
- `BRANCH`: Branch coverage
- `COMPLEXITY`: Cyclomatic complexity
- `METHOD`: Method coverage
- `CLASS`: Class coverage

### PITest Plugin (Mutation Testing)
**Latest Version**: 1.15.3

**Purpose**: Performs mutation testing to verify test effectiveness.

**Configuration**:
```xml
<plugin>
    <groupId>org.pitest</groupId>
    <artifactId>pitest-maven</artifactId>
    <version>1.15.3</version>
    <configuration>
        <targetClasses>
            <param>com.example.*</param>
        </targetClasses>
        <targetTests>
            <param>com.example.*</param>
        </targetTests>
        <mutationThreshold>80</mutationThreshold>
        <coverageThreshold>80</coverageThreshold>
        <timestampedReports>false</timestampedReports>
        <failWhenNoMutations>false</failWhenNoMutations>
    </configuration>
</plugin>
```

**Usage**:
```bash
# Run mutation testing
mvn org.pitest:pitest-maven:mutationCoverage
```

## Code Quality Plugins

### Spotless Plugin (Code Formatting)
**Latest Version**: 2.43.0

**Purpose**: Automates code formatting and keeps code consistent.

**Configuration**:
```xml
<plugin>
    <groupId>com.diffplug.spotless</groupId>
    <artifactId>spotless-maven-plugin</artifactId>
    <version>2.43.0</version>
    <configuration>
        <java>
            <importOrder/>
            <removeUnusedImports/>
            <formatAnnotations/>
            <palantirJavaFormat>
                <version>2.38.0</version>
            </palantirJavaFormat>
        </java>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>check</goal>
            </goals>
            <phase>compile</phase>
        </execution>
    </executions>
</plugin>
```

**Commands**:
```bash
# Check formatting
mvn spotless:check

# Apply formatting
mvn spotless:apply

# Check specific format
mvn spotless:check -Dspotless.check.java=google-java-format
```

### Checkstyle Plugin
**Latest Version**: 10.12.7

**Purpose**: Checks Java source code for adherence to coding standards.

**Configuration**:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-checkstyle-plugin</artifactId>
    <version>10.12.7</version>
    <configuration>
        <configLocation>google_checks.xml</configLocation>
        <consoleOutput>true</consoleOutput>
        <failsOnError>true</failsOnError>
        <violationSeverity>warning</violationSeverity>
    </configuration>
    <executions>
        <execution>
            <id>validate</id>
            <phase>validate</phase>
            <goals>
                <goal>check</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### PMD Plugin
**Latest Version**: 7.0.0

**Purpose**: Static code analysis for finding common programming flaws.

**Configuration**:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-pmd-plugin</artifactId>
    <version>7.0.0</version>
    <configuration>
        <rulesets>
            <ruleset>/rulesets/java/quickstart.xml</ruleset>
        </rulesets>
        <failOnViolation>true</failOnViolation>
    </configuration>
    <executions>
        <execution>
            <phase>verify</phase>
            <goals>
                <goal>check</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### SpotBugs Plugin
**Latest Version**: 4.8.3

**Purpose**: Detects bugs and potential issues in Java code.

**Configuration**:
```xml
<plugin>
    <groupId>com.github.spotbugs</groupId>
    <artifactId>spotbugs-maven-plugin</artifactId>
    <version>4.8.3</version>
    <configuration>
        <effort>Max</effort>
        <threshold>Low</threshold>
        <failOnError>true</failOnError>
        <includeFilterFile>spotbugs-include.xml</includeFilterFile>
    </configuration>
    <executions>
        <execution>
            <phase>verify</phase>
            <goals>
                <goal>check</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

## Build Plugins

### Compiler Plugin
**Latest Version**: 3.12.1

**Purpose**: Compiles Java source code.

**Configuration**:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.12.1</version>
    <configuration>
        <release>17</release>
        <encoding>UTF-8</encoding>
        <showWarnings>true</showWarnings>
        <showDeprecation>true</showDeprecation>
        <compilerArgs>
            <arg>-Xlint:all</arg>
            <arg>-parameters</arg>
        </compilerArgs>
        <annotationProcessorPaths>
            <path>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>1.18.30</version>
            </path>
        </annotationProcessorPaths>
    </configuration>
</plugin>
```

### Shade Plugin
**Latest Version**: 3.5.1

**Purpose**: Creates uber JAR files with all dependencies included.

**Configuration**:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>3.5.1</version>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>shade</goal>
            </goals>
            <configuration>
                <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>com.example.Main</mainClass>
                    </transformer>
                </transformers>
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
        </execution>
    </executions>
</plugin>
```

## Frontend Plugins

### Frontend Maven Plugin
**Latest Version**: 1.15.0

**Purpose**: Integrates Node.js and npm tools into Maven builds.

**Configuration**:
```xml
<plugin>
    <groupId>com.github.eirslett</groupId>
    <artifactId>frontend-maven-plugin</artifactId>
    <version>1.15.0</version>
    <configuration>
        <nodeVersion>v20.10.0</nodeVersion>
        <npmVersion>10.2.3</npmVersion>
    </configuration>
    <executions>
        <execution>
            <id>install node and npm</id>
            <goals>
                <goal>install-node-and-npm</goal>
            </goals>
        </execution>
        <execution>
            <id>npm install</id>
            <goals>
                <goal>npm</goal>
            </goals>
            <configuration>
                <arguments>install</arguments>
            </configuration>
        </execution>
        <execution>
            <id>npm build</id>
            <goals>
                <goal>npm</goal>
            </goals>
            <configuration>
                <arguments>run build</arguments>
            </configuration>
        </execution>
    </executions>
</plugin>
```

## Toolchain Plugin

### Maven Toolchains Plugin
**Latest Version**: 3.1.0

**Purpose**: Manages JDK toolchains for different Java versions.

**Configuration**:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-toolchains-plugin</artifactId>
    <version>3.1.0</version>
    <configuration>
        <toolchains>
            <toolchain>
                <type>jdk</type>
                <provides>
                    <version>17</version>
                    <vendor>oracle</vendor>
                </provides>
            </toolchain>
        </toolchains>
    </configuration>
</plugin>
```

**Toolchains Configuration** (`~/.m2/toolchains.xml`):
```xml
<toolchains>
    <toolchain>
        <type>jdk</type>
        <provides>
            <version>17</version>
            <vendor>oracle</vendor>
        </provides>
        <configuration>
            <jdkHome>/path/to/jdk-17</jdkHome>
        </configuration>
    </toolchain>
    <toolchain>
        <type>jdk</type>
        <provides>
            <version>21</version>
            <vendor>oracle</vendor>
        </provides>
        <configuration>
            <jdkHome>/path/to/jdk-21</jdkHome>
        </configuration>
    </toolchain>
</toolchains>
```

## Spring Plugins

### Spring Boot Maven Plugin
**Latest Version**: 3.2.0

**Purpose**: Provides Spring Boot support in Maven builds.

**Configuration**:
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>3.2.0</version>
    <configuration>
        <mainClass>com.example.Application</mainClass>
        <executable>true</executable>
        <image>
            <name>myorg/myapp</name>
        </image>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>repackage</goal>
                <goal>build-info</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

## Version Management

### Versions Maven Plugin
**Latest Version**: 2.16.2

**Purpose**: Manages dependency and plugin versions.

**Commands**:
```bash
# Display dependency updates
mvn versions:display-dependency-updates

# Display plugin updates
mvn versions:display-plugin-updates

# Display property updates
mvn versions:display-property-updates

# Use latest versions
mvn versions:use-latest-versions

# Set specific version
mvn versions:set -DnewVersion=1.0.0
```

## Release Management

### Maven Release Plugin
**Latest Version**: 3.0.1

**Purpose**: Manages project releases.

**Configuration**:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-release-plugin</artifactId>
    <version>3.0.1</version>
    <configuration>
        <autoVersionSubmodules>true</autoVersionSubmodules>
        <useReleaseProfile>false</useReleaseProfile>
        <releaseProfiles>release</releaseProfiles>
        <goals>deploy</goals>
    </configuration>
</plugin>
```

## Best Practices

### Plugin Version Management
```xml
<build>
    <pluginManagement>
        <plugins>
            <!-- Define all plugin versions centrally -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.12.1</version>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
```

### Plugin Ordering
1. Clean plugins
2. Compiler plugins
3. Resource plugins
4. Testing plugins
5. Packaging plugins
6. Quality plugins
7. Release plugins

### Performance Tips
- Use parallel execution where possible
- Configure appropriate memory settings
- Use daemon mode (mvnd) for faster builds
- Enable incremental compilation
- Skip unnecessary plugins in development