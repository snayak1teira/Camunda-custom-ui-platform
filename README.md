# Camunda Custom UI Platform

This guide provides step-by-step instructions to build Camunda 7 from source, including the engine, REST API, and web applications.

## Overview

Camunda 7 follows a modular architecture that needs to be built in sequence:

```
camunda-engine → camunda-engine-rest-root → camunda-webapp-root → camunda-webapp-webjar → camunda-tomcat
```

## Repository Structure

The official Camunda 7 source is available at: https://github.com/camunda/camunda-bpm-platform

| Module | Purpose |
|--------|---------|
| `engine` | Core BPM engine |
| `engine-rest` | REST API for the engine |
| `webapps` | UI components (Cockpit, Tasklist, Admin) |
| `distro/tomcat` | Tomcat-based distribution assembly |
| `distro/springboot` | Spring Boot-based distribution (since 7.15) |

## Build Process

### Step 1: Build All Modules

Build the entire project to ensure all dependencies are available:

```bash
mvn clean install -DskipTests
```

> **Note**: This step is crucial as it installs all required artifacts into the local Maven repository.

### Step 2: Build Tomcat Distribution

Navigate to the Tomcat distribution directory and build:

```bash
cd distro/tomcat
mvn clean package -Pdist
```

This creates:
- `camunda-webapp-tomcat-7.x.x.war` - Deployable WAR file
- `camunda-bpm-tomcat-7.x.x.zip` - Complete distribution package

## Deployment Options

### Option 1: Deploy WAR to Existing Tomcat

Deploy the generated WAR file (`target/camunda-webapp-tomcat-*.war`) to your existing Tomcat server.

### Option 2: Use Packaged Distribution

1. Extract the distribution ZIP file:
   ```bash
   cd distro/tomcat/target/
   unzip camunda-bpm-tomcat-*.zip
   ```

2. Configure the server (if needed):
   - Edit `server/apache-tomcat-*/conf/server.xml` to change ports
   - Add database drivers to `server/apache-tomcat-*/lib/` for PostgreSQL or MySQL
   - **OR**
   - Modify the changes in `camunda-tomcat` → `camunda-tomcat-assembly-pom.xml` add the PostgreSQL or MySQL dependency with include it in the `assembly.xml` file.
   - Example: `<include>org.postgresql:postgresql:jar</include>`

3. Start the server:
   ```bash
   # Linux/macOS
   ./server/apache-tomcat-*/bin/startup.sh
   
   # Windows
   ./server/apache-tomcat-*/bin/startup.bat
   ```

## Access Points

Once deployed, access the following URLs:

| Service | URL | Description |
|---------|-----|-------------|
| **Cockpit**  | http://localhost:8090/camunda/app/cockpit/ | Process monitoring and management |
| **Tasklist** | http://localhost:8090/camunda/app/tasklist/ | Task management interface |
| **Admin**    | http://localhost:8090/camunda/app/admin/ | User and system administration |
| **REST API** | http://localhost:8090/engine-rest/ | RESTful API endpoint |

## Default Credentials

```
Username: demo
Password: demo
```

## Database Configuration

### For PostgreSQL or MySQL:

1. Edit `server/apache-tomcat-*/conf/server.xml` to change DB Configuration

**OR**: Manually we can change:

1. Download the appropriate JDBC driver JAR
2. Place it in the `server/apache-tomcat-*/lib/` directory
3. Update database configuration in the application

## Build Artifacts

After successful build, you'll find:

```
distro/tomcat/target/
├── camunda-bpm-tomcat-*.zip          # Full distribution
├── camunda-webapp-tomcat-*.war       # Deployable WAR
└── camunda-bpm-tomcat-*/             # Extracted distribution
```

## Component Integration

The build process integrates these components:

| Component | Source Path | Included in WAR |
|-----------|-------------|-----------------|
| Engine | `engine/` | ✅ |
| REST API | `engine-rest/` | ✅ |
| Web Applications | `webapps/camunda-webapp-webjar/` | ✅ |
| Distribution Assembly | `distro/tomcat/` | ✅ |

## Customization Options

You can customize the build by modifying:

- **BPMN files**: Add `.bpmn` files to `webapps/camunda-webapp-webjar/src/main/resources/`
- **REST endpoints**: Modify `engine-rest/` module
- **UI styles**: Edit `webapps/camunda-webapp-webjar/` module

## Troubleshooting

### Common Issues:

1. **Build fails**: Ensure you run `mvn clean install` from the root directory first
2. **Missing dependencies**: The `webapps/` module depends on `engine` and `engine-rest` - don't build it in isolation
3. **Database connection**: Verify JDBC drivers are in the correct `lib/` directory


## Summary

1. **Clone** the repository
2. **Build all modules** with `mvn clean install -DskipTests`
3. **Package distribution** with `mvn clean package -Pdist` in `distro/tomcat/`
4. **Deploy** WAR or run the packaged distribution
5. **Access** applications at http://localhost:8090/camunda/app/welcome/

This approach ensures you have a complete Camunda 7 installation with all components properly integrated.

---

## Quick Start Commands

```bash
# Clone and build
git clone https://github.com/camunda/camunda-bpm-platform.git
cd camunda-bpm-platform
mvn clean install -DskipTests

# Build distribution
cd distro/tomcat
mvn clean package -Pdist

# Extract and run
cd target
unzip camunda-bpm-tomcat-*.zip
./server/apache-tomcat-*/bin/startup.sh
```

## License

This project follows the Camunda 7 licensing terms. Please refer to the official Camunda documentation for license details.