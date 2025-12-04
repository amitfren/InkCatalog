# Mobile Testing Setup Guide

This guide will help you access your Ink Grimoire portfolio from your mobile device on the same WiFi network.

## Quick Setup Steps

### 1. Find Your Computer's IP Address

#### macOS
```bash
ifconfig | grep "inet " | grep -v 127.0.0.1
```
Look for something like: `inet 192.168.1.XXX`

Or use the GUI:
1. System Settings → Network
2. Click on your WiFi connection
3. Look for "IP Address"

#### Windows
```cmd
ipconfig
```
Look for "IPv4 Address" under your WiFi adapter (usually starts with 192.168.x.x)

#### Linux
```bash
ip addr show | grep "inet " | grep -v 127.0.0.1
```

### 2. Start Your Server

If using **live-server**:
```bash
cd /Users/amitfrenkel/InkCatalog
live-server
```

If using **Python**:
```bash
# Python 3
python3 -m http.server 8000

# Python 2
python -m SimpleHTTPServer 8000
```

If using **Node.js http-server**:
```bash
npx http-server -p 8080
```

### 3. Access From Mobile

Replace `localhost` or `127.0.0.1` with your IP address:

**Example:**
- Desktop URL: `http://localhost:5500/index.html`
- Mobile URL: `http://192.168.1.100:5500/index.html`
  (Replace `192.168.1.100` with YOUR computer's IP)

### 4. Generate QR Code

1. Open the portfolio in your desktop browser
2. Click the **"Stand QR"** button (top-right)
3. The QR modal now shows:
   - Current URL being used
   - Instructions to replace localhost with your IP
   - The QR code (which you'll need to regenerate after changing the URL)

### 5. Create Manual QR Code for Network URL

**Option A: Use QR Code Generator Website**
1. Go to https://www.qr-code-generator.com/
2. Enter your network URL (e.g., `http://192.168.1.100:5500/index.html`)
3. Download and display the QR code

**Option B: Update the App Dynamically**
1. Open Artist Mode (gear icon)
2. Note your computer's IP address
3. Access via: `http://YOUR_IP:PORT/index.html`
4. Click "Stand QR" - it will automatically show the correct URL

## Troubleshooting

### QR Code Doesn't Scan
- **Check WiFi**: Ensure both devices are on the same network
- **Check Firewall**: Your computer's firewall might be blocking connections
- **Check Port**: Make sure the port number matches (common: 5500, 8000, 8080)
- **Use Manual URL**: Type the URL directly into your mobile browser

### Firewall Issues (macOS)
```bash
# Temporarily allow incoming connections (use with caution)
# Or add an exception for your dev server in System Settings → Network → Firewall
```

### Firewall Issues (Windows)
1. Windows Security → Firewall & network protection
2. Allow an app through firewall
3. Add your server (Python, Node.js, etc.)

### Connection Refused
- Server might not be running - check terminal
- Wrong port number - verify in browser URL bar
- IP address changed - WiFi networks can reassign IPs

## Network IP Detection Script

If you want to automatically detect and use your network IP, you can create a small helper:

```bash
#!/bin/bash
# save as start-server.sh

IP=$(ipconfig getifaddr en0)  # macOS WiFi
echo "🌐 Starting server..."
echo "📱 Mobile URL: http://$IP:5500/index.html"
live-server --host=$IP
```

Make it executable:
```bash
chmod +x start-server.sh
./start-server.sh
```

## Pro Tips

1. **Bookmark on Mobile**: Save the network URL as a bookmark for easy access
2. **Use QR Stand**: Print the QR code and display it in your tattoo shop
3. **Static IP**: Configure your computer to use a static IP so the URL never changes
4. **Port Forwarding**: For external access (outside your network), set up port forwarding on your router

## Security Note

This setup is for LOCAL NETWORK ONLY. Anyone on your WiFi can access the portfolio. For production/public access, deploy to a proper hosting service like Netlify, Vercel, or GitHub Pages.
