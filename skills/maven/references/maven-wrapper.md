# Maven Wrapper

## Purpose
Maven Wrapper allows you to run Maven projects without requiring Maven to be installed on the system. It ensures consistent builds across different environments.

## ⚠️ Critical Security Warning

**Runtime Binary Download Risk**: Maven Wrapper downloads and executes remote binaries at runtime. The `distributionUrl` and `wrapperUrl` properties specify remote JAR/ZIP files that are fetched and executed when you run `./mvnw`. While the default URLs point to official Apache Maven repositories, this pattern introduces supply chain security risks:

- **Remote Code Execution**: Binaries are downloaded and executed without manual verification
- **URL Compromise Risk**: If repository URLs are compromised, malicious code could be executed
- **DNS/Cache Poisoning**: Network-level attacks could redirect downloads to malicious servers
- **Supply Chain Attacks**: Compromised build infrastructure could distribute tampered artifacts

**Mitigation Strategies**:
1. Use corporate repository mirrors with strict access controls
2. Verify GPG signatures of downloaded distributions
3. Pre-download and verify binaries in controlled environments
4. Use offline mode with pre-verified distributions
5. Implement CI/CD pipeline verification before wrapper execution
6. Monitor and audit wrapper downloads in enterprise environments

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

**⚠️ Security Warning**: When downloading wrapper files manually, verify GPG signatures and checksums from trusted sources.

## Configuration

### maven-wrapper.properties
```properties
# OFFICIAL APACHE MAVEN REPOSITORY (Recommended for development)
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
wrapperUrl=https://repo.maven.apache.org/maven2/org/apache/maven/wrapper/maven-wrapper/3.2.0/maven-wrapper-3.2.0.jar

# CORPORATE REPOSITORY (Recommended for production/enterprise)
# distributionUrl=https://your-corporate-repo.com/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
# wrapperUrl=https://your-corporate-repo.com/maven2/org/apache/maven/wrapper/maven-wrapper/3.2.0/maven-wrapper-3.2.0.jar
```

**⚠️ Runtime Download Risk**: These URLs are used to download and execute binaries at runtime. Only use trusted repositories.

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

**⚠️ Security Warning**: This URL downloads and executes binary at runtime. Only use trusted repositories.

### Source Distribution
```properties
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-src.zip
```

**⚠️ Security Warning**: This URL downloads and executes binary at runtime. Only use trusted repositories.

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

**⚠️ Security Note**: Implement pre-build verification steps before wrapper execution in CI/CD pipelines.

### GitLab CI
```yaml
build:
  script:
    - ./mvnw clean install
```

**⚠️ Security Note**: Implement pre-build verification steps before wrapper execution in CI/CD pipelines.

### Jenkins
```groovy
sh './mvnw clean install'
```

**⚠️ Security Note**: Implement pre-build verification steps before wrapper execution in CI/CD pipelines.

### Secure CI/CD Pattern
```yaml
# GitHub Actions with verification
- name: Verify Maven Wrapper
  run: |
    # Download and verify signatures before execution
    curl -O https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip.asc
    gpg --verify apache-maven-3.9.5-bin.zip.asc ~/.m2/wrapper/dists/*/apache-maven-*/apache-maven-*/apache-maven-3.9.5-bin.zip
    
- name: Build with Maven Wrapper
  run: ./mvnw clean install
```

## Security Considerations

### ⚠️ Runtime Binary Download Risks

The Maven Wrapper downloads and executes remote binaries at runtime. This introduces significant security considerations:

#### Attack Vectors
- **Repository Compromise**: If the repository is compromised, malicious binaries could be served
- **DNS/Network Attacks**: DNS spoofing or cache poisoning could redirect downloads
- **Supply Chain Attacks**: Compromised build infrastructure could distribute tampered artifacts
- **Man-in-the-Middle**: Network-level attacks could intercept and modify downloads
- **Dependency Confusion**: Similar artifact IDs from malicious repositories

#### Mitigation Strategies

##### 1. Use Corporate Repository Mirrors
```properties
# Use trusted corporate repository with strict access controls
distributionUrl=https://your-corporate-repo.com/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
wrapperUrl=https://your-corporate-repo.com/maven2/org/apache/maven/wrapper/maven-wrapper/3.2.0/maven-wrapper-3.2.0.jar
```

##### 2. Verify GPG Signatures
```bash
# Download and verify Maven distribution signature
curl -O https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip.asc
curl -O https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip

# Import Apache Maven GPG keys
gpg --keyserver keyserver.ubuntu.com --recv-keys B2E69B4724854E0D

# Verify signature
gpg --verify apache-maven-3.9.5-bin.zip.asc apache-maven-3.9.5-bin.zip
```

##### 3. Pre-download and Verify in CI/CD
```yaml
# GitHub Actions example
- name: Download and verify Maven
  run: |
    curl -O https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
    curl -O https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip.asc
    gpg --verify apache-maven-3.9.5-bin.zip.asc apache-maven-3.9.5-bin.zip
    # Use verified local copy
    export MAVEN_HOME=./apache-maven-3.9.5
    ./mvnw -o clean install
```

##### 4. Use Offline Mode with Verified Distributions
```bash
# First download and verify in controlled environment
./mvnw wrapper:wrapper -Dmaven=3.9.5

# Verify the downloaded distribution
# (manual verification steps)

# Then use offline mode for builds
./mvnw -o clean install
```

##### 5. Implement Network-level Controls
- Use corporate DNS with DNSSEC
- Implement SSL/TLS inspection
- Use network segmentation for build servers
- Monitor and log all wrapper downloads
- Implement IP allowlisting for repository access

##### 6. Audit and Monitor
```bash
# Check wrapper cache for unexpected downloads
ls -la ~/.m2/wrapper/dists/

# Verify wrapper JAR checksums
sha256sum ~/.m2/wrapper/dists/*/apache-maven-*/apache-maven-*/bin/mvn

# Monitor network connections during wrapper execution
./mvnw --version &
# Monitor with netstat or similar tools
```

### Repository URL Security

#### Official Apache Maven Repository
```properties
# Default: Official Apache repository (trusted but still remote)
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
```

**Security Notes**:
- Official Apache repositories are generally trusted
- Still subject to DNS/network attacks
- Recommended to use corporate mirrors for enterprise

#### Corporate Repository
```properties
# Enterprise: Corporate repository with access controls
distributionUrl=https://your-corporate-repo.com/maven2/org/apache/maven/apache-maven/3.9.5/apache-maven-3.9.5-bin.zip
```

**Security Notes**:
- Provides additional security through access controls
- Can implement additional verification steps
- Should have SSL/TLS and authentication
- Monitor for unauthorized access attempts

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
export MAVEN_OPTS="-Dhttp.proxyHost=proxy.yourcompany.com -Dhttp.proxyPort=8080"

# Use local Maven installation
export MAVEN_HOME=/path/to/local/maven
./mvnw clean install
```

**Security Warning**: Replace example hostnames with your actual proxy server configuration.

## Best Practices

### Security Best Practices
- **Never use untrusted repository URLs** for distributionUrl/wrapperUrl
- **Pre-download and verify binaries** in controlled environments before runtime use
- **Use corporate repository mirrors** with strict access controls
- **Verify GPG signatures** of downloaded distributions when possible
- **Implement CI/CD verification** before wrapper execution in production
- **Monitor wrapper downloads** in enterprise environments
- **Use offline mode** with pre-verified distributions when possible
- **Audit wrapper cache** regularly for unexpected artifacts
- **Implement network-level controls** (DNSSEC, SSL inspection, allowlisting)
- **Document security procedures** for wrapper verification in your organization

### General Best Practices
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