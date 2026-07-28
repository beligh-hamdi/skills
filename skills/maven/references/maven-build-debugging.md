# Maven Build Debugging

## Purpose
Maven build debugging techniques for troubleshooting build failures, performance issues, and configuration problems.

## Debug Output

### Enable Debug Mode
```bash
# Enable debug output
mvn clean install -X

# Enable debug for specific goal
mvn compile -X

# Enable debug for specific plugin
mvn compiler:compile -X
```

### Error Output
```bash
# Show error stack traces
mvn clean install -e

# Show both debug and error
mvn clean install -X -e
```

## Build Failures

### Compilation Errors
```bash
# Show compilation details
mvn compile -X

# Check effective POM
mvn help:effective-pom

# Verify dependencies
mvn dependency:tree
```

### Test Failures
```bash
# Run specific test
mvn test -Dtest=TestClass

# Run with debug
mvn test -X

# Skip tests
mvn install -DskipTests

# Continue on test failure
mvn install -Dmaven.test.failure.ignore=true
```

### Dependency Resolution Issues
```bash
# Check dependency tree
mvn dependency:tree

# Force update dependencies
mvn clean install -U

# Resolve dependencies
mvn dependency:resolve

# Check for conflicts
mvn dependency:tree -Dverbose
```

## Performance Issues

### Build Time Analysis
```bash
# Measure build time
time mvn clean install

# Use Maven build timer
mvn clean install -Dmaven.build.timer=true

# Profile build performance
mvn clean install -Dmaven.build.profile=true
```

### Memory Issues
```bash
# Increase Maven memory
export MAVEN_OPTS="-Xmx2048m -Xms512m"

# Use parallel builds
mvn clean install -T 4

# Use offline mode
mvn clean install -o
```

### Dependency Resolution Performance
```bash
# Use mirror for faster resolution
# Configure in settings.xml

# Use repository manager
# Configure Nexus/Artifactory

# Enable incremental compilation
# Configure in maven-compiler-plugin
```

## Configuration Debugging

### Effective POM Analysis
```bash
# View effective POM
mvn help:effective-pom

# Save to file
mvn help:effective-pom -Doutput=effective.xml

# Compare with original
diff pom.xml effective.xml
```

### Effective Settings
```bash
# View effective settings
mvn help:effective-settings

# Check repository configuration
mvn help:effective-settings | grep repositories

# Check mirror configuration
mvn help:effective-settings | grep mirrors
```

### Profile Debugging
```bash
# List active profiles
mvn help:active-profiles

# List all profiles
mvn help:all-profiles

# Activate specific profile
mvn clean install -Pprofile-name

# Debug profile activation
mvn clean install -X | grep profile
```

## Plugin Debugging

### Plugin Execution
```bash
# List plugin goals
mvn help:describe -Dplugin=compiler

# Describe plugin in detail
mvn help:describe -Dplugin=compiler -Ddetail

# Execute specific goal
mvn compiler:compile

# Debug plugin execution
mvn compiler:compile -X
```

### Plugin Configuration
```bash
# Check effective plugin configuration
mvn help:effective-pom | grep -A 20 "plugin"

# Verify plugin version
mvn help:effective-pom | grep "plugin.*version"

# Check plugin dependencies
mvn dependency:tree -Dincludes=plugin:group
```

### Plugin Conflicts
```bash
# List all plugins
mvn help:describe -Dcmd=plugins

# Check plugin management
mvn help:effective-pom | grep pluginManagement

# Verify plugin inheritance
mvn help:effective-pom | grep -B 5 -A 5 "plugin"
```

## Network Issues

### Repository Connectivity
```bash
# Test repository connectivity
mvn dependency:get -Dartifact=group:artifact:version

# Check repository configuration
mvn help:effective-settings | grep repositories

# Test specific repository
mvn dependency:list -DremoteRepositories=repo-url
```

### Proxy Issues
```bash
# Check proxy configuration
mvn help:effective-settings | grep proxy

# Test with proxy
export MAVEN_OPTS="-Dhttp.proxyHost=proxy -Dhttp.proxyPort=8080"
mvn clean install

# Bypass proxy
export MAVEN_OPTS="-Dhttp.proxyHost="
mvn clean install
```

### Authentication Issues
```bash
# Check server configuration
mvn help:effective-settings | grep servers

# Test authentication
mvn deploy -DrepositoryId=repo-id

# Verify credentials
mvn help:effective-settings | grep -A 5 repo-id
```

## Multi-Module Debugging

### Reactor Build Order
```bash
# Show reactor projects
mvn -projects list

# Display dependency graph
mvn graph:reactor

# Build specific module
mvn install -pl module-name

# Build with dependencies
mvn install -pl module-name -am
```

### Module Dependencies
```bash
# Check module dependencies
mvn dependency:tree

# Analyze inter-module dependencies
mvn dependency:analyze

# Check module order
mvn help:effective-pom | grep modules
```

## Common Issues and Solutions

### Out of Memory
```bash
# Increase heap size
export MAVEN_OPTS="-Xmx2048m -Xms512m"

# Use PermSize for older Java
export MAVEN_OPTS="-XX:MaxPermSize=512m"

# Use GC tuning
export MAVEN_OPTS="-XX:+UseG1GC"
```

### Stuck Builds
```bash
# Check what Maven is doing
mvn clean install -X

# Kill stuck processes
jps | grep Maven
kill -9 <pid>

# Check network connectivity
ping repo.maven.apache.org
```

### Classpath Issues
```bash
# Show classpath
mvn dependency:build-classpath

# Analyze classpath
mvn dependency:analyze

# Check for conflicts
mvn dependency:tree -Dverbose
```

## Logging Configuration

### Maven Logging
```bash
# Set log level
export MAVEN_OPTS="-Dmaven.log.level=DEBUG"

# Configure logging in settings.xml
<settings>
    <loggingLevel>DEBUG</loggingLevel>
</settings>
```

### Plugin Logging
```bash
# Enable plugin debug
mvn clean install -Dplugin.pluginId.debug=true

# Set specific plugin log level
export MAVEN_OPTS="-Dplugin.pluginId.log.level=DEBUG"
```

## Build Reproducibility

### Checksum Verification
```bash
# Verify checksums
mvn verify -DchecksumPolicy=fail

# Generate checksums
mvn install -DcreateChecksum=true

# Check for snapshot issues
mvn dependency:tree | grep SNAPSHOT
```

### Build Environment
```bash
# Show environment info
mvn -version

# Show Java info
java -version

# Show effective settings
mvn help:effective-settings
```

## Debugging Tools

### Maven Debug Plugin
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-debug-plugin</artifactId>
    <version>1.0.0</version>
</plugin>
```

### IDE Integration
```bash
# Import Maven project in IDE
# Use IDE's debugging capabilities

# Set breakpoints in Maven plugins
# Attach debugger to Maven process
export MAVEN_OPTS="-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=y,address=5005"
```

## Advanced Debugging

### Maven Internal Debugging
```bash
# Enable Plexus debugging
export MAVEN_OPTS="-Dplexus.debug=true"

# Enable component debugging
export MAVEN_OPTS="-Dmaven.component.debug=true"

# Enable lifecycle debugging
export MAVEN_OPTS="-Dmaven.lifecycle.debug=true"
```

### Custom Debug Scripts
```bash
#!/bin/bash
# debug-maven.sh
export MAVEN_OPTS="-Xmx2048m -Xms512m -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005"
mvn "$@"
```

## Best Practices
- Use `-X` flag for detailed debugging
- Check effective POM for configuration issues
- Use dependency tree for dependency problems
- Monitor build times for performance issues
- Use parallel builds for multi-module projects
- Configure appropriate memory settings
- Test in clean environment
- Document debug procedures
- Use version control for reproducibility
- Leverage IDE debugging capabilities

## Useful Commands
```bash
# Debug build
mvn clean install -X

# Effective POM
mvn help:effective-pom

# Dependency tree
mvn dependency:tree

# Active profiles
mvn help:active-profiles

# Plugin description
mvn help:describe -Dplugin=compiler -Ddetail

# Force update
mvn clean install -U

# Parallel build
mvn clean install -T 4

# Offline mode
mvn clean install -o

# Skip tests
mvn install -DskipTests

# Increase memory
export MAVEN_OPTS="-Xmx2048m"
```