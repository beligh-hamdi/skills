# Maven Dependency Resolver

## Purpose
Maven dependency resolver handles the complex process of determining which versions of dependencies to use, resolving conflicts, and building the dependency graph.

## Resolution Process

### Dependency Graph Construction
1. **Direct Dependencies**: Dependencies declared in POM
2. **Transitive Dependencies**: Dependencies of dependencies
3. **Conflict Resolution**: Nearest definition strategy
4. **Dependency Management**: Version enforcement
5. **Scope Filtering**: Apply appropriate scopes
6. **Optional Exclusion**: Remove optional dependencies

### Nearest Definition Strategy
Maven uses "nearest definition wins" for conflict resolution:
- Direct dependencies beat transitive dependencies
- Shallower dependencies beat deeper dependencies
- First declared wins when at same depth

## Dependency Scopes

### Scope Resolution Matrix
| Dependency Scope | Compile Scope | Test Scope | Runtime Scope | Provided Scope |
|-----------------|---------------|------------|---------------|----------------|
| compile          | ✓             | ✓          | ✓             | -              |
| provided         | ✓             | ✓          | -             | ✓              |
| runtime          | -             | ✓          | ✓             | -              |
| test             | -             | ✓          | -             | -              |

### Scope Inheritance Rules
```xml
<!-- Compile scope (default) -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.14.0</version>
    <!-- No scope means compile -->
</dependency>

<!-- Provided scope -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>

<!-- Runtime scope -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
    <scope>runtime</scope>
</dependency>

<!-- Test scope -->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
</dependency>
```

## Conflict Resolution

### Version Conflicts
```bash
# Analyze conflicts
mvn dependency:tree -Dverbose

# Find specific conflicts
mvn dependency:tree -Dincludes=group:artifact -Dverbose
```

### Resolution Strategies

#### 1. Dependency Management (Recommended)
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.conflicting</groupId>
            <artifactId>conflicting-lib</artifactId>
            <version>2.0.0</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

#### 2. Direct Declaration
```xml
<dependency>
    <groupId>org.conflicting</groupId>
    <artifactId>conflicting-lib</artifactId>
    <version>2.0.0</version>
</dependency>
```

#### 3. Exclusion
```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.14.0</version>
    <exclusions>
        <exclusion>
            <groupId>org.conflicting</groupId>
            <artifactId>conflicting-lib</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### Dependency Constraints (Maven 3.9+)
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>[3.13.0,4.0.0)</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Transitive Dependencies

### Controlling Transitive Dependencies
```xml
<!-- Exclude specific transitive dependency -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.14.0</version>
    <exclusions>
        <exclusion>
            <groupId>org.unwanted</groupId>
            <artifactId>unwanted-lib</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!-- Exclude all transitive dependencies -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.14.0</version>
    <exclusions>
        <exclusion>
            <groupId>*</groupId>
            <artifactId>*</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### Optional Dependencies
```xml
<!-- Mark dependency as optional -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.14.0</version>
    <optional>true</optional>
</dependency>
```

## Dependency Management

### BOM (Bill of Materials)
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

### Custom Dependency Management
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.14.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-collections4</artifactId>
            <version>4.4</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Repository Resolution

### Repository Configuration
```xml
<repositories>
    <repository>
        <id>central</id>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
    <repository>
        <id>spring-milestones</id>
        <url>https://repo.spring.io/milestone</url>
    </repository>
</repositories>
```

### Repository Mirrors
```xml
<mirrors>
    <mirror>
        <id>central-mirror</id>
        <name>Central Repository Mirror</name>
        <url>https://repo.maven.apache.org/maven2</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
```

**Security Warning**: Mirror overrides of `central` can redirect all dependency resolution to alternative repositories. Ensure mirrors are trusted, maintain artifact integrity verification (checksums/signing), and are properly secured. Only use mirrors from trusted sources within your organization. Replace example URLs with your actual corporate mirror URLs.

### Authentication
```xml
<!-- INSECURE - Do not hardcode credentials -->
<servers>
    <server>
        <id>private-repo</id>
        <username>username</username>
        <password>password</password>
    </server>
</servers>

<!-- SECURE - Use environment variables or Maven settings -->
<servers>
    <server>
        <id>private-repo</id>
        <username>${env.REPO_USERNAME}</username>
        <password>${env.REPO_PASSWORD}</password>
    </server>
</servers>

<!-- Alternative: Use Maven settings.xml with encrypted master password -->
<!-- See: https://maven.apache.org/guides/mini/guide-encryption.html -->
```

## Dependency Analysis

### Dependency Analysis Plugin
```bash
# Analyze for unused declared dependencies
mvn dependency:analyze

# Analyze for used undeclared dependencies
mvn dependency:analyze-dep-mgt

# Full analysis
mvn dependency:analyze -Dverbose
```

### Dependency Checker Plugin
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
        <execution>
            <id>analyze</id>
            <goals>
                <goal>analyze-only</goal>
            </goals>
            <configuration>
                <failOnWarning>true</failOnWarning>
                <ignoreNonCompile>true</ignoreNonCompile>
            </configuration>
        </execution>
    </executions>
</plugin>
```

## Troubleshooting

### Missing Dependencies
```bash
# Force update dependencies
mvn clean install -U

# Clear local repository cache
rm -rf ~/.m2/repository

# Check dependency resolution
mvn dependency:resolve
```

### Resolution Failures
```bash
# Enable debug output
mvn dependency:resolve -X

# Check repository connectivity
mvn dependency:list -Dverbose

# Verify dependency exists
mvn dependency:get -Dartifact=group:artifact:version
```

### Circular Dependencies
```bash
# Detect circular dependencies
mvn dependency:tree -Dverbose

# Break circular dependency by refactoring
# Use interfaces to break tight coupling
```

## Best Practices
- Use dependency management for version control
- Prefer BOMs for coordinated dependencies
- Regular dependency analysis to prevent bloat
- Exclude unnecessary transitive dependencies
- Monitor for security vulnerabilities
- Use specific versions rather than ranges
- Document dependency decisions
- Test dependency resolution in clean environment
- Use dependency constraints for Maven 3.9+
- Consider using dependency locks for reproducibility

## Security Best Practices
- **Never hardcode credentials** in pom.xml or settings.xml
- Use environment variables or secret managers for sensitive data
- Enable artifact integrity verification (checksums/signing)
- Use trusted repository mirrors only
- Regularly audit dependencies for vulnerabilities (OWASP Dependency-Check)
- Implement dependency locking to prevent supply chain attacks
- Restrict repository access with proper authentication
- Use HTTPS for all repository URLs
- Avoid wildcard mirrorOf configurations that redirect to untrusted sources
- Keep Maven and plugins updated for security patches

## Advanced Features

### Dependency Locking (Maven 3.9+)
```bash
# Generate dependency lock file
mvn dependency:go-offline

# Use dependency lock file
mvn deploy -DdependencyLock=strict
```

### Provider Selection
```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.14.0</version>
    <type>jar</type>
</dependency>
```

### Artifact Resolution
```bash
# Resolve specific artifact
mvn dependency:get -Dartifact=group:artifact:version

# Resolve from specific repository
mvn dependency:get -Dartifact=group:artifact:version -DremoteRepositories=repo-url
```

## Useful Commands
```bash
# Resolve dependencies
mvn dependency:resolve

# Resolve with sources
mvn dependency:resolve-sources

# Resolve with javadoc
mvn dependency:resolve -Dclassifier=javadoc

# List dependencies
mvn dependency:list

# List with scopes
mvn dependency:list -DincludeScope=compile

# Tree analysis
mvn dependency:tree

# Analyze unused
mvn dependency:analyze

# Force update
mvn clean install -U

# Go offline
mvn dependency:go-offline
```