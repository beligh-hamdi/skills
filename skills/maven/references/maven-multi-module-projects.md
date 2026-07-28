# Maven Multi-Module Projects

## Parent POM Structure
```xml
<project>
    <groupId>com.example</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0.0</version>
    <packaging>pom</packaging>
    
    <modules>
        <module>core</module>
        <module>web</module>
        <module>api</module>
    </modules>
    
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>
    
    <dependencyManagement>
        <dependencies>
            <!-- Shared dependency versions -->
        </dependencies>
    </dependencyManagement>
    
    <build>
        <pluginManagement>
            <plugins>
                <!-- Shared plugin configurations -->
            </plugins>
        </pluginManagement>
    </build>
</project>
```

## Module POM Structure
```xml
<project>
    <parent>
        <groupId>com.example</groupId>
        <artifactId>parent-project</artifactId>
        <version>1.0.0</version>
    </parent>
    
    <artifactId>core</artifactId>
    <packaging>jar</packaging>
    
    <dependencies>
        <!-- Module-specific dependencies -->
    </dependencies>
</project>
```

## Inter-Module Dependencies
```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>core</artifactId>
    <version>${project.version}</version>
</dependency>
```

## Reactor Build Order
Maven automatically determines build order based on dependencies:
1. Modules with no dependencies first
2. Then modules that depend on those
3. And so on, following dependency graph

## Build Specific Modules
```bash
# Build specific module
mvn install -pl web

# Build module and its dependencies
mvn install -pl web -am

# Build module and modules that depend on it
mvn install -pl web -amd

# Build multiple modules
mvn install -pl core,web

# Build all except specified modules
mvn install -pl '!api'
```

## Shared Configuration
Inherit common configuration in parent POM:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

## Module-Specific Configuration
Override parent configuration in module POM:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>21</source> <!-- Override parent -->
            </configuration>
        </plugin>
    </plugins>
</build>
```

## Aggregation vs Inheritance
- **Aggregation**: Build multiple modules together (`<modules>`)
- **Inheritance**: Share configuration (`<parent>`)
- Can use both in same parent POM

## Useful Commands
```bash
# Build all modules
mvn install

# Build from specific module
mvn install -rf web

# List reactor projects
mvn -projects list

# Display dependency graph
mvn graph:reactor
```