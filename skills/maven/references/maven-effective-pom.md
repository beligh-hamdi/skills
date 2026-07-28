# Maven Effective POM

## Purpose
The effective POM is the result of merging the project's POM with all parent POMs, super POM, and applied profiles. It shows the complete configuration used by Maven during the build.

## Viewing Effective POM

### Display Effective POM
```bash
# Display effective POM
mvn help:effective-pom

# Display to file
mvn help:effective-pom -Doutput=effective-pom.xml

# Display for specific module
mvn help:effective-pom -pl module-name
```

### Display Effective Settings
```bash
# Display effective settings
mvn help:effective-settings

# Display to file
mvn help:effective-settings -Doutput=effective-settings.xml
```

## POM Inheritance Hierarchy

### Inheritance Order
1. **Super POM**: Maven's default POM
2. **Parent POMs**: All parent POMs in hierarchy
3. **Project POM**: The current project's POM
4. **Profiles**: Active profiles merged in

### Super POM Location
```bash
# Find super POM
mvn help:effective-pom | grep "maven/super-pom"

# View super POM content
# Located in Maven installation: lib/maven-model-builder-x.x.x.jar
```

## POM Merging Rules

### Property Resolution
```xml
<!-- Parent POM -->
<properties>
    <project.version>1.0.0</project.version>
    <java.version>17</java.version>
</properties>

<!-- Child POM -->
<properties>
    <java.version>21</java.version> <!-- Overrides parent -->
    <custom.property>value</custom.property> <!-- Added -->
</properties>

<!-- Effective POM -->
<properties>
    <project.version>1.0.0</project.version> <!-- Inherited -->
    <java.version>21</java.version> <!-- Overridden -->
    <custom.property>value</custom.property> <!-- Added -->
</properties>
```

### Dependency Management
```xml
<!-- Parent POM -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>6.0.0</version>
        </dependency>
    </dependencies>
</dependencyManagement>

<!-- Child POM -->
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <!-- Version inherited from dependencyManagement -->
    </dependency>
</dependencies>
```

### Plugin Management
```xml
<!-- Parent POM -->
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

<!-- Child POM -->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <!-- Configuration inherited from pluginManagement -->
        </plugin>
    </plugins>
</build>
```

## Profile Merging

### Profile Activation
```bash
# Activate specific profile
mvn help:effective-pom -Pprofile-name

# Activate multiple profiles
mvn help:effective-pom -Pprofile1,profile2

# Activate by property
mvn help:effective-pom -Denvironment=production
```

### Profile Merging Rules
```xml
<!-- Project POM -->
<profiles>
    <profile>
        <id>dev</id>
        <properties>
            <database.url>jdbc:h2:mem:devdb</database.url>
        </properties>
    </profile>
</profiles>

<!-- Effective POM (with dev profile active) -->
<properties>
    <database.url>jdbc:h2:mem:devdb</database.url>
</properties>
```

## Useful Analysis

### Compare POMs
```bash
# Save project POM
mvn help:effective-pom -Doutput=effective.xml

# Compare with original POM
diff pom.xml effective.xml
```

### Check Plugin Configuration
```bash
# View effective plugin configuration
mvn help:effective-pom | grep -A 20 "maven-compiler-plugin"

# Check specific plugin
mvn help:describe -Dplugin=compiler -Ddetail
```

### Verify Dependency Versions
```bash
# Check resolved dependency versions
mvn help:effective-pom | grep -A 5 "dependencies"

# Compare with dependency tree
mvn dependency:tree
```

## Common Scenarios

### Debugging Build Issues
```bash
# Check if configuration is applied
mvn help:effective-pom | grep "configuration"

# Verify plugin versions
mvn help:effective-pom | grep "plugin"

# Check property values
mvn help:effective-pom | grep "properties"
```

### Understanding Inherited Configuration
```bash
# View inherited dependencies
mvn help:effective-pom | grep -B 5 -A 5 "dependencyManagement"

# Check inherited plugins
mvn help:effective-pom | grep -B 5 -A 5 "pluginManagement"
```

### Profile Validation
```bash
# Check profile activation
mvn help:effective-pom -Pprofile-name

# Verify profile properties
mvn help:effective-pom -Pprofile-name | grep "properties"
```

## Advanced Usage

### Effective POM for Specific Goal
```bash
# View effective POM for specific goal
mvn help:effective-pom -Dcmd=compile

# View effective POM for specific phase
mvn help:effective-pom -Dcmd=package
```

### Effective POM with Settings
```bash
# Combine with effective settings
mvn help:effective-pom && mvn help:effective-settings

# Check repository configuration
mvn help:effective-settings | grep "repositories"
```

### Remote Repository Analysis
```bash
# Check effective repository configuration
mvn help:effective-pom | grep "repositories"

# Verify mirror configuration
mvn help:effective-settings | grep "mirrors"
```

## Interpretation Guide

### Key Sections to Check
1. **Properties**: Verify property values and overrides
2. **Dependencies**: Check resolved dependency versions
3. **Dependency Management**: Review version management
4. **Build/Plugins**: Validate plugin configurations
5. **Repositories**: Confirm repository settings
6. **Profiles**: Check active profile contributions

### Common Patterns
```xml
<!-- Standard dependency inheritance -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>6.0.0</version> <!-- From dependencyManagement -->
</dependency>

<!-- Plugin configuration inheritance -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.11.0</version> <!-- From pluginManagement -->
    <configuration>
        <source>17</source> <!-- From pluginManagement -->
        <target>17</target> <!-- From pluginManagement -->
    </configuration>
</plugin>
```

## Troubleshooting

### Unexpected Configuration
```bash
# Identify source of configuration
mvn help:effective-pom -Doutput=effective.xml
# Search effective.xml for the unexpected element

# Check parent POMs
mvn help:effective-pom | grep -B 10 "unexpected-element"
```

### Missing Configuration
```bash
# Verify configuration is in effective POM
mvn help:effective-pom | grep "expected-configuration"

# Check if profile is active
mvn help:active-profiles
```

### Version Conflicts
```bash
# Check resolved versions in effective POM
mvn help:effective-pom | grep "version"

# Compare with dependency tree
mvn dependency:tree -Dverbose
```

## Best Practices
- Review effective POM when debugging build issues
- Use effective POM to understand inheritance
- Compare effective POM before/after changes
- Document critical configuration decisions
- Validate profile configurations
- Check plugin configurations in effective POM
- Use effective POM for build reproducibility verification
- Keep effective POM documentation for complex projects

## Integration with Other Tools

### XML Analysis
```bash
# Format effective POM for readability
mvn help:effective-pom | xmllint --format -

# Extract specific sections
mvn help:effective-pom | grep -A 20 "dependencies"
```

### Scripting
```bash
# Extract all properties
mvn help:effective-pom | grep -o '<.*>.*</.*>' | grep property

# Extract plugin versions
mvn help:effective-pom | grep -A 2 "plugin" | grep version
```

### Documentation Generation
```bash
# Generate documentation from effective POM
mvn help:effective-pom -Doutput=effective-pom.xml
# Process effective-pom.xml with documentation tools
```

## Useful Commands
```bash
# Display effective POM
mvn help:effective-pom

# Save to file
mvn help:effective-pom -Doutput=effective.xml

# With profile
mvn help:effective-pom -Pprofile-name

# Effective settings
mvn help:effective-settings

# Active profiles
mvn help:active-profiles

# Describe plugin
mvn help:describe -Dplugin=compiler -Ddetail

# Effective POM for specific goal
mvn help:effective-pom -Dcmd=compile
```