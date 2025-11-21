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
- 🛡️ Only `.log` files allowed - prevents unauthorized file access
- ⚡ Lightweight and fast

---

## Local Setup (Windows/macOS/Linux)

### Prerequisites

- **Go 1.16+** - Download from https://golang.org/dl/
- **Git** (optional, for cloning the repository)

### Installation Steps

1. **Clone the repository:**
```bash
git clone <repository-url>
cd loged
```

2. **Copy configuration file:**
```bash
cp example.config.yml config.yml
```

3. **Edit config.yml (optional):**
```bash
# Update default credentials and log file paths as needed
nano config.yml
```

4. **Install dependencies and build:**
```bash
./catlog install
```

This will:
- Check if Go is installed
- Install Go dependencies
- Build the application binary

5. **Start the server:**
```bash
./catlog start
```

6. **Access the application:**
Open your browser and go to: `http://localhost:8008`

**Default Credentials:**
- Username: `admin`
- Password: `catlog123`

### Local Commands

```bash
./catlog install              # Install dependencies and build
./catlog start                # Start the server
./catlog stop                 # Stop the server
./catlog status               # Check if running
./catlog restart              # Restart the server
./catlog update               # Update and rebuild
./catlog uninstall            # Remove all files
```

### Local Configuration

Edit `config.yml`:
```yaml
port: 8008
base_url: ""                  # Leave empty for localhost
ssl:
  enabled: false
auth:
  enabled: true
  users:
    - username: "admin"
      password: "catlog123"
      role: "admin"
log_files:
  - name: "Application Log"
    path: "./runtime/catlog.log"
```

---

## Linux Server Setup

### Prerequisites

- **Linux OS** (Ubuntu, Debian, CentOS, Fedora, etc.)
- **Go 1.16+** - Will be installed automatically
- **Nginx** (optional, for reverse proxy) - Will be installed automatically
- **sudo access** - Required for nginx and SSL setup

### Installation Steps

1. **Clone the repository:**
```bash
git clone <repository-url>
cd loged
```

2. **Copy configuration file:**
```bash
cp example.config.yml config.yml
```

3. **Edit config.yml (optional):**
```bash
# Update default credentials and log file paths as needed
nano config.yml
```

4. **Grant permissions, install dependencies and build:**
```bash
chmod +x ./catlog
./catlog install
```

This will:
- Install Go (if not already installed)
- Install Go dependencies
- Build the application binary
- Setup auto-start on boot (via crontab)

5. **Start the server:**
```bash
./catlog start
```

6. **Access the application:**
Open your browser and go to: `http://<server-ip>:8008`

### Linux Commands

```bash
./catlog install              # Install dependencies and build
./catlog start                # Start the server
./catlog stop                 # Stop the server
./catlog status               # Check if running
./catlog restart              # Restart the server
./catlog update               # Update and rebuild
./catlog uninstall            # Remove all files
```

### Linux Configuration (Standalone)

Edit `config.yml`:
```yaml
port: 8008
base_url: ""                  # Leave empty for standalone
ssl:
  enabled: false
auth:
  enabled: true
  users:
    - username: "admin"
      password: "catlog123"
      role: "admin"
log_files:
  - name: "System Log"
    path: "/var/log/syslog"
  - name: "Nginx Access"
    path: "/var/log/nginx/access.log"
```

---

## Linux Server Setup with Nginx

### Prerequisites

- **Linux OS** (Ubuntu, Debian, CentOS, Fedora, etc.)
- **Go 1.16+** - Will be installed automatically
- **Nginx** - Will be installed automatically
- **OpenSSL** - Usually pre-installed
- **sudo access** - Required for nginx and SSL setup

### Installation Steps

1. **Clone the repository:**
```bash
git clone <repository-url>
cd loged
```

2. **Copy configuration file:**
```bash
cp example.config.yml config.yml
```

3. **Edit config.yml (optional):**
```bash
# Update default credentials and log file paths as needed
nano config.yml
```

4. **Grant permissions, install dependencies and build:**
```bash
chmod +x ./catlog
./catlog install
```

5. **Setup Nginx with SSL:**
```bash
./catlog nginx setup
```

This will:
- Install nginx (if not already installed)
- Generate self-signed SSL certificates
- Configure nginx as a reverse proxy
- Update config.yml with `base_url: "/catlog"` and enable SSL
- Set timezone to IST
- Restart nginx

6. **Start the server:**
```bash
./catlog start
```

7. **Access the application:**
- HTTPS: `https://your-server-ip/catlog`
- HTTP: `http://your-server-ip/catlog` (redirects to HTTPS)

**Note:** Browser will show SSL warning for self-signed certificate. Click "Advanced" → "Proceed" to continue.

### Linux with Nginx Commands

```bash
./catlog install              # Install dependencies and build
./catlog start                # Start the server
./catlog stop                 # Stop the server
./catlog status               # Check if running
./catlog restart              # Restart the server
./catlog update               # Update and rebuild
./catlog uninstall            # Remove all files

./catlog nginx setup          # Install nginx, generate SSL, and configure reverse proxy
./catlog nginx restart        # Restart nginx
./catlog nginx stop           # Stop nginx
./catlog nginx remove         # Remove catlog nginx configuration
```

### Linux with Nginx Configuration

Edit `config.yml`:
```yaml
port: 8008
base_url: "/catlog"           # Set to /catlog for nginx
ssl:
  enabled: true               # Enable SSL
  cert_path: "/etc/ssl/certs/catlog.crt"
  key_path: "/etc/ssl/private/catlog.key"
auth:
  enabled: true
  users:
    - username: "admin"
      password: "catlog123"
      role: "admin"
    - username: "backend"
      password: "backend123"
      role: "backend"
      allowed_paths:
        - "/var/log/app/*"
    - username: "devops"
      password: "devops123"
      role: "devops"
      allowed_paths:
        - "/var/log/nginx/*"
        - "/var/log/syslog"
log_files:
  - name: "System Log"
    path: "/var/log/syslog"
  - name: "Nginx Access"
    path: "/var/log/nginx/access.log"
  - name: "Nginx Error"
    path: "/var/log/nginx/error.log"
```

---

## Configuration Reference

### config.yml Options

```yaml
port: 8008                              # Port to run on
base_url: ""                            # "" for localhost, "/catlog" for nginx
ssl:
  enabled: false                        # true when using nginx with SSL
  cert_path: "/etc/ssl/certs/catlog.crt"
  key_path: "/etc/ssl/private/catlog.key"
auth:
  enabled: true
  users:
    - username: "admin"
      password: "admin123"
      role: "admin"                     # admin has access to all logs
      allowed_paths: []
    - username: "user"
      password: "user123"
      role: "user"
      allowed_paths:
        - "/var/log/app/*"              # Supports wildcards
        - "/var/log/nginx/*"
log_files:
  - name: "Display Name"
    path: "/path/to/log/file"
```

### User Roles

- **admin** - Access to all log files
- **Custom roles** - Define custom roles with path restrictions

---

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

### Nginx Issues (Linux)
1. Check nginx status: `sudo systemctl status nginx`
2. Test nginx config: `sudo nginx -t`
3. Check nginx logs: `sudo tail -f /var/log/nginx/error.log`

### SSL Certificate Issues (Linux)
Regenerate certificates:
```bash
./catlog nginx remove
./catlog nginx setup
```

---

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

---

## Security Considerations

- **File Type Restriction** - Only `.log` files are allowed. Any attempt to access other file types will be rejected
- Change default passwords in config.yml
- Use strong passwords for user accounts
- When using nginx, always enable SSL
- Restrict file access permissions in config.yml using role-based access control
- Run on a secure network
- For production, use proper SSL certificates (not self-signed)

---

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

---

## License

See LICENSE file for details.

## Support

For issues or questions, please check the configuration or create an issue in the repository.
