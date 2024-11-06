# Docker Personal Multimedia Server (DPMS)

A complete solution for your personal media server needs, including automatic download, media organization, and streaming capabilities.

## 🚀 Features

- **Plex Media Server**: Stream your media anywhere
- **Automated Downloads**: Integration with Sonarr (TV Shows) and Radarr (Movies)
- **Torrent Management**: Transmission client with VPN protection
- **Secure Access**: SWAG reverse proxy with Cloudflare integration
- **Download Management**: Jackett for torrent indexing
- **Analytics**: Tautulli for Plex statistics and monitoring

## 🛠️ Prerequisites

- Linux-based system (Ubuntu/Debian recommended)
- At least 2GB RAM
- Minimum 20GB storage space (recommended: 100GB+)
- Active internet connection
- (Optional) Cloudflare account for domain management
- (Optional) NordVPN subscription

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

### 5. Start the Services
```bash
docker compose up -d
```

## ⚙️ Configuration

### Required Environment Variables
Check the `.env.example` file for all required variables. Key sections include:

- Global Settings (PGID, PUID, timezone)
- Docker network configuration
- SWAG (reverse proxy) settings
- Plex configuration
- NordVPN credentials
- Media paths configuration

### Service-Specific Setup

#### Plex
1. Access Plex at `http://your-server-ip:32400/web`
2. Follow the first-time setup wizard
3. Configure your libraries pointing to:
   - `/movies` for Movies
   - `/tv` for TV Shows

#### Sonarr/Radarr
1. Access Sonarr at `http://your-server-ip:8989`
2. Access Radarr at `http://your-server-ip:7878`
3. Configure download client (Transmission)
4. Set up Jackett indexers

#### SWAG
1. Ensure your Cloudflare credentials are correctly set in `.env`
2. Configure your domain DNS to point to your server
3. SSL certificates will be automatically generated

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
```

## 🔒 Security

- All traffic is routed through NordVPN for downloading services
- SWAG provides SSL encryption for all services
- Default credentials should be changed immediately after installation
- Regular updates are recommended for all containers

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
