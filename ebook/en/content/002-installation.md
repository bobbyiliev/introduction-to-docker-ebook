# Chapter 2: Installing Docker

Installing Docker is the first step in your journey with containerization. This chapter will guide you through the process of installing Docker on various operating systems, troubleshooting common issues, and verifying your installation.

## Docker Editions

Before we begin, it's important to understand the different Docker editions available:

1. **Docker Engine - Community**: Free, open-source Docker platform suitable for developers and small teams.
2. **Docker Engine - Enterprise**: Designed for enterprise development and IT teams building, running, and operating business-critical applications at scale.
3. **Docker Desktop**: An easy-to-install application for Mac or Windows environments that includes Docker Engine, Docker CLI client, Docker Compose, Docker Content Trust, Kubernetes, and Credential Helper.

For most users, Docker Engine - Community or Docker Desktop will be sufficient.

## Installing Docker on Linux

Docker runs natively on Linux, making it the ideal platform for Docker containers. There are two main methods to install Docker on Linux: using the convenience script or manual installation for specific distributions.

### Method 1: Using the Docker Installation Script (Recommended for Quick Setup)

Docker provides a convenient script that automatically detects your Linux distribution and installs Docker for you. This method is quick and works across many Linux distributions:

1. Run the following command to download and execute the Docker installation script:
   ```
   wget -qO- https://get.docker.com | sh
   ```

2. Once the installation is complete, start the Docker service:
   ```
   sudo systemctl start docker
   ```

3. Enable Docker to start on boot:
   ```
   sudo systemctl enable docker
   ```

This method is ideal for quick setups and testing environments. However, for production environments, you might want to consider the manual installation method for more control over the process.

### Method 2: Manual Installation for Specific Distributions

For more control over the installation process or if you prefer to follow distribution-specific steps, you can manually install Docker. Here are instructions for popular Linux distributions:

Docker runs natively on Linux, making it the ideal platform for Docker containers. Here's how to install Docker on popular Linux distributions:

### Ubuntu

1. Update your package index:
   ```
   sudo apt-get update
   ```

2. Install prerequisites:
   ```
   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
   ```

3. Add Docker's official GPG key:
   ```
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. Set up the stable repository:
   ```
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```

5. Update the package index again:
   ```
   sudo apt-get update
   ```

6. Install Docker:
   ```
   sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```

### CentOS

1. Install required packages:
   ```
   sudo yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

2. Add Docker repository:
   ```
   sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```

3. Install Docker:
   ```
   sudo yum install docker-ce docker-ce-cli containerd.io
   ```

4. Start and enable Docker:
   ```
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

### Other Linux Distributions

For other Linux distributions, refer to the official Docker documentation: https://docs.docker.com/engine/install/

## Installing Docker on macOS

For macOS, the easiest way to install Docker is by using Docker Desktop:

1. Download Docker Desktop for Mac from the official Docker website: https://www.docker.com/products/docker-desktop

2. Double-click the downloaded `.dmg` file and drag the Docker icon to your Applications folder.

3. Open Docker from your Applications folder.

4. Follow the on-screen instructions to complete the installation.

## Installing Docker on Windows

For Windows 10 Pro, Enterprise, or Education editions, you can install Docker Desktop:

1. Download Docker Desktop for Windows from the official Docker website: https://www.docker.com/products/docker-desktop

2. Double-click the installer to run it.

3. Follow the installation wizard to complete the installation.

4. Once installed, Docker Desktop will start automatically.

For Windows 10 Home or older versions of Windows, you can use Docker Toolbox, which uses Oracle VirtualBox to run Docker:

1. Download Docker Toolbox from: https://github.com/docker/toolbox/releases

2. Run the installer and follow the installation wizard.

3. Once installed, use the Docker Quickstart Terminal to interact with Docker.

## Post-Installation Steps

After installing Docker, there are a few steps you should take:

1. Verify the installation:
   ```
   docker version
   docker run hello-world
   ```

2. Configure Docker to start on boot (Linux only):
   ```
   sudo systemctl enable docker
   ```

3. Add your user to the docker group to run Docker commands without sudo (Linux only):
   ```
   sudo usermod -aG docker $USER
   ```
   Note: You'll need to log out and back in for this change to take effect.

## Docker Desktop vs Docker Engine

It's important to understand the difference between Docker Desktop and Docker Engine:

- **Docker Desktop** is a user-friendly application that includes Docker Engine, Docker CLI client, Docker Compose, Docker Content Trust, Kubernetes, and Credential Helper. It's designed for easy installation and use on Mac and Windows.

- **Docker Engine** is the core Docker runtime available for Linux systems. It doesn't come with the additional tools included in Docker Desktop but can be installed alongside them separately.

## Troubleshooting Common Installation Issues

1. **Permission denied**: 
   If you encounter "permission denied" errors, ensure you've added your user to the docker group or are using sudo.

2. **Docker daemon not running**: 
   On Linux, try starting the Docker service: `sudo systemctl start docker`

3. **Conflict with VirtualBox (Windows)**: 
   Ensure Hyper-V is enabled for Docker Desktop, or use Docker Toolbox if you need to keep using VirtualBox.

4. **Insufficient system resources**: 
   Docker Desktop requires at least 4GB of RAM. Increase your system's or virtual machine's allocated RAM if needed.

## Updating Docker

To update Docker:

- On Linux, use your package manager (e.g., `apt-get upgrade docker-ce` on Ubuntu)
- On Mac and Windows, Docker Desktop will notify you of updates automatically

## Uninstalling Docker

If you need to uninstall Docker:

- On Linux, use your package manager (e.g., `sudo apt-get purge docker-ce docker-ce-cli containerd.io` on Ubuntu)
- On Mac, remove Docker Desktop from the Applications folder
- On Windows, uninstall Docker Desktop from the Control Panel

## Conclusion

Installing Docker is generally a straightforward process, but it can vary depending on your operating system. Always refer to the official Docker documentation for the most up-to-date installation instructions for your specific system. With Docker successfully installed, you're now ready to start exploring the world of containerization!
