# Maven Dependency Management

## Dependency Management Section
Use the `<dependencyManagement>` section to centralize dependency versions:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>3.2.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## BOM (Bill of Materials)
Import BOMs for coordinated dependency versions:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!-- Version inherited from BOM -->
</dependency>
```

## Dependency Scopes
- **compile**: Default scope, available in all classpaths
- **provided**: Available at compile and test, but not runtime
- **runtime**: Available at runtime and test, but not compile
- **test**: Available only for test compilation and execution
- **system**: Similar to provided, but must explicitly provide JAR

## Excluding Transitive Dependencies
```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-lib</artifactId>
    <version>1.0.0</version>
    <exclusions>
        <exclusion>
            <groupId>org.unwanted</groupId>
            <artifactId>unwanted-lib</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## Dependency Constraints (Maven 3.9+)
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.example</groupId>
            <artifactId>example-lib</artifactId>
            <version>[1.0.0,2.0.0)</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Optional Dependencies
```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-lib</artifactId>
    <version>1.0.0</version>
    <optional>true</optional>
</dependency>
```

## Useful Commands
```bash
# Display dependency tree
mvn dependency:tree

# Analyze dependencies
mvn dependency:analyze

# List unused dependencies
mvn dependency:analyze-dep-mgt

# Resolve dependencies
mvn dependency:resolve

# Display dependency updates
mvn versions:display-dependency-updates
```