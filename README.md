# Docker SSH Communication Setup

This project sets up two Docker containers using the Ubuntu image:
- One container runs an OpenSSH server.
- The other container runs an OpenSSH client.

The two containers are connected via SSH to demonstrate container-to-container communication.

## Prerequisites
- Docker installed on your system
- Basic knowledge of Docker commands

## Setup Instructions

### Step 1: Build and Run the Containers
Assuming you already have `Dockerfile.server`, `Dockerfile.client`, and `docker-compose.yml` set up, you can start by building and running the containers:
```sh
docker-compose up -d --build
```

### Step 2: Configure and Connect to the Server
1. Start the server container:
   ```sh
   docker start ssh_server
   ```
2. Access the server container:
   ```sh
   docker exec -it ssh_server bash
   ```
3. Verify the root account entry in `/etc/shadow`:
   ```sh
   cat /etc/shadow | grep root
   ```
4. Change the root password:
   ```sh
   passwd root
   ```
   - Enter `1234` as the new password.
   - Exit the container.
   ```sh
   exit
   ```
5. Retrieve the server container's IP address:
   ```sh
   docker inspect ssh_server | grep IPAddress
   ```
   - Copy the displayed IP address for use in the next step.

### Step 3: Connect from the Client to the Server
1. Start the client container:
   ```sh
   docker start ssh_client
   ```
2. Access the client container:
   ```sh
   docker exec -it ssh_client bash
   ```
3. Attempt to SSH into the server using the copied IP address:
   ```sh
   ssh root@<IP_ADDRESS>
   ```
   - If SSH fails, it means the SSH service is not running in the server container. Exit the client container:
     ```sh
     exit
     ```
4. Restart the SSH service in the server container:
   ```sh
   docker start ssh_server
   docker exec -it ssh_server bash
   service ssh start
   service --status-all | grep ssh  # Verify that SSH is running
   exit
   ```
5. Re-enter the client container and attempt SSH again:
   ```sh
   docker exec -it ssh_client bash
   ssh root@<IP_ADDRESS>
   ```
   - If prompted, type `yes` to accept the fingerprint.
   - Use the password `1234` (as set in Step 2).

## Cleanup
To stop and remove the containers:
```sh
docker-compose down
```

## Conclusion
This project demonstrates how to set up SSH communication between two Docker containers using OpenSSH. It can be expanded to include more security configurations like SSH key authentication.

---

For improvements, consider using **non-root users** and **SSH key-based authentication** for better security.

