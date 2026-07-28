# Maven Dependency Tree

## Purpose
The Maven Dependency Tree plugin provides a visual representation of project dependencies, helping identify conflicts, unused dependencies, and dependency relationships.

## Basic Usage

### Display Full Dependency Tree
```bash
mvn dependency:tree
```

### Output Formats
```bash
# Text format (default)
mvn dependency:tree

# Verbose output
mvn dependency:tree -Dverbose

# Graphviz DOT format
mvn dependency:tree -DoutputType=dot

# TGF format (Trivial Graph Format)
mvn dependency:tree -DoutputType=tgf

# XML format
mvn dependency:tree -DoutputType=xml
```

## Filtering Options

### Filter by Scope
```bash
# Compile scope only
mvn dependency:tree -Dscope=compile

# Test scope only
mvn dependency:tree -Dscope=test

# Runtime scope only
mvn dependency:tree -Dscope=runtime

# Provided scope only
mvn dependency:tree -Dscope=provided
```

### Filter by GroupId
```bash
# Filter by specific group
mvn dependency:tree -Dincludes=org.springframework

# Filter by multiple groups
mvn dependency:tree -Dincludes=org.springframework,org.hibernate

# Filter by specific artifact
mvn dependency:tree -Dincludes=org.springframework:spring-core
```

### Exclude Dependencies
```bash
# Exclude specific group
mvn dependency:tree -Dexcludes=org.slf4j

# Exclude multiple groups
mvn dependency:tree -Dexcludes=org.slf4j,commons-logging

# Exclude specific artifact
mvn dependency:tree -Dexcludes=org.slf4j:slf4j-api
```

### Filter by Conflicts
```bash
# Show only conflicting dependencies
mvn dependency:tree -Dverbose
```

## Module-Specific Analysis

### Multi-Module Projects
```bash
# Tree for specific module
mvn dependency:tree -pl module-name

# Tree for module and dependencies
mvn dependency:tree -pl module-name -am

# Tree for all modules
mvn dependency:tree -N
```

### Reactor Projects
```bash
# Build aggregation tree
mvn dependency:tree -Daggregate=true

# Show module dependencies
mvn dependency:tree -DappendOutput=true
```

## Output Control

### Output to File
```bash
# Save to file
mvn dependency:tree -DoutputFile=dependency-tree.txt

# Save as DOT file
mvn dependency:tree -DoutputType=dot -DoutputFile=deps.dot
```

### Output Limitations
```bash
# Limit depth
mvn dependency:tree -Ddepth=3

# Limit to direct dependencies only
mvn dependency:tree -Ddepth=1
```

## Conflict Resolution

### Identify Version Conflicts
```bash
# Show all conflicts
mvn dependency:tree -Dverbose

# Find specific conflict
mvn dependency:tree -Dincludes=group:artifact -Dverbose
```

### Resolve Conflicts
```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-lib</artifactId>
    <version>2.0.0</version>
    <exclusions>
        <exclusion>
            <groupId>org.conflicting</groupId>
            <artifactId>conflicting-lib</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### Dependency Management
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.conflicting</groupId>
            <artifactId>conflicting-lib</artifactId>
            <version>1.5.0</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Integration with Other Tools

### Graph Visualization
```bash
# Generate DOT file
mvn dependency:tree -DoutputType=dot -DoutputFile=deps.dot

# Convert to PNG (requires Graphviz)
dot -Tpng deps.dot -o dependency-tree.png

# Convert to SVG
dot -Tsvg deps.dot -o dependency-tree.svg
```

### Dependency Analysis
```bash
# Find unused dependencies
mvn dependency:analyze

# Find used undeclared dependencies
mvn dependency:analyze-dep-mgt

# Full analysis
mvn dependency:analyze && mvn dependency:tree
```

## Common Use Cases

### Troubleshooting ClassNotFound Issues
```bash
# Find missing transitive dependency
mvn dependency:tree -Dverbose

# Check if dependency is excluded
mvn dependency:tree -Dincludes=missing:dependency
```

### Security Auditing
```bash
# List all dependencies for security review
mvn dependency:tree -DoutputFile=security-audit.txt

# Check for vulnerable versions
mvn dependency:tree -Dincludes=vulnerable:artifact
```

### License Compliance
```bash
# Generate dependency list for license review
mvn dependency:tree -DoutputFile=license-compliance.txt

# Combine with license plugin
mvn license:aggregate-add-third-party
```

### Migration Planning
```bash
# Analyze current dependencies before upgrade
mvn dependency:tree -DoutputFile=current-deps.txt

# Compare with new version
mvn dependency:tree -DoutputFile=new-deps.txt
```

## Advanced Scenarios

### Circular Dependencies
```bash
# Detect circular dependencies
mvn dependency:tree -Dverbose | grep -i circular
```

### Snapshot Dependencies
```bash
# Find snapshot dependencies
mvn dependency:tree -Dverbose | grep SNAPSHOT
```

### Optional Dependencies
```bash
# Show optional dependencies
mvn dependency:tree -Dverbose | grep optional
```

## Best Practices
- Regular dependency tree analysis to prevent dependency bloat
- Use dependency management for version control
- Exclude unnecessary transitive dependencies
- Monitor for security vulnerabilities
- Document dependency decisions
- Use tree analysis before major upgrades
- Keep dependency tree as part of documentation

## Useful Commands
```bash
# Full tree with verbose output
mvn dependency:tree -Dverbose

# Tree for specific artifact
mvn dependency:tree -Dincludes=group:artifact

# Save to file
mvn dependency:tree -DoutputFile=tree.txt

# Graph visualization
mvn dependency:tree -DoutputType=dot -DoutputFile=deps.dot

# Compile scope only
mvn dependency:tree -Dscope=compile

# Limit depth
mvn dependency:tree -Ddepth=3

# Multi-module specific
mvn dependency:tree -pl module-name
```