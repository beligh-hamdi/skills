# Maven Profiles and Lifecycle

## Build Lifecycle Phases
Maven has three built-in lifecycles:

### Default Lifecycle
1. **validate** - Validate project structure
2. **compile** - Compile source code
3. **test** - Run unit tests
4. **package** - Package compiled code
5. **verify** - Run integration tests
6. **install** - Install to local repository
7. **deploy** - Deploy to remote repository

### Clean Lifecycle
1. **pre-clean** - Execute before cleaning
2. **clean** - Remove build files
3. **post-clean** - Execute after cleaning

### Site Lifecycle
1. **pre-site** - Execute before site generation
2. **site** - Generate project site
3. **post-site** - Execute after site generation
4. **site-deploy** - Deploy site to server

## Profile Configuration
Define profiles for different environments:

```xml
<profiles>
    <profile>
        <id>development</id>
        <properties>
            <environment>dev</environment>
            <database.url>jdbc:h2:mem:devdb</database.url>
        </properties>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    
    <profile>
        <id>production</id>
        <properties>
            <environment>prod</environment>
            <database.url>jdbc:postgresql://prod-db:5432/appdb</database.url>
        </properties>
    </profile>
    
    <profile>
        <id>ci</id>
        <properties>
            <skipTests>false</skipTests>
        </properties>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.jacoco</groupId>
                    <artifactId>jacoco-maven-plugin</artifactId>
                </plugin>
            </plugins>
        </build>
    </profile>
</profiles>
```

## Profile Activation
Activate profiles by various conditions:

```xml
<profile>
    <id>jdk-17</id>
    <activation>
        <jdk>17</jdk>
    </activation>
</profile>

<profile>
    <id>os-windows</id>
    <activation>
        <os>
            <family>windows</family>
        </os>
    </activation>
</profile>

<profile>
    <id>property-based</id>
    <activation>
        <property>
            <name>environment</name>
            <value>production</value>
        </property>
    </activation>
</profile>

<profile>
    <id>file-based</id>
    <activation>
        <file>
            <exists>src/main/resources/production.properties</exists>
        </file>
    </activation>
</profile>
```

## Using Profiles
```bash
# Activate specific profile
mvn install -Pproduction

# Activate multiple profiles
mvn install -Pdevelopment,ci

# Activate profile by property
mvn install -Denvironment=production

# List active profiles
mvn help:active-profiles

# List all profiles
mvn help:all-profiles
```

## Custom Lifecycle Phases
Define custom phases if needed:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.example</groupId>
            <artifactId>custom-plugin</artifactId>
            <executions>
                <execution>
                    <phase>pre-integration-test</phase>
                    <goals>
                        <goal>custom-goal</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

## Phase Skipping
Skip specific lifecycle phases:

```bash
# Skip tests
mvn install -DskipTests

# Skip test compilation
mvn install -Dmaven.test.skip=true

# Skip specific plugin
mvn install -Dmaven.javadoc.skip=true
```

## Common Profile Patterns

### Development Profile
```xml
<profile>
    <id>dev</id>
    <properties>
        <spring.profiles.active>dev</spring.profiles.active>
    </properties>
    <dependencies>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
    </dependencies>
</profile>
```

### Production Profile
```xml
<profile>
    <id>prod</id>
    <properties>
        <spring.profiles.active>prod</spring.profiles.active>
    </properties>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <executable>true</executable>
                </configuration>
            </plugin>
        </plugins>
    </build>
</profile>
```

## Useful Commands
```bash
# Run specific phase
mvn compile

# Run up to specific phase
mvn package

# Skip phases
mvn install -Dmaven.main.skip=false

# Show phase details
mvn help:describe -Dcmd=install

# Display lifecycle mapping
mvn help:describe -Dcmd=compile -Ddetail
```