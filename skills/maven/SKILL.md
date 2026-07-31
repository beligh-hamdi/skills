---
name: maven
description: Advanced Maven build automation including dependency management, multi-module projects, plugin configuration, build optimization, and troubleshooting. Use when working with Maven-based projects, configuring Maven plugins and dependencies, optimizing build performance, setting up multi-module projects, or troubleshooting Maven build issues.
license: Apache-2.0
metadata:
  author: beligh
  version: 1.0.0
---

# Advanced Maven Skill

## When to use this skill
- Working with Maven-based projects
- Configuring Maven plugins and dependencies
- Optimizing build performance
- Setting up multi-module projects
- Troubleshooting Maven build issues
- Integrating with CI/CD pipelines

## Workflow

1. **Assess project context**
   - Check existing POM structure
   - Identify Maven version and project type
   - Review current dependencies and plugins

2. **Apply Maven best practices**
   - Configure dependency management properly
   - Set up appropriate plugin configurations
   - Implement build optimization strategies
   - Follow project-specific conventions

3. **Verify and test**
   - Run `mvn validate` to check POM syntax
   - Execute relevant build phases
   - Test specific Maven commands
   - Verify changes don't break builds

## Common Operations

### Add Dependency
```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-lib</artifactId>
    <version>1.0.0</version>
</dependency>
```

### Add Plugin
```xml
<plugin>
    <groupId>org.example</groupId>
    <artifactId>example-plugin</artifactId>
    <version>1.0.0</version>
</plugin>
```

### Configure Profile
```xml
<profile>
    <id>dev</id>
    <properties>
        <environment>development</environment>
    </properties>
</profile>
```

### Multi-Module Parent POM
```xml
<modules>
    <module>module1</module>
    <module>module2</module>
    <module>module3</module>
</modules>
```

## Key Principles

### Dependency Management
- Use dependency management sections for version control
- Prefer BOM (Bill of Materials) for coordinating dependencies
- Keep dependency scopes appropriate (compile, test, runtime, provided)
- Exclude transitive dependencies when conflicts arise
- Use dependency constraints for Maven 3.9+ projects

### Build Lifecycle
- Understand Maven lifecycle phases: validate, compile, test, package, verify, install, deploy
- Use profiles for environment-specific configurations
- Bind plugin executions to appropriate lifecycle phases
- Leverage build phases rather than calling plugins directly

### Plugin Configuration
- Configure plugins in pluginManagement for version consistency
- Use plugin executions to customize behavior
- Set up proper plugin dependencies and requirements
- Configure plugin goals with appropriate parameters

### Multi-Module Projects
- Use parent POM for shared configuration
- Define modules in parent POM
- Use proper inter-module dependencies
- Configure reactor build order
- Share plugin configurations across modules

### Build Optimization
- Configure parallel build execution
- Use incremental compilation
- Optimize dependency resolution
- Configure repository mirrors and proxies
- Use Maven build cache when available

## Troubleshooting

### Dependency Conflicts
- Use `mvn dependency:tree` to analyze conflicts
- Use `mvn dependency:analyze` to find unused dependencies
- Exclude conflicting transitive dependencies
- Use dependency management to enforce versions

### Build Failures
- Check with `mvn -X` for debug output
- Verify repository connectivity
- Check for snapshot version issues
- Validate POM syntax with `mvn validate`

### Performance Issues
- Enable parallel builds: `mvn -T 4`
- Use offline mode when possible: `mvn -o`
- Configure repository mirrors
- Optimize plugin configurations

## Integration with Spring Boot
- Follow Spring Boot parent POM conventions
- Use spring-boot-starter dependencies
- Configure spring-boot-maven-plugin properly
- Use build-info for application metadata

## Best Practices
- Keep POM files clean and well-organized
- Use properties for version management
- Document custom plugin configurations
- Use consistent naming conventions
- Regular dependency updates for security
- Test build reproducibility
- Use Maven Enforcer Plugin for standards
- Configure proper source/target Java versions
- Set up proper resource filtering
- Use Maven Wrapper for project portability

## References

For detailed guidance on specific Maven topics, see:
- [Dependency Management](references/maven-dependency-management.md) - BOM usage, scopes, exclusions, constraints
- [Plugin Configuration](references/maven-plugins-configuration.md) - Plugin management, common plugins, executions
- [Multi-Module Projects](references/maven-multi-module-projects.md) - Parent POM structure, inter-module dependencies, reactor builds
- [Build Optimization](references/maven-build-optimization.md) - Parallel builds, incremental compilation, performance tuning, Maven Daemon
- [Profiles and Lifecycle](references/maven-profiles-lifecycle.md) - Build phases, profile configuration, activation strategies
- [Maven Wrapper](references/maven-wrapper.md) - Installation, configuration, CI/CD integration
- [Dependency Tree](references/maven-dependency-tree.md) - Visualization, filtering, conflict resolution
- [Dependency Resolver](references/maven-dependency-resolver.md) - Resolution process, scope matrix, conflict handling
- [Unused Dependencies](references/maven-unused-dependencies.md) - dependency:analyze, dependency:analyze-only, cleanup strategies
- [Effective POM](references/maven-effective-pom.md) - POM inheritance, merging rules, profile analysis
- [Repository Management](references/maven-repository-management.md) - Repository types, mirrors, authentication, security
- [Enforcer Plugin](references/maven-enforcer-plugin.md) - Build rules, dependency constraints, custom rules
- [Build Debugging](references/maven-build-debugging.md) - Debug techniques, troubleshooting, performance analysis
- [Maven Resources](references/maven-resources.md) - Official links, latest versions, installation guides, IDE integration
- [Popular Plugins](references/maven-popular-plugins.md) - Surefire, Failsafe, JaCoCo, PITest, Spotless, frontend plugins, toolchains