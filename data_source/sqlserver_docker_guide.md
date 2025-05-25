# Running SQL Server 2019 on macOS using Docker
This guide explains how to install Docker Desktop on macOS and run Microsoft SQL Server 2019 in a Docker container.  

Note that you may experience trouble on an Apple Silcon baed mac. This has to do with chip simulation incompatibilities between macOS and the Linux OS being emulated. I was able to over come this by including a "platform" parameter as part of the docker run command. The examples below show both versions of the docer run command statement.  

Although not directly related, it may be generally a good idea to go ahead and install the Rosetta app on your mac.

## Step 1: Install Docker Desktop on macOS

1. **Download Docker Desktop:**
   - Visit: [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
   - Choose the correct version for your system:
     - **Apple Silicon (M1/M2/M3/M4)**: Download for Apple Chip
     - **Intel Macs**: Download for Intel Chip

2. **Install Docker Desktop:**
   - Open the `.dmg` file and drag **Docker** into your **Applications** folder.
   - Launch **Docker Desktop** and follow the setup instructions.

3. **Verify Installation:**
   ```sh
   docker --version
   ```

4. **Ensure Docker is Running:**
   - Click the **Docker whale icon** in the menu bar and confirm it's active.

## Step 2: Download and Run SQL Server 2019

1. **Pull the SQL Server 2019 Docker Image:**
   ```sh
   docker pull mcr.microsoft.com/mssql/server:2019-latest
   ```

2. **Run SQL Server in a Container:**  
   (without "platform" parameter)
   ```text
   docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=YourStrong!Passw0rd' \
   -p 1433:1433 --name sqlserver2019 \
   -d mcr.microsoft.com/mssql/server:2019-latest
   ```

   (with "platform" parameter)
   ```text
   docker run --platform linux/amd64 -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=MyS3cur3P@ssw0rd' \
   -p 1433:1433 --name sqlserver2019 \
   -d mcr.microsoft.com/mssql/server:2019-latest
   ```

   - Replace `YourStrong!Passw0rd` with a strong password that meets SQL Server requirements.
   - `-p 1433:1433` maps the container port to your local machine.

3. **Check that the Container is Running:**
   ```sh
   docker ps
   ```

## Step 3: Connect to SQL Server

Use one of the following methods:

### Option A: Azure Data Studio (Recommended for macOS)
1. Download and install: [Azure Data Studio](https://learn.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio)
2. Open the application and create a new connection:
   - **Server:** `localhost,1433`
   - **Authentication:** SQL Login
   - **Username:** `sa`
   - **Password:** Your container password

### Option B: Command Line using `sqlcmd`
```sh
docker exec -it sqlserver2019 /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P 'YourStrong!Passw0rd'
```

## Step 4: Managing the Container
### Stop the Container:
```sh
docker stop sqlserver2019
```

### Start the Container:
```sh
docker start sqlserver2019
```

### Remove the Container (if needed):
```sh
docker rm -f sqlserver2019
```

## Optional: Restore a Database

1. Copy the `.bak` file into the container:
   ```sh
   docker cp mydatabase.bak sqlserver2019:/var/opt/mssql/backup/
   ```

2. Restore the database using `sqlcmd` or a GUI tool.

---

You're all set to run SQL Server 2019 on macOS using Docker! 🎉
