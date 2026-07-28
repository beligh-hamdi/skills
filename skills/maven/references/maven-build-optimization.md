# Maven Build Optimization

## Parallel Build Execution
Enable parallel builds for multi-module projects:

```bash
# Use 4 threads
mvn install -T 4

# Use 1 thread per CPU core
mvn install -T 1C

# Use 2 threads per CPU core
mvn install -T 2C

# Smart parallel build (Maven 3.9+)
mvn install -T 1C -Dmaven.parallel.deploy
```

### Parallel Module Builds
```bash
# Build specific module and dependencies in parallel
mvn install -pl module-name -am -T 4

# Build modules in parallel with dependency awareness
mvn install -T 1C -Dmaven.parallel.optimize
```

## Maven Daemon (mvnd)
Use Maven Daemon for significantly faster builds:

```bash
# Install Maven Daemon
brew install mvnd  # macOS
choco install mvnd  # Windows

# Use Maven Daemon
mvnd clean install

# Parallel build with daemon
mvnd clean install -T 4
```

**Benefits**:
- JVM process stays alive between builds
- Reduced startup time
- Better memory utilization
- Built-in parallel build support

## Incremental Compilation
Configure compiler plugin for incremental builds:

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.12.1</version>
    <configuration>
        <useIncrementalCompilation>true</useIncrementalCompilation>
        <optimize>true</optimize>
        <debug>false</debug>
    </configuration>
</plugin>
```

### Advanced Incremental Options
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <useIncrementalCompilation>true</useIncrementalCompilation>
        <compilerArgs>
            <arg>-Xlint:all</arg>
            <arg>-parameters</arg>
        </compilerArgs>
        <fork>true</fork>
        <compilerArgs>
            <arg>-J-Xmx512m</arg>
        </compilerArgs>
    </configuration>
</plugin>
```

## Offline Mode
Use offline mode when dependencies are available locally:

```bash
mvn install -o

# Force offline mode with warning
mvn install -o -Dmaven.offline=true
```

## Repository Configuration
Configure mirrors for faster dependency resolution:

```xml
<mirrors>
    <mirror>
        <id>central-mirror</id>
        <name>Central Repository Mirror</name>
        <url>https://corporate-mirror.com/maven2</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
    <mirror>
        <id>all-mirror</id>
        <name>All Repositories Mirror</name>
        <url>https://corporate-mirror.com/maven2</url>
        <mirrorOf>*</mirrorOf>
    </mirror>
</mirrors>
```

### Repository Optimization
```xml
<repositories>
    <repository>
        <id>fast-repo</id>
        <url>https://fast-repo.com/maven2</url>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>never</updatePolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

## Dependency Resolution Optimization

### Minimal Dependencies
```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-lib</artifactId>
    <version>1.0.0</version>
    <type>jar</type>
    <scope>compile</scope>
    <optional>false</optional>
    <exclusions>
        <exclusion>
            <groupId>org.unnecessary</groupId>
            <artifactId>unnecessary-lib</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### Dependency Analysis
```bash
# Analyze for unused dependencies
mvn dependency:analyze

# Find used but undeclared dependencies
mvn dependency:analyze-dep-mgt

# Optimize dependency tree
mvn dependency:tree -Dverbose
```

## Plugin Optimization

### Test Plugin Optimization
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.2.5</version>
    <configuration>
        <parallel>methods</parallel>
        <threadCount>4</threadCount>
        <perCoreThreadCount>true</perCoreThreadCount>
        <forkCount>1C</forkCount>
        <reuseForks>true</reuseForks>
        <argLine>-Xmx1024m -XX:+UseG1GC</argLine>
    </configuration>
</plugin>
```

### Compiler Plugin Optimization
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.12.1</version>
    <configuration>
        <release>17</release>
        <encoding>UTF-8</encoding>
        <optimize>true</optimize>
        <debug>false</debug>
        <fork>true</fork>
        <compilerArgs>
            <arg>-J-Xmx512m</arg>
        </compilerArgs>
    </configuration>
</plugin>
```

## Skip Unnecessary Steps
```bash
# Skip tests
mvn install -DskipTests

# Skip test compilation and execution
mvn install -Dmaven.test.skip=true

# Skip plugin execution
mvn install -Dplugin.pluginId.skip=true

# Skip specific phases
mvn install -Dmaven.main.skip=false
```

## Maven Build Cache (Maven 3.9+)
Enable build cache for faster rebuilds:

```xml
<build>
    <cache>
        <enabled>true</enabled>
        <directory>${project.build.directory}/maven-cache</directory>
    </cache>
</build>
```

### Build Cache Configuration
```bash
# Enable build cache
mvn install -Dmaven.build.cache.enabled=true

# Configure cache location
mvn install -Dmaven.build.cache.dir=/path/to/cache

# Clear build cache
mvn install -Dmaven.build.cache.clear=true
```

## Resource Filtering Optimization
```xml
<resources>
    <resource>
        <directory>src/main/resources</directory>
        <filtering>false</filtering> <!-- Disable when not needed -->
    </resource>
    <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
        <includes>
            <include>**/*.properties</include>
            <include>**/*.xml</include>
        </includes>
    </resource>
</resources>
```

## Memory Configuration
Increase Maven memory for large projects:

```bash
# Standard memory settings
export MAVEN_OPTS="-Xmx2048m -Xms512m"

# High-performance settings
export MAVEN_OPTS="-Xmx4096m -Xms1024m -XX:MaxMetaspaceSize=512m"

# GC optimization
export MAVEN_OPTS="-Xmx2048m -Xms512m -XX:+UseG1GC -XX:MaxGCPauseMillis=200"

# For very large projects
export MAVEN_OPTS="-Xmx8192m -Xms2048m -XX:+UseG1GC -XX:MaxMetaspaceSize=1024m"
```

### Plugin-Specific Memory
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <configuration>
        <argLine>-Xmx1024m -XX:+UseG1GC</argLine>
    </configuration>
</plugin>
```

## Profile Optimization
Use profiles for environment-specific builds:

```xml
<profiles>
    <profile>
        <id>fast</id>
        <properties>
            <skipTests>true</skipTests>
            <maven.compiler.optimize>true</maven.compiler.optimize>
            <maven.compiler.debug>false</maven.compiler.debug>
        </properties>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <configuration>
                        <optimize>true</optimize>
                        <debug>false</debug>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </profile>
    <profile>
        <id>ci</id>
        <properties>
            <skipTests>false</skipTests>
        </properties>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <configuration>
                        <parallel>methods</parallel>
                        <threadCount>4</threadCount>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </profile>
</profiles>
```

## Multi-Module Optimization

### Reactor Optimization
```bash
# Build specific module and dependencies
mvn install -pl module-name -am

# Build dependents of a module
mvn install -pl module-name -amd

# Resume from specific module
mvn install -rf :module-name

# Skip unchanged modules
mvn install -amd
```

### Module Ordering
```xml
<!-- Optimize module order for parallel builds -->
<modules>
    <module>common</module>  <!-- Build first - no dependencies -->
    <module>api</module>     <!-- Depends on common -->
    <module>service</module> <!-- Depends on api -->
    <module>web</module>     <!-- Depends on service -->
</modules>
```

## Build Time Analysis

### Maven Build Timer
```bash
# Analyze build time with built-in timer
time mvn clean install

# Use Maven Build Timer extension
mvn clean install -Dmaven.build.timer=true

# Profile build performance
mvn clean install -Dmaven.build.profile=true
```

### Performance Profiling
```bash
# Enable Maven profiler
mvn clean install -Dmaven.profiler=true

# Generate performance report
mvn clean install -Dmaven.profiler.output=profile.txt
```

## Network Optimization

### Dependency Download Optimization
```bash
# Force update snapshots
mvn clean install -U

# Skip snapshot updates
mvn clean install -Dmaven.snapshot-update=false

# Use local repository only
mvn clean install -o
```

### Repository Connection Pooling
```xml
<settings>
    <servers>
        <server>
            <id>central</id>
            <configuration>
                <connectionTimeout>5000</connectionTimeout>
                <readTimeout>30000</readTimeout>
            </configuration>
        </server>
    </servers>
</settings>
```

## Advanced Optimization Techniques

### Compiler Caching
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <useIncrementalCompilation>true</useIncrementalCompilation>
        <meminitial>512m</meminitial>
        <maxmem>1024m</maxmem>
    </configuration>
</plugin>
```

### Test Parallelization Strategies
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <configuration>
        <parallel>methods</parallel>  <!-- methods, classes, both -->
        <threadCount>4</threadCount>
        <perCoreThreadCount>true</perCoreThreadCount>
        <forkCount>1C</forkCount>
        <reuseForks>true</reuseForks>
    </configuration>
</plugin>
```

## CI/CD Optimization

### Jenkins Pipeline Optimization
```groovy
stage('Build') {
    parallel(
        "Compile": { sh 'mvn compile -T 4' },
        "Test": { sh 'mvn test -T 4' },
        "Package": { sh 'mvn package -T 4' }
    )
}
```

### GitHub Actions Optimization
```yaml
- name: Build with Maven
  run: mvn clean install -T 4 -B

- name: Cache Maven packages
  uses: actions/cache@v3
  with:
    path: ~/.m2/repository
    key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
```

## Useful Commands
```bash
# Build time analysis
time mvn clean install

# Profile activation
mvn install -Pfast

# Show effective POM
mvn help:effective-pom

# Display project info
mvn help:evaluate -Dexpression=project.version

# Clean build
mvn clean install

# Parallel build
mvn install -T 4

# Daemon build
mvnd clean install

# Offline build
mvn install -o

# Force update
mvn install -U

# Dependency analysis
mvn dependency:analyze

# Dependency tree
mvn dependency:tree

# Build with debug
mvn install -X

# Active profiles
mvn help:active-profiles
```