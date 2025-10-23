---
layout: default_c
RefPages:
--- 

<br>

# MariaDB and phpMyAdmin <span style="color: #409EFF; font-size: 0.6em; font-style: italic;"> -  Docker Setup & Usage Guide</span>


This guide explains how to set up and access a MariaDB database with phpMyAdmin using Docker Compose.
It also describes how to connect other containers or external applications to the same database service.

<details>  
  <summary class="clickable-summary">
  <span  class="summary-icon"></span> <!-- Square Symbol -->
  <b>What's in my network</b>
  </summary>
It can be useful to know what containe, IP4 addresses and ports are used in a network
For this I have a script that displays the information for you. it can be found in my **Powershelll-Utilities** repository [**here**](https://github.com/NicoJanE/Powershell-Utilities). Use the `docker-netw-info` directory to execute the scrip
</details>


## Quick Setup

1. **Make sure the external network exists (or create it)**

<pre class="nje-cmd-one-line"> docker network create --subnet=172.40.0.0/24 dev1-net </pre>

2. **Create and start the container**

<pre class="nje-cmd-one-line"> docker compose -f compose.yaml up -d --build --force-recreate --remove-orphans </pre>

3. Verify Network

- Verify that both containers are on the same network: ``docker network inspect dev1-net``  
  Also the other containers in the network will be shown including details like Ip address
- Check the IP address inside a container: ``hostname -I``

---

## Usage

### Default Credentials

- **Database:** `docker`
- **Username:** `docker`
- **Password:** `docker`
- **Port:** `3306` (internal), `9004` (external)

### Access phpMyAdmin

Access phpMyAdmin from the host system at:

<pre class="nje-cmd-multi-line"> 
http://localhost:9001/
# Login using the default credentials above.
</pre>

---

### Access the Database from a Another Container

When connecting other Docker containers to this MariaDB instance, configure your compose file with these environment variables:

- The **service name** from the current compose file as the **host environment variable** (`DB_HOST`)
- The **internal port** (`3306`) as the **port environment variable** (`DB_PORT`)
- The **`MARIADB_USER`** value as the **user environment variable** (`DB_USER`)
- The **`MARIADB_PASSWORD`** value as the **password environment variable** (`DB_PASSWORD`)
- The **`MARIADB_DATABASE`** value as the **database name environment variable** (`DB_NAME`)

#### Example: Web Application Compose File

<pre class="nje-cmd-multi-line"> 
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
</pre>

---

### Access via DNS Name (from within Docker)

<pre class="nje-cmd-multi-line"> 
$dsn = 'mysql:host=mariadb;port=3306;dbname=docker;charset=utf8mb4';
$db = new PDO($dsn, 'docker', 'docker');  // first 'docker' = user, second = password
</pre>

---

### Access from Host or External Machine

When connecting from the host system or another machine on the network:

<pre class="nje-cmd-multi-line"> 
DB_HOST=127.0.0.1   # Use host's IP address for external machines
DB_PORT=9004        # Use the external port (first value in ports mapping)
DB_USER=docker
DB_PASSWORD=docker
DB_NAME=docker
</pre>
