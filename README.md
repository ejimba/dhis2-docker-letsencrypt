# DHIS2 Docker with Let's Encrypt SSL

This project sets up a DHIS2 instance with PostgreSQL database using Docker, with automatic SSL certificate management through Let's Encrypt. The setup includes nginx-proxy for reverse proxying and automatic certificate generation and renewal. If you don't have a domain or dont require security certificates, please use [this version](https://github.com/ejimba/dhis2-docker)

## Project Structure

```
.
├── data/
│   ├── dhis/
│   │   └── dhis.conf
│   └── postgres/
├── docker-compose.yml
├── LICENSE
└── README.md
```

## Services

The Docker Compose setup includes the following services:

- **nginx-proxy**: Reverse proxy that automatically generates nginx configuration
- **letsencrypt-companion**: Automatic SSL certificate generation and renewal
- **dhis2-init**: Initialization container that sets proper file permissions
- **dhis2**: DHIS2 application server (v2.42)
- **postgres**: PostgreSQL database with PostGIS extension (v16-3.5)

## Prerequisites

Before you can run this project, you need:

### Software Requirements
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

### Domain and DNS Requirements
- A registered domain name
- DNS A record pointing your domain to your server's public IP address
- Server accessible from the internet on ports 80 and 443

## Setup

1. **Clone this repository:**
   ```bash
   git clone <repository-url>
   cd dhis2-docker-letsencrypt
   ```

2. **Configure your domain:**
   Edit the `docker-compose.yml` file and replace the placeholder values in the DHIS2 service:
   ```yaml
   SERVER_BASE_URL: https://your-domain.com          # Replace with your domain
   VIRTUAL_HOST: your-domain.com                     # Replace with your domain  
   LETSENCRYPT_HOST: your-domain.com                 # Replace with your domain
   LETSENCRYPT_EMAIL: your-email@example.com         # Replace with your email
   ```

3. **Ensure DNS is configured:**
   Verify that your domain points to your server's public IP address:
   ```bash
   nslookup your-domain.com
   ```

## Running the Project

1. **Start the services:**
   ```bash
   docker-compose up -d
   ```

2. **Monitor the startup:**
   ```bash
   docker-compose logs -f
   ```

The services will start in the following order:
1. **dhis2-init** - Sets proper file permissions for DHIS2_home
2. **nginx-proxy** - Starts the reverse proxy
3. **letsencrypt-companion** - Generates SSL certificates (may take a few minutes)
4. **postgres** - Database server
5. **dhis2** - DHIS2 application server

**Access your DHIS2 instance at: `https://your-domain.com`**

> **Note**: Initial SSL certificate generation may take 2-5 minutes. The site may show a "502 Bad Gateway" error until DHIS2 fully starts up.

## Stopping the Project

To stop the project, run the following command in the project directory:

```bash
docker-compose down
```

This will stop and remove all containers. To also remove the volumes (including SSL certificates):

```bash
docker-compose down -v
```

> **Warning**: Using `-v` will remove SSL certificates and database data. Only use this for complete cleanup.

## Data Persistence

The project uses both local directories and Docker volumes for data persistence:

### Local Directories
- `./data/dhis/` - DHIS2 configuration and application data
- `./data/postgres/` - PostgreSQL database files

### Docker Volumes  
- `certs` - SSL certificates from Let's Encrypt
- `vhost` - nginx virtual host configurations
- `html` - Web challenge files for certificate validation

### Important Notes
- SSL certificates are automatically renewed by the letsencrypt-companion
- Database data persists between container restarts
- DHIS2 configuration is loaded from `./data/dhis/dhis.conf`

## SSL Certificate Management

The setup automatically:
- Generates SSL certificates on first run
- Renews certificates before expiration  
- Handles multiple domains if needed
- Redirects HTTP traffic to HTTPS

## Troubleshooting

### Certificate Issues
- Ensure your domain DNS is properly configured
- Check that ports 80 and 443 are accessible from the internet
- Monitor logs: `docker-compose logs letsencrypt-companion`

### DHIS2 Connection Issues  
- Wait for all services to start (may take 5-10 minutes)
- Check DHIS2 logs: `docker-compose logs dhis2`
- Verify database connection: `docker-compose logs postgres`

### Common Commands
```bash
# View all service status
docker-compose ps

# Restart specific service
docker-compose restart dhis2

# View real-time logs
docker-compose logs -f dhis2

# Force certificate renewal (if needed)
docker-compose restart letsencrypt-companion
```

## Contributing

Contributions are welcome. Please submit a pull request or create an issue to discuss the changes.

## License

This project is licensed under the MIT License.
