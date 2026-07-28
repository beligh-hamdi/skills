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
        <url>https://corporate-mirror.com/maven2</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
    
    <mirror>
        <id>all-mirror</id>
        <name>Corporate All Mirror</name>
        <url>https://corporate-mirror.com/maven2</url>
        <mirrorOf>*</mirrorOf>
    </mirror>
</mirrors>
```

### Advanced Mirror Patterns
```xml
<!-- Mirror specific repositories -->
<mirror>
    <id>spring-mirror</id>
    <url>https://corporate-mirror.com/spring</url>
    <mirrorOf>spring-*</mirrorOf>
</mirror>

<!-- Mirror everything except local -->
<mirror>
    <id>external-mirror</id>
    <url>https://corporate-mirror.com/external</url>
    <mirrorOf>*,!local</mirrorOf>
</mirror>

<!-- Multiple repository mirror -->
<mirror>
    <id>multi-mirror</id>
    <url>https://corporate-mirror.com/maven2</url>
    <mirrorOf>repo1,repo2,repo3</mirrorOf>
</mirror>
```

## Authentication

### Server Authentication
```xml
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
```

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
<proxies>
    <proxy>
        <id>http-proxy</id>
        <active>true</active>
        <protocol>http</protocol>
        <host>proxy.example.com</host>
        <port>8080</port>
        <username>proxyuser</username>
        <password>proxypass</password>
        <nonProxyHosts>localhost|127.0.0.1</nonProxyHosts>
    </proxy>
</proxies>
```

### HTTPS Proxy
```xml
<proxies>
    <proxy>
        <id>https-proxy</id>
        <active>true</active>
        <protocol>https</protocol>
        <host>proxy.example.com</host>
        <port>8443</port>
        <username>proxyuser</username>
        <password>proxypass</password>
    </proxy>
</proxies>
```

## Repository Plugins

### Deployment Configuration
```xml
<distributionManagement>
    <repository>
        <id>releases-repo</id>
        <name>Releases Repository</name>
        <url>https://corporate-repo.com/releases</url>
    </repository>
    <snapshotRepository>
        <id>snapshots-repo</id>
        <name>Snapshots Repository</name>
        <url>https://corporate-repo.com/snapshots</url>
    </snapshotRepository>
</distributionManagement>
```

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
        <url>https://nexus.example.com/repository/maven-releases</url>
    </repository>
    <repository>
        <id>nexus-snapshots</id>
        <name>Nexus Snapshots</name>
        <url>https://nexus.example.com/repository/maven-snapshots</url>
    </repository>
</repositories>
```

### Artifactory Configuration
```xml
<repositories>
    <repository>
        <id>artifactory-releases</id>
        <name>Artifactory Releases</name>
        <url>https://artifactory.example.com/artifactory/libs-release-local</url>
    </repository>
    <repository>
        <id>artifactory-snapshots</id>
        <name>Artifactory Snapshots</name>
        <url>https://artifactory.example.com/artifactory/libs-snapshot-local</url>
    </repository>
</repositories>
```

## Repository Best Practices

### Repository Ordering
```xml
<repositories>
    <!-- Most specific first -->
    <repository>
        <id>corporate-internal</id>
        <url>https://corporate-repo.com/internal</url>
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

### Snapshot Policies
```xml
<repository>
    <id>snapshots-repo</id>
    <url>https://repo.example.com/snapshots</url>
    <snapshots>
        <enabled>true</enabled>
        <updatePolicy>daily</updatePolicy>
        <checksumPolicy>warn</checksumPolicy>
    </snapshots>
</repository>
```

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
    <url>https://repo.example.com/maven2</url>
    <releases>
        <checksumPolicy>fail</checksumPolicy>
    </releases>
    <snapshots>
        <checksumPolicy>fail</checksumPolicy>
    </snapshots>
</repository>
```

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