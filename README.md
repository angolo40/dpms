# Docker Personal Multimedia Server (DPMS)

A complete solution for your personal media server needs, including automatic download, media organization, and streaming capabilities.

## 🚀 Features

- **Plex Media Server**: Stream your media anywhere
- **Automated Downloads**: Integration with Sonarr (TV Shows) and Radarr (Movies)
- **Torrent Management**: Transmission client
- **Download Management**: Jackett for torrent indexing
- **Analytics**: Tautulli for Plex statistics and monitoring
- **Optional Secure Access**: SWAG reverse proxy with optional Cloudflare integration
- **Optional VPN**: NORDVPN integration available (https://github.com/bubuntux/nordlynx)

## 🛠️ Prerequisites

- Linux-based system (Ubuntu/Debian recommended)
- At least 8GB RAM
- Minimum 20GB storage space (recommended: 100GB+)
- Active internet connection
- (Optional) Intel > i5 8xxx for best HW trascoding and PlexPass
- (Optional) Domain name and Cloudflare account for SWAG integration
- (Optional) NordVPN subscription for VPN protection

## 📦 Installation

### 1. Install Docker
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

### 2. Install Git
```bash
sudo apt update && sudo apt install git
```

### 3. Clone the Repository
```bash
git clone https://github.com/angolo40/dpms.git
cd dpms
```

### 4. Configure Environment
```bash
cp .env.example .env
```
Edit the `.env` file with your preferred text editor and configure all the required variables:
```bash
nano .env
```

### 5. Optional: Configure SWAG with Cloudflare
If you want to use SWAG with Cloudflare integration:

1. Keep the SWAG service in docker-compose.yml
2. Configure the Cloudflare-related variables in your .env file
3. Set up the required configuration files:

   a. Configure tunnelconfig.yml:
   ```bash
   # Copy the example file to swag/config/tunnelconfig.yml
   cp tunnelconfig.yml /path/to/swag/config/
   # Edit the file and replace yourdomain.com with your actual domain
   nano /path/to/swag/config/tunnelconfig.yml
   ```

   Example configuration:
   ```yaml
   ingress:
     - hostname: plex.yourdomain.com
       service: https://localhost:443
       originRequest:
         connectTimeout: 10s
         noTLSVerify: true
     # Repeat for other services
     - service: http_status:404
   ```

   b. Configure Cloudflare DNS authentication:
   ```bash
   # Copy the example file to swag/config/dns-conf/cloudflare.ini
   cp cloudflare.ini /path/to/swag/config/dns-conf/
   # Edit the file and add your Cloudflare API token
   nano /path/to/swag/config/dns-conf/cloudflare.ini
   ```

   The cloudflare.ini file only requires editing the dns_cloudflare_api_token field with your Cloudflare API token.

4. Configure the proxy settings:
   ```bash
   cd /path/to/swag/config/proxy-conf/
   # Rename the sample configuration files
   mv radarr.subdomain.conf.sample radarr.subdomain.conf
   mv sonarr.subdomain.conf.sample sonarr.subdomain.conf
   mv jackett.subdomain.conf.sample jackett.subdomain.conf
   # Repeat for other services
   ```

5. If using both SWAG and NordVPN, modify each proxy configuration file:
   ```nginx
   # Original configuration in each .conf file
   set $upstream_app radarr;
   # Change to
   set $upstream_app vpn;
   ```

### Advanced Security Configuration (🚧 Under Construction)

Cloudflare offers additional security features through access configuration, such as:
- Team access controls
- GeoIP blocking
- Additional authentication layers
- Access policies
- Traffic filtering

The following optional configuration can be added to each service in your tunnelconfig.yml to enable these features:
```yaml
    access:
      required: true
      teamName: yourdomain.com
```

Detailed instructions for setting up and configuring these advanced security features will be provided in future updates.

To run without Cloudflare:
1. Comment out or remove the SWAG service from docker-compose.yml
2. Uncomment the port mappings in each service
3. Remove Cloudflare-related variables from your .env file

### 6. Optional: Configure NordVPN
If you want to use NordVPN:
1. Keep the VPN service and network configuration as is
2. Configure NordVPN credentials in your .env file

To run without NordVPN:
1. Remove the VPN service from docker-compose.yml
2. For each service using `network_mode: service:vpn`, replace with:
   ```yaml
   networks:
     - compose_default
   ```
3. Remove the VPN-related environment variables from your .env file

### 7. Start the Services
```bash
docker compose up -d
```

## ⚙️ Service Access

### With Cloudflare (SWAG) enabled:
Access your services through their subdomains:
- Plex: https://plex.yourdomain.com
- Radarr: https://radarr.yourdomain.com
- Sonarr: https://sonarr.yourdomain.com
- Jackett: https://jackett.yourdomain.com
- Transmission: https://torrent.yourdomain.com
- Tautulli: https://tautulli.yourdomain.com

### Without Cloudflare:
Access your services through their direct ports:
- Plex: http://your-server-ip:32400
- Radarr: http://your-server-ip:7878
- Sonarr: http://your-server-ip:8989
- Jackett: http://your-server-ip:9117
- Transmission: http://your-server-ip:9091
- Tautulli: http://your-server-ip:8181

## 📂 Directory Structure
```
/config
├── plex            # Plex configuration and metadata
├── sonarr          # Sonarr configuration
├── radarr          # Radarr configuration
├── transmission    # Transmission settings and watch folder
├── jackett         # Jackett configuration
├── tautulli        # Tautulli configuration
└── swag            # SWAG configuration and SSL certificates
    ├── config
    │   ├── proxy-conf     # Nginx proxy configurations
    │   ├── dns-conf      # Cloudflare DNS configuration
    │   │   └── cloudflare.ini  # Cloudflare API credentials
    │   └── tunnelconfig.yml   # Cloudflare tunnel configuration
```

## 🔒 Security

### With Cloudflare & VPN:
- All traffic is routed through NordVPN for downloading services
- SWAG provides SSL encryption for all services
- Cloudflare provides additional security features (optional):
  - GeoIP blocking for restricting access by geographic location
  - Advanced authentication policies
  - Traffic filtering and rate limiting
  - DDoS protection

### Without Cloudflare & VPN:
- Basic authentication is still enforced for all services
- Consider using your router's firewall rules
- Recommend using HTTPS when possible
- Consider setting up alternative VPN solutions
- Note: Advanced security features won't be available without Cloudflare integration

## 📝 Maintenance

### Update Containers
```bash
docker compose pull
docker compose up -d
```

### View Logs
```bash
docker compose logs -f [service_name]
```

### Backup
Regular backups of the `/config` directory are recommended:
```bash
tar -czf dpms_backup_$(date +%Y%m%d).tar.gz /path/to/config
```

## 🚧 Troubleshooting

### Common Issues

1. **VPN Connection Issues**
   - Check NordVPN credentials
   - Verify VPN container logs
   - Ensure ports are not blocked

2. **Media Not Appearing in Plex**
   - Verify file permissions (PGID/PUID)
   - Check path mappings
   - Scan library manually

3. **Download Problems**
   - Verify Jackett indexers are working
   - Check Transmission connection through VPN
   - Verify path permissions

4. **SWAG/Proxy Issues**
   - Verify proxy configuration files are properly renamed and configured
   - Check SWAG logs for specific errors
   - Ensure upstream_app is correctly set when using VPN
   - Verify cloudflare.ini contains the correct API token
   - Check tunnelconfig.yml has the correct domain names configured

### Initial Setup Checklist
- [ ] Environment variables configured in .env
- [ ] tunnelconfig.yml copied to correct location and domain name updated
- [ ] cloudflare.ini copied to dns-conf directory and API token configured
- [ ] Proxy configuration files renamed and configured
- [ ] If using VPN, upstream_app properly set in proxy configurations

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a new Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📧 Support

For issues and feature requests, please use the GitHub issues page.

---
⭐ If you find this project useful, please consider giving it a star on GitHub!
