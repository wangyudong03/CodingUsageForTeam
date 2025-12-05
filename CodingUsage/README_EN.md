# Coding Usage

**Effortless AI Usage Tracking** - No manual cookie extraction needed. This extension monitors your Cursor/Trae AI usage directly within your editor, supporting team mode for collaborative usage tracking across multiple accounts.

**English Version | [中文版](README.md)**

<div align="left">
  <img src="https://raw.githubusercontent.com/lasoons/CodingUsage/refs/heads/main/CodingUsage/img/statebar.png" alt="Status Bar Configuration" width="400">
</div>

Currently supports [Cursor](cursor:extension/whyuds.coding-usage) and [Trae International](trae:extension/whyuds.coding-usage)

## Configuration Demo

<div align="center" style="display: flex; gap: 10px; overflow-x: auto; white-space: nowrap;">
  <img src="https://raw.githubusercontent.com/lasoons/CodingUsage/refs/heads/main/CodingUsage/img/standalone.gif" alt="Basic Configuration Demo" width="400" style="display: inline-block; flex-shrink: 0;">
  <img src="https://raw.githubusercontent.com/lasoons/CodingUsage/refs/heads/main/CodingUsage/img/teamserver.gif" alt="Team Server Connection Demo" width="400" style="display: inline-block; flex-shrink: 0;"> 
</div>

<p align="center"><em>Basic Configuration Demo | Team Server Connection Demo</em></p>

## Features

- **Configuration Setup**: Browser extension automatically fetches tokens from the official website, copies to clipboard, and IDE extension automatically reads clipboard for configuration
- **Second-level Usage Updates**: Monitors local AI conversations every 10 seconds, only calls official usage API when conversation changes
- **Team Collaboration**: Optional team server integration for shared usage tracking
- **Auto Discovery**: Automatically finds and configures available team servers, currently deployed to [demo server](http://115.190.183.157:3000/)
- **Usage Display**: Detailed tooltips including usage details, progress bars, and billing cycle information

## Quick Start

### 1. Install IDE Extension
- <a href="cursor:extension/whyuds.coding-usage">Cursor Extension Store - CodingUsage</a>
- <a href="trae:extension/whyuds.coding-usage">Trae Extension Store - CodingUsage</a>

### 2. Double-click the status bar at the bottom of the window

<div align="center" style="display: flex; gap: 10px; overflow-x: auto; white-space: nowrap;">
  <img src="https://raw.githubusercontent.com/lasoons/CodingUsage/refs/heads/main/CodingUsage/img/doubleclickconfig.png" alt="Double-click Status Bar Configuration" width="400" style="display: inline-block; flex-shrink: 0;">
  <img src="https://raw.githubusercontent.com/lasoons/CodingUsage/refs/heads/main/CodingUsage/img/quickpick.png" alt="QuickPick Menu" width="400" style="display: inline-block; flex-shrink: 0;">
</div>

<p align="center"><em>Double-click Status Bar Configuration | QuickPick Menu</em></p>

### 3. Install browser extension via QuickPick menu

### 4. Use browser extension or QuickPick to navigate to official website

### 5. Return and automatically configure SessionToken

## Team Features (Optional)

Configure team server URL to enable:
- Shared usage tracking among team members
- Historical usage data and analytics
- Current online status
- Multi-account usage queries

### Personal Multi-account Query

<div align="center" style="display: flex; gap: 10px; overflow-x: auto; white-space: nowrap;">
  <img src="https://raw.githubusercontent.com/lasoons/CodingUsage/refs/heads/main/CodingUsage/img/mystats.png" alt="Personal Multi-account Query" width="400" style="display: inline-block; flex-shrink: 0;">
  <img src="https://raw.githubusercontent.com/lasoons/CodingUsage/refs/heads/main/CodingUsage/img/plza.png" alt="Team Account Data" width="400" style="display: inline-block; flex-shrink: 0;">
</div>

<p align="center"><em>Personal Multi-account Query | Team Account Data</em></p>

## FAQ

#### Why does Cursor Pro subscription show a total of $45?
Currently, Pro total usage is: API billing $20 fixed + $25 Bonus, Auto billing: $150

#### How often is data updated?
The extension checks local database changes every 10 seconds and only calls the official API when changes are detected, ensuring real-time updates while avoiding frequent requests.

#### Is the team server required?
No. The team server is an optional feature for team collaboration and historical data tracking, as well as personal multi-account tracking.
The current configuration server is only for public demo use and is disabled by default. We recommend deploying your own team server on your internal network according to the submission protocol.

#### Team Mode Submission Data Format

```json
{"client_token":"ck_eb33d6fb4d5b541d28a0d042b0e4ba56","email":"aisrv0615@qiyi.com","expire_time":1767060076000,"membership_type":"pro","api_spend":2002,"api_limit":4500,"auto_spend":0,"auto_limit":15000,"host":"IQ275CG42123NJ","platform":"win32","app_name":"Cursor"}
```

#### Is my token secure?
Tokens are stored locally only. The extension will not send your tokens to any servers other than the official API.

---

## Support

For questions or suggestions, please submit an [Issue](https://github.com/lasoons/CodingUsage/issues) or [Pull Request](https://github.com/lasoons/CodingUsage/pulls).