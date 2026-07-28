# Maven Wrapper

## Purpose
Maven Wrapper allows you to run Maven projects without requiring Maven to be installed on the system. It ensures consistent builds across different environments.

## Directory Structure
```
project/
├── mvnw                    # Unix shell script
├── mvnw.cmd               # Windows batch script
└── .mvn/
    ├── wrapper/
    │   ├── maven-wrapper.jar
    │   └── maven-wrapper.properties
```

## Installation

### Add to Existing Project
```bash
# Using Maven
mvn wrapper:wrapper

# Specify Maven version
mvn wrapper:wrapper -Dmaven=3.9.5

# Specify Maven version and distribution type
mvn wrapper:wrapper -Dmaven=3.9.5 -Dtype=only-script
```

### Manual Installation
1. Download `mvnw` and `mvnw.cmd` from Maven Wrapper GitHub
2. Create `.mvn/wrapper/` directory
3. Download `maven-wrapper.jar` and `maven-wrapper.properties`

## Configuration

### maven-wrapper.properties
```properties
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
wrapperUrl=https://repo.maven.apache.org/maven2/org/apache/maven/wrapper/maven-wrapper/3.2.0/maven-wrapper-3.2.0.jar
```

### Environment Variables
```bash
# Set custom Maven home
export MAVEN_HOME=/path/to/custom/maven

# Set custom Maven repository
export MAVEN_REPO=/path/to/custom/repo
```

## Usage

### Unix/Linux/macOS
```bash
# Make executable (first time only)
chmod +x mvnw

# Run Maven commands
./mvnw clean install

# Run with specific profile
./mvnw clean install -Pproduction

# Run with debug
./mvnw clean install -X
```

### Windows
```cmd
# Run Maven commands
mvnw.cmd clean install

# Run with specific profile
mvnw.cmd clean install -Pproduction
```

## Distribution Types

### Binary Distribution (default)
```properties
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
```

### Source Distribution
```properties
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-src.zip
```

### Script Only (use system Maven)
```bash
mvn wrapper:wrapper -Dtype=only-script
```

## Version Management

### Update Wrapper Version
```bash
# Update to latest Maven Wrapper
./mvnw wrapper:wrapper

# Update to specific Maven version
./mvnw wrapper:wrapper -Dmaven=3.9.6
```

### Check Current Version
```bash
# View maven-wrapper.properties
cat .mvn/wrapper/maven-wrapper.properties

# Check downloaded Maven version
./mvnw -version
```

## CI/CD Integration

### GitHub Actions
```yaml
- name: Build with Maven Wrapper
  run: ./mvnw clean install
```

### GitLab CI
```yaml
build:
  script:
    - ./mvnw clean install
```

### Jenkins
```groovy
sh './mvnw clean install'
```

## Security Considerations

### Verify JAR Signatures
```bash
# Verify Maven distribution
curl -O https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip.asc
gpg --verify apache-maven-3.9.5-bin.zip.asc apache-maven-3.9.5-bin.zip
```

### Use Corporate Repository
```properties
distributionUrl=https://corporate-repo.com/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
```

## Troubleshooting

### Permission Issues
```bash
# Make script executable
chmod +x mvnw

# Fix Windows script permissions
git update-index --chmod=+x mvnw
```

### Download Issues
```bash
# Clear wrapper cache
rm -rf ~/.m2/wrapper

# Use offline mode with existing installation
./mvnw -o clean install
```

### Network Issues
```bash
# Set proxy
export MAVEN_OPTS="-Dhttp.proxyHost=proxy.example.com -Dhttp.proxyPort=8080"

# Use local Maven installation
export MAVEN_HOME=/path/to/local/maven
./mvnw clean install
```

## Best Practices
- Always commit Maven Wrapper files to version control
- Keep Maven Wrapper version up to date
- Use specific Maven versions for reproducibility
- Document Maven version requirements in project README
- Consider using corporate repository for distributions
- Test wrapper in CI/CD pipelines
- Include wrapper files in `.gitignore` for distribution-specific JARs if needed

## Advanced Configuration

### Custom Wrapper Properties
```properties
# Set JVM options
 MAVEN_OPTS=-Xmx1024m -Xms512m

# Set custom local repository
 MAVEN_REPO_LOCAL=/path/to/repo
```

### Multiple Maven Versions
```bash
# Use different Maven versions for different projects
./mvnw -version  # Shows project-specific version
```

## Useful Commands
```bash
# Install wrapper with specific version
mvn wrapper:wrapper -Dmaven=3.9.5

# Update wrapper to latest Maven
./mvnw wrapper:wrapper

# Clean wrapper cache
rm -rf ~/.m2/wrapper

# Force re-download
rm -rf ~/.m2/wrapper/dists
./mvnw clean install
```