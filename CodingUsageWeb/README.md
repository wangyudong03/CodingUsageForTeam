# Coding Usage Web

**Shared Usage Dashboard** - A web platform for monitoring and sharing AI IDE (Cursor/Trae) usage statistics across teams and community.

![Coding Usage Web](docs/screenshot.png)

## ✨ Features

- **📊 Public Plaza**: Browse community-shared usage statistics (by email account)
- **👤 Personal Dashboard**: Track your own usage across multiple devices
- **🔐 Privacy-First**: Bind API keys locally, toggle public/private visibility
- **📱 Multi-Device Support**: Aggregate stats from all your synced devices
- **📈 Usage Trends**: 30-day historical charts for usage monitoring
- **🎯 Zero Login**: No registration needed - bind client API keys directly
- **🔄 Real-Time Sync**: Automatic updates from IDE extensions via API
- **🌐 Team Ready**: Optional integration with main server for team deployments

## How It Works

```
┌─────────────┐         ┌──────────────┐         ┌─────────────┐
│   Browser   │         │     IDE      │         │   Coding    │
│  Extension  │────────▶│  Extension   │────────▶│  Usage Web  │
│             │ Token   │              │  API    │             │
└─────────────┘         └──────────────┘         └─────────────┘
     │                         │                         │
     │ Auto-extract            │ Auto-generate          │ Store &
     │ session token           │ client API key         │ Display
     └─────────────────────────┴─────────────────────────┘
```

1. **Browser Extension**: Extracts session token from Cursor/Trae dashboard
2. **IDE Extension**: Monitors usage and reports to web server via client API key
3. **Web Server**: Aggregates and displays usage data with privacy controls

## Quick Start

### Installation

```bash
# Install dependencies
cd tool-cursor-usage-web
npm install

# Start server
npm start
```

### Access

- **Standalone**: `http://localhost:3000/`
- **Integrated**: `http://localhost:3000/cursor-usage-web`

### Bind Your API Key

1. Install IDE extension (Cursor Usage or Trae Usage)
2. Get your client API key from extension settings or use command:
   - Command Palette: "Copy Client API Key"
   - Format: `ck_` + 32-character MD5 hash
3. Visit web dashboard and click "Bind API Key"
4. Paste your key and confirm

## Running Modes

### 1. Standalone Mode (Development)

```bash
cd tool-cursor-usage-web
npm start
# or
npm run dev
```

- Direct access at `http://localhost:3000/`
- `BASE_PATH` is empty
- `STANDALONE=true`

### 2. Integrated Mode (Production)

```bash
# Started by main server with environment variables
PORT=3000 BASE_PATH=/cursor-usage-web STANDALONE=false node server.js
```

- Access at `http://localhost:3000/cursor-usage-web`
- Proxied through main server
- Configured via environment variables

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `PORT` | `3000` | Server port |
| `BASE_PATH` | `/cursor-usage-web` (integrated)<br>`''` (standalone) | Application base path |
| `STANDALONE` | `true` | Standalone mode flag |

## API Endpoints

### Usage Reporting

```bash
POST /api/usage
Content-Type: application/json

{
  "client_token": "ck_xxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "email": "user@example.com",
  "platform": "darwin",
  "app_name": "Cursor",
  "membership_type": "pro",
  "total_usage": 2000,
  "used_usage": 500,
  "bonus_usage": 100,
  "remaining_usage": 1600,
  "expire_time": 1735689600000,
  "host": "api.cursor.sh"
}
```

### Health Check

```bash
GET /api/health

Response:
{
  "status": "ok",
  "service": "coding-usage",
  "version": "1.0.0",
  "timestamp": 1735084800000
}
```

### Ping (Keep-Alive)

```bash
POST /api/ping
Content-Type: application/json

{
  "client_token": "ck_xxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "active": true
}
```

### Validate API Key

```bash
POST /validate-key
Content-Type: application/json

{
  "api_key": "ck_xxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

## Features in Detail

### Public Plaza

- Browse all publicly shared usage statistics
- Grouped by email account (multiple devices aggregated)
- Sort by activity time or usage amount
- Ascending/descending order
- Device count badges
- Real-time online status indicators

### Personal Dashboard

- View your bound API keys across all devices
- Individual device statistics with usage breakdown
- 30-day usage trend charts
- Toggle public/private visibility per key
- One-click unbind functionality
- Device-specific monitoring

### Privacy Controls

- **Private by default**: New keys are private until you enable public sharing
- **Per-key control**: Toggle visibility for each device independently
- **Local storage**: API keys stored in browser localStorage + cookie
- **No registration**: Bind keys without creating accounts

### Usage Statistics

Each card displays:
- Email/account identifier
- Membership type (Free/Pro/Business)
- Expiration date and time
- Total/used/bonus/remaining usage
- Progress bar with color coding:
  - Green: < 70% used
  - Yellow: 70-90% used
  - Red: > 90% used
- Last activity timestamp
- Online/offline status
- Platform logo (Cursor/Trae)
- Device count for multi-device accounts

### Usage Trends

- 30-day historical chart with SVG rendering
- Smooth interpolation for missing days
- Interactive hover states
- Max value indicators
- Auto-scaling Y-axis
- Responsive design

## Database Schema

### API Keys Table

```sql
CREATE TABLE vibe_usage_api_keys (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  api_key VARCHAR(64) UNIQUE NOT NULL,
  email VARCHAR(255),
  platform VARCHAR(64),
  app_name VARCHAR(64),
  created_at BIGINT NOT NULL,
  last_ping_at BIGINT DEFAULT NULL,
  online TINYINT DEFAULT 0,
  is_public TINYINT DEFAULT 0
);
```

### Usage Reports Table

```sql
CREATE TABLE vibe_usage_usage_reports (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  api_key VARCHAR(64) NOT NULL,
  email VARCHAR(255),
  expire_time BIGINT,
  membership_type VARCHAR(32),
  total_usage BIGINT,
  used_usage BIGINT,
  bonus_usage BIGINT DEFAULT 0,
  remaining_usage BIGINT,
  host VARCHAR(255),
  platform VARCHAR(64),
  created_at BIGINT NOT NULL,
  FOREIGN KEY(api_key) REFERENCES vibe_usage_api_keys(api_key)
);
```

## Integration with IDE Extensions

### Client API Key Generation

```javascript
// Generated from device fingerprint
const crypto = require('crypto');
const os = require('os');

function generateClientToken() {
  const hostname = os.hostname();
  const networkInterfaces = os.networkInterfaces();

  // Get MAC address
  let mac = '';
  for (const name of Object.keys(networkInterfaces)) {
    for (const iface of networkInterfaces[name]) {
      if (!iface.internal && iface.mac !== '00:00:00:00:00:00') {
        mac = iface.mac;
        break;
      }
    }
    if (mac) break;
  }

  const identifier = `${hostname}-${mac}`;
  const hash = crypto.createHash('md5').update(identifier).digest('hex');
  return `ck_${hash}`;
}
```

### Auto-Discovery

IDE extensions can discover available web servers:

```javascript
// Check if server is Coding Usage service
const response = await fetch('http://localhost:3000/api/health');
const data = await response.json();

if (data.service === 'coding-usage') {
  // Auto-configure team server URL
  vscode.workspace.getConfiguration('cursorUsage')
    .update('teamServerUrl', 'http://localhost:3000', true);
}
```

## Test Accounts

Load sample data for development:

```json
// test_accounts.json
[
  {
    "api_key": "ck_test123456789abcdef123456789abc",
    "email": "test@example.com",
    "platform": "darwin",
    "app_name": "Cursor",
    "usage_reports": [
      {
        "email": "test@example.com",
        "membership_type": "pro",
        "total_usage": 2000,
        "used_usage": 500,
        "remaining_usage": 1500,
        "created_at": 1735084800000
      }
    ]
  }
]
```

## GitHub-Style Design

- Clean, professional interface inspired by GitHub
- Octicons-compatible SVG icons
- Responsive grid layout (5 columns on desktop, 1 on mobile)
- Smooth animations and transitions
- Accessible color schemes
- Mobile-first responsive design

## Troubleshooting

### API Key Not Found

**Symptom**: "API Key not found" error when binding

**Solution**:
1. Ensure IDE extension has reported data at least once
2. Check client API key matches format: `ck_` + 32 chars
3. Verify extension is running and connected

### Usage Data Not Updating

**Symptom**: Statistics not refreshing in dashboard

**Solution**:
1. Check IDE extension ping status (should be green)
2. Verify network connectivity
3. Check browser console for API errors
4. Refresh page to force data reload

### Multiple Devices Not Aggregating

**Symptom**: Same email showing as separate entries

**Solution**:
1. Ensure email is set in extension settings
2. Check that both devices have reported data
3. Verify email matches exactly across devices
4. Wait for next sync cycle (devices update independently)

### Offline Status Always Shown

**Symptom**: Device shows offline despite extension running

**Solution**:
1. Check last ping timestamp (updates every 60 seconds)
2. Verify extension ping is not disabled
3. Check server receives ping requests
4. Offline threshold is 2 minutes of inactivity

## Project Structure

```
tool-cursor-usage-web/
├── server.js              # Express server & API routes
├── package.json           # Dependencies & scripts
├── db.sqlite              # SQLite database (auto-created)
├── test_accounts.json     # Sample data (optional)
├── public/             
│   ├── style.css         # GitHub-style CSS
│   └── web_logo.png      # Dashboard icon
├── logo/
│   ├── cursor.png        # Cursor platform logo
│   └── trae.png          # Trae platform logo
└── views/
    ├── plaza.ejs         # Public plaza page
    └── me.ejs            # Personal dashboard
```

## Development

### Adding New Features

1. **New API Endpoint**:
```javascript
app.post('/api/new-endpoint', async (req, res) => {
  // Your logic here
  res.json({ ok: true });
});
```

2. **New Database Table**:
```javascript
await run(`CREATE TABLE IF NOT EXISTS new_table (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  // ...fields
)`);
```

3. **New View**:
```javascript
app.get('/new-page', async (req, res) => {
  res.render('new-page', { data: {} });
});
```

## Security Considerations

- API keys are device-specific (hostname + MAC based)
- No authentication required for read-only plaza
- Write operations require valid client API key
- Cookie-based session for bound keys (1 year expiry)
- SQL injection prevention via parameterized queries
- XSS protection through EJS auto-escaping

## Performance Optimization

- SQLite indexes on frequently queried columns
- Smart sync: only queries API when local changes detected
- Debounced ping updates (120 second threshold)
- Efficient aggregation queries for multi-device accounts
- Static asset caching
- Minimal external dependencies

## Related Projects

- **[Cursor Usage Extension](../CursorUsage)**: IDE extension for Cursor/Trae
- **[Browser Extension](../CodingUsageBrowserExtension)**: Automatic token extraction

## Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing`)
3. Commit changes (`git commit -am 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing`)
5. Open Pull Request

## License

MIT License - see LICENSE file for details

## Support

- **Issues**: Open GitHub issue for bugs or feature requests
- **Discussions**: Use GitHub Discussions for questions
- **Email**: Contact maintainers for private inquiries

---

**Made for AI developers who want to share and track their usage across teams** 🚀