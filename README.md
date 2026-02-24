# Oracle SQL Developer Setup on Pop!_OS with Docker Oracle 23c

This guide documents the complete setup of:

* **Oracle Database running inside Docker**
* **Oracle SQL Developer installation on Pop!_OS**
* Password reset inside container
* RPM installation using `alien`

Database used:
**Oracle Database 23c Free**

Client tool used:
**Oracle SQL Developer**

---

# Environment Overview

* OS: Pop!_OS (Ubuntu-based)
* Database: Oracle 23c Free (Docker container)
* Exposed Ports:

  * `1521` → SQL Listener
  * `5500` → Enterprise Manager

Check running container:

```bash
docker ps
```

Expected:

```
oracle-apex-db   Up (healthy)
```

---

# Installing SQL Developer

## Option A (Recommended – Native DEB)

If you have the `.deb` file:

```bash
sudo apt install ./sqldeveloper_24.3.1-xxx_all.deb
```

If dependencies fail:

```bash
sudo apt --fix-broken install
```

---

## Option B (Using alien for RPM)

If you only have the `.rpm` file, convert it:

```bash
sudo alien -k *.rpm
```

This generates a `.deb` file which can then be installed using `dpkg` or `apt`.

---

# Install Java (Required)

SQL Developer 24.x requires Java 17+

```bash
sudo apt install openjdk-17-jdk
java -version
```

---

# Resetting Oracle Password Inside Docker

Enter the container:

```bash
docker exec -it oracle-apex-db bash
```

Connect internally:

```bash
sqlplus / as sysdba
```

---

## Switch to Root Container

```sql
ALTER SESSION SET CONTAINER=CDB$ROOT;
```

Reset SYS and SYSTEM:

```sql
ALTER USER SYS IDENTIFIED BY NewPassword123 CONTAINER=ALL;
ALTER USER SYSTEM IDENTIFIED BY NewPassword123 CONTAINER=ALL;
```

---

## Reset PDBADMIN (Inside PDB)

```sql
ALTER SESSION SET CONTAINER=FREEPDB1;
ALTER USER PDBADMIN IDENTIFIED BY NewPassword123;
```

Exit:

```sql
EXIT;
```

---

# Connecting via SQL Developer

Create a new connection with:

| Field        | Value          |
| ------------ | -------------- |
| Host         | localhost      |
| Port         | 1521           |
| Service Name | FREEPDB1       |
| Username     | sys            |
| Role         | SYSDBA         |
| Password     | NewPassword123 |

---

# Enterprise Manager (Optional)

Access via browser:

```
https://localhost:5500/em
```

Login as:

* Username: `sys`
* Role: `SYSDBA`

---

# Architecture Note

Oracle 23c Free runs as:

* **CDB (Container Database)**
* With one PDB: `FREEPDB1`

Common users like `SYS` must be altered using:

```
CONTAINER=ALL
```

---

# Final Result

You now have:

* Oracle 23c running in Docker
* SQL Developer installed on Pop!_OS
* Working connection to `FREEPDB1`
* Reset administrator credentials
* Enterprise Manager accessible


