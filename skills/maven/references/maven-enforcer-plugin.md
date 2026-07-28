# Maven Enforcer Plugin

## Purpose
The Maven Enforcer Plugin provides rules to enforce constraints on the project environment, ensuring consistency and preventing common issues.

## Basic Configuration

### Plugin Setup
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-enforcer-plugin</artifactId>
            <version>3.4.1</version>
            <executions>
                <execution>
                    <id>enforce-rules</id>
                    <goals>
                        <goal>enforce</goal>
                    </goals>
                    <configuration>
                        <rules>
                            <!-- Rules go here -->
                        </rules>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

## Common Rules

### Require Maven Version
```xml
<rules>
    <requireMavenVersion>
        <version>[3.8.0,)</version>
        <message>Maven 3.8.0 or higher is required</message>
    </requireMavenVersion>
</rules>
```

### Require Java Version
```xml
<rules>
    <requireJavaVersion>
        <version>[17,)</version>
        <message>Java 17 or higher is required</message>
    </requireJavaVersion>
</rules>
```

### Require OS
```xml
<rules>
    <requireOS>
        <family>unix</family>
        <message>This project requires Unix-based OS</message>
    </requireOS>
</rules>
```

### Require Property
```xml
<rules>
    <requireProperty>
        <property>project.version</property>
        <message>Project version must be set</message>
    </requireProperty>
</rules>
```

## Dependency Rules

### Ban Duplicate Dependencies
```xml
<rules>
    <banDuplicatePomDependencyVersions>
        <ignoreScope>test</ignoreScope>
    </banDuplicatePomDependencyVersions>
</rules>
```

### Require Upper Bound Dependencies
```xml
<rules>
    <requireUpperBoundDeps>
        <excludes>
            <exclude>org.example:example-lib</exclude>
        </excludes>
    </requireUpperBoundDeps>
</rules>
```

### Ban Circular Dependencies
```xml
<rules>
    <banCircularDependencies/>
</rules>
```

### Ban Duplicate Classes
```xml
<rules>
    <banDuplicateClasses>
        <ignoreClasses>
            <ignoreClass>org.exampleduplicate.DuplicateClass</ignoreClass>
        </ignoreClasses>
    </banDuplicateClasses>
</rules>
```

## Dependency Convergence

### Require Dependency Convergence
```xml
<rules>
    <dependencyConvergence/>
</rules>
```

### With Exclusions
```xml
<rules>
    <dependencyConvergence>
        <excludes>
            <exclude>org.example:example-lib</exclude>
        </excludes>
    </dependencyConvergence>
</rules>
```

## Standard Rules

### Require No Repositories
```xml
<rules>
    <requireNoRepositories>
        <message>Project should not define repositories in POM</message>
    </requireNoRepositories>
</rules>
```

### Require Plugin Versions
```xml
<rules>
    <requirePluginVersions>
        <banLatest>true</banLatest>
        <banRelease>true</banRelease>
        <banSnapshots>true</banSnapshots>
        <phases>clean,deploy,site</phases>
    </requirePluginVersions>
</rules>
```

### Require Release Dependencies
```xml
<rules>
    <requireReleaseDeps>
        <message>No snapshot dependencies allowed in release</message>
        <onlyWhenRelease>true</onlyWhenRelease>
    </requireReleaseDeps>
</rules>
```

## Profile Rules

### Require Active Profile
```xml
<rules>
    <requireActiveProfile>
        <profiles>dev,test,prod</profiles>
        <all>false</all>
    </requireActiveProfile>
</rules>
```

### Require Property
```xml
<rules>
    <requireProperty>
        <property>environment</property>
        <message>Environment property must be set</message>
        <regex>^(dev|test|prod)$</regex>
        <regexMessage>Environment must be dev, test, or prod</regexMessage>
    </requireProperty>
</rules>
```

## File Rules

### Require Files Exist
```xml
<rules>
    <requireFilesExist>
        <files>
            <file>${project.basedir}/README.md</file>
            <file>${project.basedir}/LICENSE</file>
        </files>
    </requireFilesExist>
</rules>
```

### Require Files Don't Exist
```xml
<rules>
    <requireFilesDontExist>
        <files>
            <file>${project.basedir}/target</file>
        </files>
    </requireFilesDontExist>
</rules>
```

## Reactor Rules

### Require Module Version
```xml
<rules>
    <requirePluginVersions>
        <banLatest>true</banLatest>
        <banRelease>true</banRelease>
    </requirePluginVersions>
</rules>
```

## Custom Rules

### Implement Custom Rule
```java
public class CustomRule extends AbstractEnforcerRule {
    @Override
    public void execute(EnforcerRuleHelper helper) throws EnforcerRuleException {
        // Custom rule logic
    }
}
```

### Use Custom Rule
```xml
<rules>
    <customRule implementation="com.example.CustomRule">
        <customProperty>value</customProperty>
    </customRule>
</rules>
```

## Execution Configuration

### Fail Fast
```xml
<configuration>
    <fail>true</fail>
    <failFast>true</failFast>
</configuration>
```

### Ignore Cache
```xml
<configuration>
    <ignoreCache>true</ignoreCache>
</configuration>
```

### Level Configuration
```xml
<configuration>
    <rules>
        <requireMavenVersion>
            <version>[3.8.0,)</version>
        </requireMavenVersion>
    </rules>
    <fail>true</fail>
</configuration>
```

## Profile-Specific Enforcement

### Different Rules for Different Profiles
```xml
<profiles>
    <profile>
        <id>strict</id>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-enforcer-plugin</artifactId>
                    <executions>
                        <execution>
                            <id>enforce-strict</id>
                            <goals>
                                <goal>enforce</goal>
                            </goals>
                            <configuration>
                                <rules>
                                    <requireReleaseDeps/>
                                    <requirePluginVersions/>
                                </rules>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
    </profile>
</profiles>
```

## Phase Binding

### Bind to Different Phases
```xml
<executions>
    <execution>
        <id>enforce-validate</id>
        <phase>validate</phase>
        <goals>
            <goal>enforce</goal>
        </goals>
    </execution>
    <execution>
        <id>enforce-deploy</id>
        <phase>deploy</phase>
        <goals>
            <goal>enforce</goal>
        </goals>
        <configuration>
            <rules>
                <requireReleaseDeps/>
            </rules>
        </configuration>
    </execution>
</executions>
```

## Troubleshooting

### Debug Enforcer Rules
```bash
# Run with debug output
mvn enforce -X

# Skip enforcer rules
mvn install -Denforcer.skip=true

# Run specific execution
mvn enforce -Drule=enforce-strict
```

### Rule Conflicts
```xml
<configuration>
    <rules>
        <banDuplicatePomDependencyVersions>
            <dependencyScope>compile</dependencyScope>
        </banDuplicatePomDependencyVersions>
    </rules>
</configuration>
```

## Best Practices
- Use enforcer rules in all projects
- Bind to validate phase for early failure
- Use profile-specific rules for different environments
- Document custom rules thoroughly
- Test rules in development before enforcement
- Use failFast for quick feedback
- Combine with dependency management
- Regular review and update of rules
- Use version ranges for flexibility
- Provide clear error messages

## Common Rule Combinations

### Standard Project Rules
```xml
<rules>
    <requireMavenVersion>
        <version>[3.8.0,)</version>
    </requireMavenVersion>
    <requireJavaVersion>
        <version>[17,)</version>
    </requireJavaVersion>
    <requireNoRepositories/>
    <dependencyConvergence/>
</rules>
```

### Strict Release Rules
```xml
<rules>
    <requireReleaseDeps/>
    <requireReleaseDeps>
        <onlyWhenRelease>true</onlyWhenRelease>
    </requireReleaseDeps>
    <requirePluginVersions/>
    <banDuplicatePomDependencyVersions/>
</rules>
```

### Development Rules
```xml
<rules>
    <requireMavenVersion>
        <version>[3.8.0,)</version>
    </requireMavenVersion>
    <requireJavaVersion>
        <version>[17,)</version>
    </requireJavaVersion>
    <requireProperty>
        <property>environment</property>
    </requireProperty>
</rules>
```

## Useful Commands
```bash
# Run enforcer rules
mvn enforce

# Run with specific profile
mvn enforce -Pstrict

# Skip enforcer rules
mvn install -Denforcer.skip=true

# Display enforcer rules
mvn help:describe -Dplugin=enforcer -Ddetail

# Debug enforcer execution
mvn enforce -X
```