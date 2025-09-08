# TeamCity Docker Deployment

This repository provides a Docker Compose setup for running a local TeamCity server with a custom build agent.

---

## Server settings
- **Image:** `jetbrains/teamcity-server:2025.07.1`
- **Port:** `8111:8111`
- **Environment:**
    - `TEAMCITY_SERVER_MEM_OPTS=-Xms1g -Xmx2g`
    - `TZ=Europe/Berlin`
    - `JAVA_OPTS=-Dfile.encoding=UTF-8`
- **Volumes:**
    - `./data/server:/data/teamcity_server/datadir`
    - `./logs/server:/opt/teamcity/logs`

---

## Agent settings
- **Service name:** `teamcity-agent-2-local` 
- **Base image:** `jetbrains/teamcity-minimal-agent:2025.07.1-linux`
- **Custom Dockerfile:** builds a pinned toolchain (Maven, JDK, etc.) for reproducible builds.
- As per the docs reffered which mentions the use of pinned versions for reproduable builds
  https://reproducible-builds.org/docs/perimeter/



## Build the custom agent
```bash
docker compose build teamcity-agent-1-local
```

## Start the stack
```bash
docker compose up -d
```

Access TeamCity at: http://localhost:8111




