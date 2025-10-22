
# MariaDB and phpMyAdmin <span style="color: #409EFF; font-size: 0.6em; font-style: italic;"> -  Docker Setup & Usage Guide</span>


This guide explains how to set up and access a MariaDB database with phpMyAdmin using Docker Compose.
It also describes how to connect other containers or external applications to the same database service.

## Quick Setup

1. **Make sure the external network exists (or create it)**

   ```bash
   docker network create --subnet=172.40.0.0/24 dev1-net
   ```

2. **Create and start the container**

   ```bash
   docker compose -f compose.yaml up -d --build --force-recreate --remove-orphans
   ```

3. Verify Network

- Verify that both containers are on the same network: ``docker network inspect dev1-net``  
  Also the other containers in the network will be shown including details like Ip address
- Check the IP address inside a container: ``hostname -I``

---
<br>

## Usage

### Default Credentials

- **Database:** `docker`
- **Username:** `docker`
- **Password:** `docker`
- **Port:** `3306` (internal), `9004` (external)

### Access phpMyAdmin

Access phpMyAdmin from the host system at:

```
http://localhost:9001/
# Login using the default credentials above.
```

---

### Access the Database from a Another Container

When connecting other Docker containers to this MariaDB instance, configure your compose file with these environment variables:

- The **service name** from the current compose file as the **host environment variable** (`DB_HOST`)
- The **internal port** (`3306`) as the **port environment variable** (`DB_PORT`)
- The **`MARIADB_USER`** value as the **user environment variable** (`DB_USER`)
- The **`MARIADB_PASSWORD`** value as the **password environment variable** (`DB_PASSWORD`)
- The **`MARIADB_DATABASE`** value as the **database name environment variable** (`DB_NAME`)

#### Example: Web Application Compose File

```yaml
webapp:
  build: ./webapp
  depends_on:
    - mariadb
  environment:
    DB_HOST: mariadb
    DB_PORT: 3306        # Use the internal port (second value)
    DB_USER: docker
    DB_PASSWORD: docker
    DB_NAME: docker
```

---

### Access via DNS Name (from within Docker)

```php
$dsn = 'mysql:host=mariadb;port=3306;dbname=docker;charset=utf8mb4';
$db = new PDO($dsn, 'docker', 'docker');  // first 'docker' = user, second = password
```

---

### Access from Host or External Machine

When connecting from the host system or another machine on the network:

```env
DB_HOST=127.0.0.1   # Use host's IP address for external machines
DB_PORT=9004        # Use the external port (first value in ports mapping)
DB_USER=docker
DB_PASSWORD=docker
DB_NAME=docker
```
