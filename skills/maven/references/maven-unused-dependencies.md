# Maven Unused Dependencies Analysis

## Purpose
Maven unused dependency analysis helps identify dependencies that are declared in your POM but not actually used in your code, as well as dependencies that are used but not declared. This keeps your dependency tree clean, reduces build time, and minimizes security exposure.

## Commands

### dependency:analyze
```bash
# Basic analysis
mvn dependency:analyze

# Verbose output with detailed explanations
mvn dependency:analyze -Dverbose

# Analyze specific module in multi-module project
mvn dependency:analyze -pl module-name

# Analyze all modules
mvn dependency:analyze -pl :all-modules
```

### dependency:analyze-only
```bash
# Analysis without running tests
mvn dependency:analyze-only

# With verbose output
mvn dependency:analyze-only -Dverbose

# Combine with other goals
mvn clean compile dependency:analyze-only
```

## Understanding the Output

### Output Categories

#### Used Declared Dependencies
Dependencies that are both declared in your POM and actually used in your code. These are correct and should be kept.

#### Used Undeclared Dependencies
Dependencies that are used in your code but not directly declared in your POM. These are brought in transitively by other dependencies.

**Action**: Consider declaring them directly for:
- Explicit dependency management
- Version control
- Build reproducibility
- Documentation purposes

#### Unused Declared Dependencies
Dependencies declared in your POM but not used in your code.

**Action**: Remove them to:
- Reduce build time
- Minimize security exposure
- Clean up dependency tree
- Avoid potential conflicts

### Example Output
```
[WARNING] Used undeclared dependencies found:
[WARNING]    org.slf4j:slf4j-api:jar:1.7.36:compile
[WARNING] Unused declared dependencies found:
[WARNING]    commons-logging:commons-logging:jar:1.2:compile
```

## Configuration Options

### Plugin Configuration
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>3.6.0</version>
    <executions>
        <execution>
            <id>analyze</id>
            <goals>
                <goal>analyze-only</goal>
            </goals>
            <configuration>
                <!-- Fail build on warnings -->
                <failOnWarning>true</failOnWarning>
                
                <!-- Ignore non-compile scope dependencies -->
                <ignoreNonCompile>true</ignoreNonCompile>
                
                <!-- Ignore specific dependencies -->
                <ignoredDependencies>
                    <ignoredDependency>org.example:example-lib</ignoredDependency>
                </ignoredDependencies>
                
                <!-- Ignore dependencies used in test scope -->
                <ignoreTestScope>true</ignoreTestScope>
                
                <!-- Verbose output -->
                <verbose>true</verbose>
            </configuration>
        </execution>
    </executions>
</plugin>
```

### Command Line Options
```bash
# Control failure behavior
mvn dependency:analyze -DfailOnWarning=false

# Ignore non-compile dependencies
mvn dependency:analyze -DignoreNonCompile=true

# Ignore test scope dependencies
mvn dependency:analyze -DignoreTestScope=true

# Specify output file
mvn dependency:analyze -DoutputFile=dependency-analysis.txt
```

## Integration with Build Lifecycle

### Binding to Verify Phase
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
        <execution>
            <id>analyze-dependencies</id>
            <phase>verify</phase>
            <goals>
                <goal>analyze-only</goal>
            </goals>
            <configuration>
                <failOnWarning>true</failOnWarning>
            </configuration>
        </execution>
    </executions>
</plugin>
```

### Binding to Test Phase
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
        <execution>
            <id>analyze-dependencies</id>
            <phase>test</phase>
            <goals>
                <goal>analyze-only</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

## Advanced Analysis

### Dependency Analysis vs Dependency Management
```bash
# Analyze dependency management section
mvn dependency:analyze-dep-mgt

# Check for duplicate dependency management entries
mvn dependency:analyze-dep-mgt -Dverbose
```

### Combining with Other Dependency Goals
```bash
# Full dependency analysis pipeline
mvn dependency:tree \
    dependency:analyze \
    dependency:analyze-dep-mgt

# Analyze and resolve in one command
mvn dependency:resolve dependency:analyze

# Check for dependency updates after analysis
mvn versions:display-dependency-updates
```

## Scope Considerations

### Compile Scope Analysis
```bash
# Default analysis focuses on compile scope
mvn dependency:analyze

# Include all scopes in analysis
mvn dependency:analyze -DignoreNonCompile=false
```

### Test Scope Analysis
```bash
# Analyze test dependencies separately
mvn dependency:analyze -DignoreTestScope=false

# Focus only on test scope
mvn test dependency:analyze -DignoreNonCompile=true
```

### Runtime vs Provided Dependencies
```bash
# Runtime dependencies may not be detected in compile analysis
mvn dependency:analyze -DignoreNonCompile=false

# Provided dependencies often appear as unused
mvn dependency:analyze -DignoreNonCompile=true
```

## Common Issues and Solutions

### False Positives

#### Reflection Usage
Dependencies used via reflection may not be detected:
```xml
<configuration>
    <ignoredDependencies>
        <ignoredDependency>org.reflections:reflections</ignoredDependency>
    </ignoredDependencies>
</configuration>
```

#### Annotation Processors
Annotation processors may appear unused:
```xml
<configuration>
    <ignoredDependencies>
        <ignoredDependency>org.mapstruct:mapstruct-processor</ignoredDependency>
    </ignoredDependencies>
</configuration>
```

#### Runtime Dependencies
Runtime-only dependencies:
```xml
<configuration>
    <ignoreNonCompile>true</ignoreNonCompile>
</configuration>
```

### Framework-Specific Dependencies

#### Spring Boot Starters
Spring Boot starters may include unused transitive dependencies:
```bash
# Analyze individual starter dependencies
mvn dependency:tree -Dincludes=org.springframework.boot:*
```

#### Jakarta EE / Java EE
Enterprise dependencies often require careful scope configuration:
```xml
<dependency>
    <groupId>jakarta.platform</groupId>
    <artifactId>jakarta.jakartaee-api</artifactId>
    <version>10.0.0</version>
    <scope>provided</scope>
</dependency>
```

## Best Practices

### Regular Analysis
```bash
# Run analysis as part of regular development
mvn clean compile dependency:analyze

# Include in CI/CD pipeline
mvn verify
```

### Incremental Cleanup
```bash
# Start with non-failing analysis
mvn dependency:analyze -DfailOnWarning=false

# Gradually remove unused dependencies
# Re-run analysis after each removal
```

### Documentation
```xml
<!-- Document why dependencies are kept despite appearing unused -->
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-lib</artifactId>
    <version>1.0.0</version>
    <!-- Used in reflection-based plugin system -->
</dependency>
```

### Version Management
```bash
# Combine with dependency management analysis
mvn dependency:analyze-dep-mgt

# Check for version conflicts
mvn dependency:tree -Dverbose
```

## Multi-Module Projects

### Analyze Specific Module
```bash
# Analyze single module
mvn dependency:analyze -pl module-name

# Analyze module with dependencies
mvn dependency:analyze -pl module-name -am
```

### Analyze All Modules
```bash
# Analyze entire project
mvn dependency:analyze

# Analyze from parent directory
mvn -f pom.xml dependency:analyze
```

### Reactor Build Integration
```bash
# Analyze during reactor build
mvn -T 4 clean verify

# Skip analysis for specific modules
mvn dependency:analyze -pl '!module-to-skip'
```

## IDE Integration

### IntelliJ IDEA
1. Open Maven tool window
2. Navigate to your project
3. Right-click → Run Maven Build
4. Command: `dependency:analyze`

### Eclipse
1. Right-click project → Run As → Maven Build
2. Goals: `dependency:analyze`
3. Run

### VS Code
```json
{
    "maven.executable.options": [
        "dependency:analyze"
    ]
}
```

## Troubleshooting

### Analysis Takes Too Long
```bash
# Use analyze-only instead of analyze
mvn dependency:analyze-only

# Run in offline mode
mvn dependency:analyze -o

# Analyze specific module only
mvn dependency:analyze -pl module-name
```

### False Unused Warnings
```bash
# Check if dependency is used in different scope
mvn dependency:analyze -DignoreNonCompile=false

# Verify with dependency tree
mvn dependency:tree -Dincludes=group:artifact

# Check for runtime usage
mvn test dependency:analyze
```

### Build Failures
```bash
# Run without failing on warnings
mvn dependency:analyze -DfailOnWarning=false

# Check verbose output for details
mvn dependency:analyze -Dverbose

# Verify plugin configuration
mvn help:describe -Dplugin=dependency -Ddetail
```

## Complementary Tools

### OWASP Dependency-Check
```bash
# Security analysis combined with dependency analysis
mvn org.owasp:dependency-check-maven:check
mvn dependency:analyze
```

### Maven Enforcer Plugin
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-enforcer-plugin</artifactId>
    <executions>
        <execution>
            <id>enforce-dependency-convergence</id>
            <goals>
                <goal>enforce</goal>
            </goals>
            <configuration>
                <rules>
                    <dependencyConvergence/>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```

### Versions Maven Plugin
```bash
# Check for updates after cleaning up dependencies
mvn versions:display-dependency-updates
mvn versions:display-plugin-updates
```

## Summary
- Use `mvn dependency:analyze` for comprehensive analysis
- Use `mvn dependency:analyze-only` for faster analysis without test execution
- Configure the plugin to fail builds on dependency issues
- Handle false positives with ignored dependencies configuration
- Integrate analysis into CI/CD pipelines for continuous dependency hygiene
- Combine with other dependency management tools for complete dependency governance