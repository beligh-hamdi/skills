# Maven Resources

## Official Links

### Maven Official Sites
- **Maven Official Website**: https://maven.apache.org/
- **Maven Documentation**: https://maven.apache.org/guides/
- **Maven Central Repository**: https://repo.maven.apache.org/maven2/
- **Maven Releases**: https://maven.apache.org/download.cgi
- **Maven GitHub**: https://github.com/apache/maven

### Installation
- **Maven Installation Guide**: https://maven.apache.org/install.html
- **Maven Wrapper**: https://github.com/takari/maven-wrapper
- **Maven Daemon (mvnd)**: https://github.com/apache/maven-mvnd

## Latest Versions (2024)

### Maven Core
- **Latest Stable**: 3.9.9 (December 2024)
- **LTS Version**: 3.8.8
- **Maven 4.0 Alpha**: Available for testing

### Java Compatibility
- **Maven 3.9.x**: Java 8+
- **Maven 3.8.x**: Java 7+
- **Maven 4.0**: Java 17+ (planned)

## Spring Boot Maven Plugin

### Spring Boot Versions
- **Spring Boot 3.2.x**: Latest stable (November 2024)
- **Spring Boot 3.3.x**: Current development
- **Java Requirements**: Java 17+

### Spring Boot Maven Plugin
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>3.2.0</version>
</plugin>
```

## Top Community Plugins

### Testing Plugins
- **Surefire Plugin**: 3.2.5 (Unit tests)
- **Failsafe Plugin**: 3.2.5 (Integration tests)
- **JaCoCo Plugin**: 0.8.11 (Code coverage)
- **PITest Plugin**: 1.15.3 (Mutation testing)

### Code Quality Plugins
- **Spotless Plugin**: 2.43.0 (Code formatting)
- **Checkstyle Plugin**: 10.12.7 (Code style)
- **PMD Plugin**: 7.0.0 (Code analysis)
- **SpotBugs Plugin**: 4.8.3 (Bug detection)
- **Error Prone Plugin**: 2.26.1 (Error detection)

### Build Plugins
- **Compiler Plugin**: 3.12.1 (Java compilation)
- **Resources Plugin**: 3.3.1 (Resource processing)
- **JAR Plugin**: 3.3.0 (JAR creation)
- **WAR Plugin**: 3.4.0 (WAR creation)
- **Shade Plugin**: 3.5.1 (Uber JAR)
- **Assembly Plugin**: 3.6.0 (Custom assemblies)

### Documentation Plugins
- **Javadoc Plugin**: 3.6.3 (Javadoc generation)
- **Source Plugin**: 3.3.0 (Source JAR)
- **Site Plugin**: 4.0.0-M13 (Project site)

### Release Plugins
- **Release Plugin**: 3.0.1 (Release management)
- **Versions Plugin**: 2.16.2 (Version management)
- **SCM Plugin**: 2.1.0 (Source control)

### Frontend Plugins
- **Frontend Plugin**: 1.15.0 (Node.js integration)
- **NPM Plugin**: 1.0.0 (NPM integration)

### Spring Plugins
- **Spring Boot Plugin**: 3.2.0 (Spring Boot support)
- **Dependency Management Plugin**: 1.1.0 (Spring dependency management)

## Toolchain Plugin

### Maven Toolchains
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

## Plugin Updates

### Check for Updates
```bash
# Check plugin updates
mvn versions:display-plugin-updates

# Check dependency updates
mvn versions:display-dependency-updates

# Check property updates
mvn versions:display-property-updates
```

## Useful Maven Commands

### Build Commands
```bash
# Clean build
mvn clean install

# Skip tests
mvn install -DskipTests

# Parallel build
mvn install -T 4

# Offline mode
mvn install -o
```

### Analysis Commands
```bash
# Dependency tree
mvn dependency:tree

# Dependency analysis
mvn dependency:analyze

# Effective POM
mvn help:effective-pom

# Active profiles
mvn help:active-profiles
```

### Repository Commands
```bash
# Deploy to repository
mvn deploy

# Install to local repository
mvn install

# Resolve dependencies
mvn dependency:resolve
```

## IDE Integration

### IntelliJ IDEA
- Built-in Maven support
- Import Maven projects automatically
- Maven tool window for dependency management

### Eclipse
- M2Eclipse plugin
- Import Maven projects
- Dependency management

### VS Code
- Maven for Java extension
- Maven integration in sidebar
- Dependency visualization

## CI/CD Integration

### GitHub Actions
```yaml
- name: Build with Maven
  run: mvn clean install

- name: Deploy to Maven Central
  run: mvn deploy
```

### GitLab CI
```yaml
build:
  script:
    - mvn clean install
```

### Jenkins
```groovy
sh 'mvn clean install'
```

## Performance Tuning

### Memory Settings
```bash
export MAVEN_OPTS="-Xmx2048m -Xms512m -XX:MaxMetaspaceSize=512m"
```

### Parallel Builds
```bash
# Use 4 threads
mvn install -T 4

# Use 1 thread per CPU core
mvn install -T 1C
```

### Build Extensions
- **Maven Daemon (mvnd)**: Faster builds with daemon process
- **Build Cache**: Maven 4.0 feature for caching
- **Incremental Builds**: Only rebuild changed modules

## Security

### Check for Vulnerabilities
```bash
# OWASP Dependency Check
mvn org.owasp:dependency-check-maven:check

# Snyk security scan
snyk test --file=pom.xml
```

### Signed Artifacts
```bash
# Sign artifacts with GPG
mvn verify -Dgpg.skip=false
```

## Monitoring and Debugging

### Build Monitoring
```bash
# Debug mode
mvn install -X

# Error stack traces
mvn install -e

# Quiet mode
mvn install -q
```

### Build Analysis
```bash
# Build time analysis
time mvn clean install

# Profile build performance
mvn clean install -Dmaven.build.profile=true
```

## Migration Guides

### Maven 3.x to 4.0
- Preview available for testing
- Breaking changes documented
- Migration guide available

### Java 8 to 17+
- Update Maven to 3.9.x
- Update plugins to latest versions
- Test thoroughly before migration

## Community Resources

### Stack Overflow
- Maven tag: https://stackoverflow.com/questions/tagged/maven
- Spring Boot tag: https://stackoverflow.com/questions/tagged/spring-boot

### Reddit
- r/java: https://reddit.com/r/java
- r/springboot: https://reddit.com/r/springboot

### Blogs
- Spring Blog: https://spring.io/blog
- Maven Blog: https://maven.apache.org/blog/

## Learning Resources

### Official Documentation
- Maven Getting Started: https://maven.apache.org/guides/getting-started/
- POM Reference: https://maven.apache.org/pom.html
- Settings Reference: https://maven.apache.org/settings.html

### Books
- "Maven: The Complete Reference" (Manning)
- "Java Development with Maven" (O'Reilly)
- "Maven by Example" (Sonatype)

### Courses
- Apache Maven Udemy courses
- Spring Boot with Maven courses
- Build automation courses

## Troubleshooting

### Common Issues
- **Dependency conflicts**: Use dependency:tree
- **Build failures**: Use -X flag for debug
- **Repository issues**: Check settings.xml
- **Memory issues**: Increase MAVEN_OPTS

### Support Channels
- Maven mailing lists
- Apache JIRA for bug reports
- Stack Overflow for questions
- GitHub Discussions for plugins