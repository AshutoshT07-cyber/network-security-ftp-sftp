# FTP vs SFTP Network Security Analysis with Wireshark

## Description

This project demonstrates the security differences between FTP (File Transfer Protocol) and SFTP (Secure File Transfer Protocol) by capturing and analyzing network traffic using Wireshark on Kali Linux. The guide includes steps to set up FTP and SFTP environments, capture traffic, and analyze the results to show why SFTP is more secure.

## Prerequisites

- **Kali Linux**: The operating system used for this project.
- **Wireshark**: Typically pre-installed on Kali Linux. If not, install it with:
  ```bash
  sudo apt install wireshark
  ```
- **OpenSSH Server**: Required for SFTP. Install with:
  ```bash
  sudo apt install openssh-server
  ```

## Step 1: FTP Login (Insecure)

FTP transmits credentials and data in plain text, making it insecure. This section shows how to capture FTP traffic using Wireshark.

1. **Start Wireshark:**
   ```bash
   sudo wireshark &
   ```
   - Select `eth0` as the interface.
   - Leave the capture filter empty.
   - Click **Start** to begin capturing packets.

2. **Initiate FTP Connection:**
   In a new terminal, connect to an FTP server running on localhost:
   ```bash
   ftp 127.0.0.1
   ```
   - Username: `testftp`
   - Password: `ftp123`

3. **Execute FTP Commands:**
   Run the following commands to generate traffic:
   ```
   ls
   pwd
   bye
   ```

4. **Stop and Save Capture:**
   - Stop the Wireshark capture.
   - Save it as `ftp_login.pcap`.

5. **Analyze Capture:**
   - Open the saved capture in Wireshark.
   - Apply the filter:
     ```
     ftp.request.command == "USER" || ftp.request.command == "PASS"
     ```
   - Observe that the username and password are visible in plain text.
![Image Alt](https://github.com/AshutoshT07-cyber/network-security-ftp-sftp/blob/main/wireshark-ftp-session.png.png?raw=true)
![Image Alt](https://github.com/AshutoshT07-cyber/network-security-ftp-sftp/blob/main/wireshark-ftp-user-pass-filter.png.png?raw=true)

## Step 2: SFTP Login (Secure)

SFTP uses SSH to encrypt all communication, making it secure. This section includes a fix for SSH connection issues and steps to capture SFTP traffic.

### Fixing SSH Connection Issues

If you encounter a "Connection refused" error when trying to connect via SFTP, ensure that the SSH server is installed and running.

1. **Install SSH Server:**
   ```bash
   sudo apt update
   sudo apt install openssh-server
   ```

2. **Start and Enable SSH Service:**
   ```bash
   sudo systemctl start ssh
   sudo systemctl enable ssh
   ```

3. **Verify SSH Service:**
   Check if SSH is listening on port 22:
   ```bash
   sudo ss -tlnp | grep :22
   ```

4. **Create Test User:**
   If the user `testftp` does not exist, create it:
   ```bash
   sudo useradd -m testftp
   sudo passwd testftp   # Set password to ftp123
   sudo usermod -s /bin/bash testftp
   ```

### Capturing SFTP Traffic

1. **Start Wireshark:**
   ```bash
   sudo wireshark &
   ```
   - Select `eth0` as the interface.
   - Click **Start**.

2. **Initiate SFTP Connection:**
   In a new terminal, connect to the SFTP server:
   ```bash
   sftp testftp@127.0.0.1
   ```
   - Enter password: `ftp123` (not displayed).

3. **Execute SFTP Commands:**
   Run the following commands:
   ```
   ls
   pwd
   bye
   ```

4. **Stop and Save Capture:**
   - Stop the Wireshark capture.
   - Save it as `sftp_login.pcap`.

5. **Analyze Capture:**
   - Open the saved capture in Wireshark.
   - Apply the filter:
     ```
     ssh
     ```
   - Observe that all packets are encrypted, and no credentials are visible.
   - ![Image Alt](image_url)
   - ![Image Alt](image_url)
   - ![Image Alt](image_url)

## Additional Notes

- **Permissions for Wireshark**: Wireshark requires root privileges to capture packets. Running `sudo wireshark &` ensures the necessary permissions. Alternatively, add your user to the `wireshark` group to capture packets without root privileges, as described in the [Wireshark Wiki](https://wiki.wireshark.org/CaptureSetup/CapturePrivileges).
- **Security Considerations**: Enabling the SSH server on Kali Linux should be done cautiously, as it can expose your system to unauthorized access if not properly secured. Consider disabling root login and using strong passwords or key-based authentication.
- **Kali Linux Environment**: This guide assumes you are using Kali Linux, which typically has Wireshark pre-installed. If not, install it using `sudo apt install wireshark`.

## References

- [Installing SSH on Kali Linux](https://medium.com/@InzenSecure/ssh-configuration-in-kali-linux-3f7c456560a9)
- [Enabling SSH on Kali Linux](https://mangohost.net/blog/enable-ssh-on-kali-linux/)
- [Wireshark for Network Analysis on Kali Linux](https://anovin.mk/tutorial/how-to-use-wireshark-for-network-analysis-in-kali-linux/)
- [Wireshark Permissions for Packet Capture](https://wiki.wireshark.org/CaptureSetup/CapturePrivileges)
