**🏠 Automated HomeLab Media Server & Infrastructure**

**📖 Project Overview**

This repository serves as documentation and Infrastructure as Code (IaC) for my personal HomeLab media server. The goal of this project was to design, deploy, and optimize a cost-effective, headless server capable of automated media management, hardware-accelerated video transcoding, and secure remote access for external users. 

The server currently supports concurrent remote streams for users across **Southeast Asia (Indonesia, Philippines), East Asia (South Korea), and North America (USA)**, demonstrating real-world application of international network routing and bandwidth management.

This project demonstrates practical experience in Linux administration, Docker containerization, network security, and hardware resource allocation.

## Network Topology

![Homelab Network Diagram](https://github.com/BBudi-634/Homelabbing/blob/main/Homelab%20network%20diagram.drawio.png?raw=true)



**🏗️ Architecture & Tech Stack**
- **Host Hardware**: Dell OptiPlex (Intel Core i3 8th Gen, 16GB RAM)

- **Host OS**: Linux (ZimaOS)

- **Storage**: 12TB Direct Attached Storage (DAS) via USB, formatted to ext4 for native Linux permissions.

- **Containerization**: Docker & Docker Compose

- **Media Streaming**: Jellyfin (Hardware Accelerated via Intel QuickSync)

- **Media Management (The Arr Stack)**: Sonarr, Radarr, Bazarr, Prowlarr, Seerr

- **Download Client**: SABnzbd (Usenet)

- **Remote Access**: Tailscale (Zero-Trust VPN)

**🚀 Key Technical Achievements**

- **Hardware Acceleration Pass-through**: Configured Docker to pass the host's integrated Intel GPU (/dev/dri) to the Jellyfin container. This enabled Intel QuickSync, allowing the server to transcode 1080p video at 270+ frames per second, eliminating buffering for remote users without overloading the CPU.

- **Zero-Trust Remote Networking**: Implemented a Tailscale VPN mesh network to provide secure, end-to-end encrypted remote access for an **international user base (USA, South Korea, Philippines, Indonesia)**. This bypassed the need for insecure port forwarding on the home router, ensuring the network remains invisible to external scans while providing low-latency routing across continents.

- **Storage & Permission Migration**: Migrated legacy storage from exFAT (which lacks native Linux permission support) to ext4. Resolved complex UID/GID mapping issues to ensure Docker containers could read and write to the host drives seamlessly.

- **Bandwidth Optimization**: Configured Jellyfin user profiles with bitrate limits (10-12 Mbps) to balance high-quality remote streams with available ISP upload bandwidth, ensuring stable playback for multiple concurrent users.

- **RAM Upgrade via Hardware Recycling**: Repurposed 8GB of SO-DIMM RAM from a retired ThinkPad laptop to upgrade the OptiPlex host, doubling the system memory to 16GB to support Docker container overhead.

**🛠️ Troubleshooting Case Studies**

**1. The Transcoding Bottleneck**
- **Issue**: Remote streams were buffering heavily. Jellyfin logs showed software transcoding was running at only 18fps.

- **Resolution**: Diagnosed the issue as a missing hardware device mapping in the Docker Compose file. Added the /dev/dri device to the Jellyfin service and enabled Intel QSV in the settings, instantly resolving the CPU bottleneck

**2. The File System Permission Trap**

- **Issue**: Containers were throwing "Permission Denied" errors when attempting to write to the DAS.

- **Resolution**: Identified that the exFAT file system does not support Unix permissions. Re-formatted the drive to ext4 and applied recursive chown to match the container's internal PUID/PGID.

**3. Broken Automation Paths**

- **Issue**: Sonarr and Radarr were downloading files but failing to import them to the media library.

- **Resolution**: Corrected the Docker volume bind mounts to map the entire storage root (/media/HD-1) to /data. This ensured both the download client and the media managers saw the exact same file paths, allowing for instant hardlinking instead of slow file copies.

**4. International Latency Management**

- **Issue**: Users located in North America and East Asia experienced longer buffer times compared to users in Southeast Asia due to the physical distance the data packets had to travel.

- **Resolution**: Configured Jellyfin user profiles with region-specific bitrate limits (10-12 Mbps) to ensure stable playback over international Tailscale routes, preventing packet loss and buffering for intercontinental users.

**🔐 Security Note**

All API keys, passwords, and sensitive environment variables have been omitted from this repository. In a production environment, these would be managed securely using a .env file or Docker Secrets.
