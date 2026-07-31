# Maven Repository Management

## Purpose
Maven repository management involves configuring repositories, mirrors, authentication, and deployment settings for efficient dependency resolution and artifact publishing.

## Repository Types

### Local Repository
```xml
<settings>
    <localRepository>/path/to/local/repo</localRepository>
</settings>
```

### Remote Repositories
```xml
<repositories>
    <repository>
        <id>central</id>
        <name>Maven Central Repository</name>
        <url>https://repo.maven.apache.org/maven2</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
    
    <repository>
        <id>spring-milestones</id>
        <name>Spring Milestones</name>
        <url>https://repo.spring.io/milestone</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

### Snapshot Repositories
```xml
<repositories>
    <repository>
        <id>spring-snapshots</id>
        <name>Spring Snapshots</name>
        <url>https://repo.spring.io/snapshot</url>
        <releases>
            <enabled>false</enabled>
        </releases>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </repository>
</repositories>
```

## Repository Mirrors

### Mirror Configuration
```xml
<mirrors>
    <mirror>
        <id>central-mirror</id>
        <name>Corporate Central Mirror</name>
        <url>https://repo.maven.apache.org/maven2</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
    
    <mirror>
        <id>all-mirror</id>
        <name>Corporate All Mirror</name>
        <url>https://repo.maven.apache.org/maven2</url>
        <mirrorOf>*</mirrorOf>
    </mirror>
</mirrors>
```

**Security Warning**: Only use mirrors from trusted sources within your organization. Replace example URLs with your actual corporate mirror URLs.

### Advanced Mirror Patterns
```xml
<!-- Mirror specific repositories -->
<mirror>
    <id>spring-mirror</id>
    <url>https://repo.spring.io/milestone</url>
    <mirrorOf>spring-*</mirrorOf>
</mirror>

<!-- Mirror everything except local -->
<mirror>
    <id>external-mirror</id>
    <url>https://repo.maven.apache.org/maven2</url>
    <mirrorOf>*,!local</mirrorOf>
</mirror>

<!-- Multiple repository mirror -->
<mirror>
    <id>multi-mirror</id>
    <url>https://repo.maven.apache.org/maven2</url>
    <mirrorOf>repo1,repo2,repo3</mirrorOf>
</mirror>
```

**Security Warning**: Replace example URLs with your actual corporate mirror URLs. Ensure all mirrors are trusted and verified.

## Authentication

### Server Authentication
```xml
<!-- INSECURE - Do not hardcode credentials -->
<servers>
    <server>
        <id>private-repo</id>
        <username>username</username>
        <password>password</password>
    </server>
    
    <server>
        <id>deploy-repo</id>
        <username>deploy-user</username>
        <password>deploy-password</password>
        <privateKey>/path/to/private/key</privateKey>
        <passphrase>key-passphrase</passphrase>
    </server>
</servers>

<!-- SECURE - Use environment variables or Maven settings -->
<servers>
    <server>
        <id>private-repo</id>
        <username>${env.REPO_USERNAME}</username>
        <password>${env.REPO_PASSWORD}</password>
    </server>
    
    <server>
        <id>deploy-repo</id>
        <username>${env.DEPLOY_USERNAME}</username>
        <password>${env.DEPLOY_PASSWORD}</password>
        <privateKey>${env.DEPLOY_PRIVATE_KEY}</privateKey>
        <passphrase>${env.DEPLOY_PASSPHRASE}</passphrase>
    </server>
</servers>
```

**Security Warning**: Never hardcode credentials. Use environment variables or Maven's encrypted password mechanism.

### Encrypted Passwords
```bash
# Create master password
mvn --encrypt-master-password

# Encrypt server password
mvn --encrypt-password

# Use encrypted passwords in settings.xml
<servers>
    <server>
        <id>private-repo</id>
        <username>username</username>
        <password>{encrypted-password}</password>
    </server>
</servers>
```

## Proxy Configuration

### HTTP Proxy
```xml
<!-- INSECURE - Do not hardcode credentials -->
<proxies>
    <proxy>
        <id>http-proxy</id>
        <active>true</active>
        <protocol>http</protocol>
        <host>proxy.yourcompany.com</host>
        <port>8080</port>
        <username>proxyuser</username>
        <password>proxypass</password>
        <nonProxyHosts>localhost|127.0.0.1</nonProxyHosts>
    </proxy>
</proxies>

<!-- SECURE - Use environment variables -->
<proxies>
    <proxy>
        <id>http-proxy</id>
        <active>true</active>
        <protocol>http</protocol>
        <host>${env.PROXY_HOST}</host>
        <port>${env.PROXY_PORT}</port>
        <username>${env.PROXY_USERNAME}</username>
        <password>${env.PROXY_PASSWORD}</password>
        <nonProxyHosts>localhost|127.0.0.1</nonProxyHosts>
    </proxy>
</proxies>
```

**Security Warning**: Replace example hostnames with your actual proxy server. Use environment variables for credentials.

### HTTPS Proxy
```xml
<!-- INSECURE - Do not hardcode credentials -->
<proxies>
    <proxy>
        <id>https-proxy</id>
        <active>true</active>
        <protocol>https</protocol>
        <host>proxy.yourcompany.com</host>
        <port>8443</port>
        <username>proxyuser</username>
        <password>proxypass</password>
    </proxy>
</proxies>

<!-- SECURE - Use environment variables -->
<proxies>
    <proxy>
        <id>https-proxy</id>
        <active>true</active>
        <protocol>https</protocol>
        <host>${env.HTTPS_PROXY_HOST}</host>
        <port>${env.HTTPS_PROXY_PORT}</port>
        <username>${env.HTTPS_PROXY_USERNAME}</username>
        <password>${env.HTTPS_PROXY_PASSWORD}</password>
    </proxy>
</proxies>
```

**Security Warning**: Replace example hostnames with your actual proxy server. Use environment variables for credentials.

## Repository Plugins

### Deployment Configuration
```xml
<distributionManagement>
    <repository>
        <id>releases-repo</id>
        <name>Releases Repository</name>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
    <snapshotRepository>
        <id>snapshots-repo</id>
        <name>Snapshots Repository</name>
        <url>https://repo.maven.apache.org/maven2</url>
    </snapshotRepository>
</distributionManagement>
```

**Security Warning**: Replace with your actual corporate repository URLs. Ensure proper authentication and security measures are in place.

### Site Deployment
```xml
<distributionManagement>
    <site>
        <id>site-repo</id>
        <name>Project Site</name>
        <url>scp://host/path/to/site</url>
    </site>
</distributionManagement>
```

## Repository Managers

### Nexus Configuration
```xml
<repositories>
    <repository>
        <id>nexus-releases</id>
        <name>Nexus Releases</name>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
    <repository>
        <id>nexus-snapshots</id>
        <name>Nexus Snapshots</name>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
</repositories>
```

**Security Warning**: Replace with your actual Nexus repository URLs. Ensure proper authentication and security measures are in place.

### Artifactory Configuration
```xml
<repositories>
    <repository>
        <id>artifactory-releases</id>
        <name>Artifactory Releases</name>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
    <repository>
        <id>artifactory-snapshots</id>
        <name>Artifactory Snapshots</name>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
</repositories>
```

**Security Warning**: Replace with your actual Artifactory repository URLs. Ensure proper authentication and security measures are in place.

## Repository Best Practices

### Repository Ordering
```xml
<repositories>
    <!-- Most specific first -->
    <repository>
        <id>corporate-internal</id>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
    
    <!-- Then specific third-party -->
    <repository>
        <id>spring-releases</id>
        <url>https://repo.spring.io/release</url>
    </repository>
    
    <!-- Finally central -->
    <repository>
        <id>central</id>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
</repositories>
```

**Security Warning**: Replace example URLs with your actual corporate repository URLs. Ensure proper authentication and security measures are in place.

### Snapshot Policies
```xml
<repository>
    <id>snapshots-repo</id>
    <url>https://repo.maven.apache.org/maven2</url>
    <snapshots>
        <enabled>true</enabled>
        <updatePolicy>daily</updatePolicy>
        <checksumPolicy>warn</checksumPolicy>
    </snapshots>
</repository>
```

**Security Warning**: Replace with your actual snapshot repository URL. Ensure proper authentication and security measures are in place.

### Update Policies
```xml
<snapshots>
    <updatePolicy>always</updatePolicy> <!-- Always check for updates -->
    <updatePolicy>daily</updatePolicy>  <!-- Check once per day (default) -->
    <updatePolicy>interval:60</updatePolicy> <!-- Check every 60 minutes -->
    <updatePolicy>never</updatePolicy>   <!-- Never check for updates -->
</snapshots>
```

## Troubleshooting

### Repository Connectivity
```bash
# Test repository connectivity
mvn dependency:list -DremoteRepositories=repo-url

# Enable debug output
mvn dependency:resolve -X

# Check repository configuration
mvn help:effective-settings | grep repositories
```

### Authentication Issues
```bash
# Test authentication
mvn deploy -DrepositoryId=repo-id

# Check server configuration
mvn help:effective-settings | grep servers

# Verify credentials
mvn help:effective-settings | grep -A 5 repo-id
```

### Mirror Issues
```bash
# Check mirror configuration
mvn help:effective-settings | grep mirrors

# Test without mirrors
mvn dependency:resolve -Dmaven.repo.local=/tmp/repo

# Verify mirror selection
mvn dependency:resolve -X | grep mirror
```

## Security Considerations

### HTTPS Configuration
```xml
<repository>
    <id>secure-repo</id>
    <url>https://secure-repo.com/maven2</url>
    <releases>
        <enabled>true</enabled>
        <checksumPolicy>fail</checksumPolicy>
    </releases>
</repository>
```

### Checksum Validation
```xml
<repository>
    <id>strict-repo</id>
    <url>https://repo.maven.apache.org/maven2</url>
    <releases>
        <checksumPolicy>fail</checksumPolicy>
    </releases>
    <snapshots>
        <checksumPolicy>fail</checksumPolicy>
    </snapshots>
</repository>
```

**Security Warning**: Replace example URLs with your actual corporate repository URLs. Ensure proper authentication and security measures are in place.

### Repository Signing
```bash
# Enable GPG signature verification
export MAVEN_OPTS="-Dgpg.skip=false"

# Verify signatures
mvn verify -Dgpg.verify=true
```

## Useful Commands
```bash
# List repositories
mvn help:effective-settings | grep repositories

# Test repository
mvn dependency:get -Dartifact=group:artifact:version -DremoteRepositories=repo-url

# Deploy to repository
mvn deploy

# Deploy to specific repository
mvn deploy -DrepositoryId=repo-id

# List remote repositories
mvn repository:list

# Check repository connectivity
mvn dependency:list -DremoteRepositories=repo-url

# Clear local repository
rm -rf ~/.m2/repository

# Force update from repositories
mvn clean install -U
```