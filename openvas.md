
# OpenVAS Penetration Testing Guide

This guide is intended for cybersecurity analysts looking to utilize OpenVAS for penetration testing. OpenVAS is a powerful open-source tool for vulnerability scanning and management.

## Table of Contents
1. [Introduction](#introduction)
2. [Installation](#installation)
   - [System Requirements](#system-requirements)
   - [Installing OpenVAS](#installing-openvas)
3. [Configuring OpenVAS](#configuring-openvas)
4. [Performing a Scan](#performing-a-scan)
   - [Setting Up a Target](#setting-up-a-target)
   - [Starting a Scan](#starting-a-scan)
5. [Analyzing Scan Results](#analyzing-scan-results)
6. [Mitigation and Reporting](#mitigation-and-reporting)
7. [Best Practices](#best-practices)

## Introduction
OpenVAS (Open Vulnerability Assessment System) is an open-source tool designed to scan and manage vulnerabilities in systems and networks. It is a part of the Greenbone Vulnerability Management (GVM) solution and is widely used for identifying security issues in IT infrastructures.

## Installation

### System Requirements
- **Operating System**: Linux (e.g., Ubuntu, Debian)
- **Memory**: At least 2GB of RAM
- **Storage**: At least 10GB of free disk space
- **Network**: Stable internet connection for updates

### Installing OpenVAS
Follow these steps to install OpenVAS on Kali using [Docker](https://docs.docker.com/):

1. **Install Docker**:
    ```bash
    sudo apt update
	sudo apt install -y docker.io
	sudo systemctl enable docker --now
	docker
	```
	Add yourself to the docker group to use `docker` without `sudo`
	```bash
    sudo usermod -aG docker $USER
	```
	The final thing is to **logout and in again**.
2. **Install Docker-CE**:
    ```bash
    echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian bookworm stable" | \ sudo tee /etc/apt/sources.list.d/docker.list
    curl -fsSL https://download.docker.com/linux/debian/gpg |  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo apt update
    sudo apt install -y docker-ce docker-ce-cli containerd.io
	```

3. **Setup OpenVAS**:
	Create download directory:
    ```bash
    export DOWNLOAD_DIR=$HOME/greenbone-community-container && mkdir -p $DOWNLOAD_DIR
    ```
    Download the docker compose file:
    ```bash
    cd $DOWNLOAD_DIR && curl -f -L https://greenbone.github.io/docs/latest/_static/docker-compose-22.4.yml -o docker-compose.yml
    ```

6. **Pull the Containers**:
    ```bash
    docker compose -f $DOWNLOAD_DIR/docker-compose.yml -p greenbone-community-edition pull
    ```

7. **Start the Containers & enable Log output**:
    ```bash
    docker compose -f $DOWNLOAD_DIR/docker-compose.yml -p greenbone-community-edition up -d & docker compose -f $DOWNLOAD_DIR/docker-compose.yml -p greenbone-community-edition logs -f
    ```
7. **Change Password of Admin User**:
    By default, a user _admin_ with the password _admin_ is created. This is insecure and it is highly recommended to set a new password.
    ```bash
    docker compose -f $DOWNLOAD_DIR/docker-compose.yml -p greenbone-community-edition \
    exec -u gvmd gvmd gvmd --user=admin --new-password='<password>'
    ```

## Configuring OpenVAS
1. **Access the web interface**:
   Open a web browser and navigate to `https://<your-server-ip>:9392`. Log in using the credentials created during setup.

2. **Update the Feed**:
   Ensure that the vulnerability data feeds are up-to-date. Go to `Administration > Feed Status` and update if necessary.

## Performing a Scan

### Setting Up a Target
1. **Create a New Target**:
   - Navigate to `Configuration > Targets`.
   - Click on `New Target`.
   - Fill in the details such as `Name`, `Hosts`
   - Select `Port List` to `Full`: detect all TCP and Nmap top 1000 UDP.
   - Alive Test: Must be set to `Consider Alive` to detect all the open ports.
   - Save the target.

### Starting a Scan
1. **Create a New Task**:
   - Navigate to `Scans > Tasks`.
   - Click on `New Task`.
   - Fill in the details such as `Name` and `Comment`.
   - Set `Scan Targets` to the previously created Target.
   - Set `Min QoD` to low values (`20%`).
   - Set `Scan Config` to `Full and Fast`.
   - Set `Maximum concurrently executed NVTs per host` and `Maximum concurrently scanned hosts`
   - Save the task.

2. **Start the Task**:
   - Select the newly created task and click on the `Start` button.

## Analyzing Scan Results
1. **View the Results**:
   - Navigate to `Scans > Reports`.
   - Click on the report corresponding to your task.

2. **Detailed Analysis**:
   - Review the vulnerabilities found, categorized by severity (High, Medium, Low).
   - Click on each vulnerability for more details, including the affected hosts, impact, and remediation steps.

## Mitigation and Reporting
1. **Remediation**:
   - Prioritize vulnerabilities based on severity and criticality.
   - Follow the remediation steps provided in the scan results.
   - Apply patches, update configurations, or implement additional security controls as necessary.

2. **Generate Reports**:
   - Navigate to `Scans > Reports`.
   - Select the desired report and click on `Download`.
   - Choose the report format (XML, CSV, PDF, HTML, TXT).

3. **Documentation**:
   - Document all identified vulnerabilities, remediation actions taken, and any remaining risks.
   - Provide recommendations for future security improvements.

## Best Practices
- **Regular Scans**: Schedule regular scans to continuously monitor for new vulnerabilities.
- **Update Feeds**: Regularly update OpenVAS feeds to ensure the latest vulnerability data is used.
- **Network Segmentation**: Perform scans on different network segments to get a comprehensive view.
- **Credentialed Scans**: Use credentialed scans for a deeper analysis of systems.
- **Monitor Logs**: Keep an eye on OpenVAS logs for any errors or issues during scans.

## Conclusion
Using OpenVAS for penetration testing provides valuable insights into the security posture of your systems and networks. By following this guide, you can effectively set up, run scans, and analyze the results to enhance your cybersecurity defenses.
