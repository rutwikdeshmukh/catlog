# Catlog - Real-time Log Viewer

A lightweight, cross-platform log viewer application that streams log files in real-time through your browser. Works on Windows, macOS, and Linux with optional nginx reverse proxy support.

## Features

- 🚀 Real-time log streaming via WebSocket
- 🔐 User authentication with role-based access control
- 🌐 Works on Windows, macOS, and Linux
- 📱 Responsive web interface with VS Code Dark Modern theme
- 🔒 Optional SSL/HTTPS support with nginx
- 📊 Load historical log lines on demand
- 🎯 Path-based access control for different users
- ⚡ Lightweight and fast

## Quick Start

### Prerequisites
- Go 1.16+ installed

### Local Development (Windows/macOS/Linux)

1. **Clone and setup:**
```bash
cd loged
cp example.config.yml config.yml
```

2. **Install and build:**
```bash
./catlog install
```

3. **Start the server:**
```bash
./catlog start
```

4. **Access the application:**
Open your browser and go to: `http://localhost:8008`

Default credentials:
- Username: `admin`
- Password: `catlog123`

## Configuration

### config.yml

```yaml
port: 8008                    # Port to run on
base_url: ""                  # Leave empty for localhost, or set to "/catlog" for nginx
ssl:
  enabled: false              # Set to true when using nginx with SSL
  cert_path: "/etc/ssl/certs/catlog.crt"
  key_path: "/etc/ssl/private/catlog.key"
auth:
  enabled: true
  users:
    - username: "admin"
      password: "catlog123"
      role: "admin"           # admin has access to all logs
      allowed_paths: []
    - username: "user"
      password: "password"
      role: "user"
      allowed_paths:
        - "/var/log/app/*"    # Supports wildcards
        - "/var/log/nginx/*"
log_files:
  - name: "Application Log"
    path: "./runtime/catlog.log"
  - name: "System Log"
    path: "/var/log/syslog"
```

### Base URL Configuration

**For Local Development:**
```yaml
base_url: ""
```
Access at: `http://localhost:8008`

**For Nginx Reverse Proxy:**
```yaml
base_url: "/catlog"
```
Access at: `https://your-server/catlog`

## Usage

### Catlog Commands

```bash
./catlog install              # Install dependencies and build
./catlog start                # Start the server
./catlog stop                 # Stop the server
./catlog status               # Check if running
./catlog restart              # Restart the server
./catlog update               # Update and rebuild
./catlog uninstall            # Remove all files
```

### Nginx Commands (Linux only)

```bash
./catlog nginx setup          # Install nginx, generate SSL, and configure reverse proxy
./catlog nginx restart        # Restart nginx
./catlog nginx stop           # Stop nginx
./catlog nginx remove         # Remove catlog nginx configuration
```

## Server Setup with Nginx

### 1. Setup Nginx
```bash
./catlog nginx setup
```

This will:
- Install nginx (if not already installed)
- Generate self-signed SSL certificates
- Configure nginx as a reverse proxy
- Update config.yml with `base_url: "/catlog"` and enable SSL
- Set timezone to IST

### 2. Start Catlog
```bash
./catlog start
```

### 3. Access via Nginx
- HTTPS: `https://your-server-ip/catlog`
- HTTP: `http://your-server-ip/catlog` (redirects to HTTPS)

**Note:** Browser will show SSL warning for self-signed certificate. Click "Advanced" → "Proceed" to continue.

## User Roles

### Admin
- Access to all log files
- No path restrictions

### Custom Roles
- Define custom roles in config.yml
- Restrict access to specific paths using wildcards
- Example: `/var/log/nginx/*` allows all nginx logs

## File Structure

```
loged/
├── catlog                 # Main control script
├── config.yml             # Configuration file
├── example.config.yml     # Example configuration
├── catlog-nginx           # Nginx configuration template
├── src/
│   ├── main.go            # Application source code
│   ├── go.mod             # Go dependencies
│   └── go.sum
├── runtime/
│   ├── catlog-server      # Compiled binary
│   ├── catlog.pid         # Process ID
│   └── catlog.log         # Log file
└── README.md
```

## Security Considerations

- Change default passwords in config.yml
- Use strong passwords for user accounts
- When using nginx, always enable SSL
- Restrict file access permissions in config.yml
- Run on a secure network
- For production, use proper SSL certificates (not self-signed)

## Troubleshooting

### Port Already in Use
Change the port in config.yml:
```yaml
port: 8009
```

### Cannot Access Application
1. Check if server is running: `./catlog status`
2. Check logs: `tail -f runtime/catlog.log`
3. Verify config.yml exists and is valid

### WebSocket Connection Failed
- Ensure `base_url` is empty for localhost
- Ensure `base_url: "/catlog"` when using nginx
- Check browser console for exact error

### Nginx Issues
1. Check nginx status: `sudo systemctl status nginx`
2. Test nginx config: `sudo nginx -t`
3. Check nginx logs: `sudo tail -f /var/log/nginx/error.log`

### SSL Certificate Issues
Regenerate certificates:
```bash
./catlog nginx remove
./catlog nginx setup
```

## Development

### Building Locally
```bash
cd src
go build -o ../runtime/catlog-server main.go
cd ..
```

### Running in Development Mode
```bash
cd src
go run main.go
```

## Cross-Platform Support

### Windows
- Install Go from https://golang.org/dl/
- Run `./catlog install` (requires PowerShell or Git Bash)
- Access at `http://localhost:8008`

### macOS
- Install Go via Homebrew: `brew install go`
- Run `./catlog install`
- Access at `http://localhost:8008`

### Linux
- Install Go: `sudo apt-get install golang-go` (or equivalent)
- Run `./catlog install`
- Access at `http://localhost:8008`
- Optional: Run `./catlog nginx setup` for nginx reverse proxy

## API Endpoints

### WebSocket
- `ws://localhost:8008/ws?file=<path>` - Real-time log streaming

### HTTP
- `GET /` - Landing page
- `GET /login` - Login page
- `POST /login` - Login submission
- `GET /logout` - Logout
- `GET /app` - Log file list (requires authentication)
- `GET /api/loadmore?file=<path>&offset=<n>&limit=<n>` - Load historical logs

## License

See LICENSE file for details.

## Support

For issues or questions, please check the configuration or create an issue in the repository.
