# ZeroTwo HubSpot App

This directory contains the configuration for the ZeroTwo HubSpot application integration for MCP (Model Context Protocol) access.

## Overview

The ZeroTwo HubSpot App is a **user-level OAuth marketplace app** that enables AI-powered interactions with HubSpot CRM data through the HubSpot MCP server. This app is specifically configured to work with MCP clients and AI assistants for natural language CRM interactions.

## Features

- **Contact Management**: Search, read, and manage HubSpot contacts
- **Company Management**: Access and update company information
- **Deal Management**: Track and manage deals through AI conversations
- **Ticket Management**: Handle support tickets efficiently
- **AI-Powered**: Natural language interface for all CRM operations

## Setup Instructions

### Prerequisites

1. A HubSpot developer account ([Create one here](https://developers.hubspot.com/))
2. HubSpot CLI version **7.6.0 or later** installed:
   ```bash
   npm install -g @hubspot/cli
   # Verify version
   hs --version
   ```
3. OAuth server setup for handling the OAuth flow
4. An MCP client (e.g., Claude Desktop, Cline, or MCP Inspector for testing)

### Step 1: Upload the App

1. **Initialize the HubSpot CLI (if not already done):**
   ```bash
   hs init
   ```

2. **Navigate to the project directory:**
   ```bash
   cd /path/to/ZeroTwoHubSpotApp
   ```

3. **Upload the app configuration:**
   ```bash
   hs project upload
   ```
   
   This will upload your app to HubSpot using platform version **2025.2** with the required MCP configuration.

### Step 2: Sign the Acceptable Use Policy

1. Navigate to your app in the [HubSpot App Management](https://app.hubspot.com/developer) page
2. Go to the **Distribution** tab
3. Click **Begin publishing**
4. Review and sign the Acceptable Use Policy (AUP)
5. Close the right panel without creating a marketplace listing
6. Copy the **install URL** from the distribution settings

### Step 3: Install the App

1. Use the install URL to install the app in your HubSpot account
2. **Important**: Do NOT include any scopes as query parameters in the URL
3. The OAuth flow will:
   - Generate an **access token** for authenticating with the MCP server
   - Generate a **refresh token** for obtaining new access tokens
4. Save these tokens securely

### Step 4: Configure Your MCP Client

Connect your MCP client to the HubSpot MCP server:

**MCP Server Configuration:**
- **URL**: `https://mcp.hubspot.com/`
- **Transport Type**: Streamable HTTP
- **Bearer Token**: Your app's access token (from OAuth flow)
- **Client ID**: Your app's client ID

**Example configuration for Claude Desktop (`~/Library/Application Support/Claude/claude_desktop_config.json`):**
```json
{
  "mcpServers": {
    "hubspot": {
      "url": "https://mcp.hubspot.com/",
      "transport": {
        "type": "streamable-http",
        "headers": {
          "Authorization": "Bearer YOUR_ACCESS_TOKEN_HERE",
          "X-Client-Id": "YOUR_CLIENT_ID_HERE"
        }
      }
    }
  }
}
```

### Project Structure

```
ZeroTwoHubSpotApp/
├── hsproject.json                    # Platform version 2025.2
├── src/
│   └── app/
│       ├── zerotwo-hsmeta.json      # App configuration with MCP settings
│       ├── app-logo.png             # App logo
│       └── extensions/
│           ├── oauth.json           # OAuth configuration
│           └── listing.json         # Marketplace listing
├── package.json
├── README.md
├── SETUP_GUIDE.md
└── env.example
```

### Configuration

The app configuration is defined in `zerotwo-hsmeta.json`:

- **Platform Settings**:
  - `distribution`: `"marketplace"` (required for MCP)
  - `isUserLevel`: `true` (enables per-user installation)
  - `platformVersion`: `"2025.2"` (minimum version for MCP)

- **Required Scopes** (minimum for MCP access):
  - `oauth` - OAuth authentication
  - `crm.objects.contacts.read`
  - `crm.objects.companies.read`
  - `crm.objects.deals.read`
  - `crm.objects.tickets.read` (required)

- **Optional Scopes** (user can choose during install):
  - Write access for contacts, companies, deals, tickets
  - Read access for products, orders, line items, invoices, quotes, subscriptions, carts
  - User and owner information
  - Schema read access

- **Redirect URLs**: OAuth callback endpoints
  - `http://localhost` (for testing)
  - `http://localhost:3000/oauth-callback`
  - `https://oauth.zerotwo.ai/oauth-callback`

### OAuth Flow for MCP

1. User clicks the install URL
2. Redirects to HubSpot authorization page at `https://mcp.hubspot.com/oauth/authorize/user`
3. User selects which scopes to authorize (based on their permissions)
4. User authorizes the app
5. HubSpot redirects back with authorization code
6. Your OAuth server exchanges code for:
   - **Access token** (for API/MCP requests)
   - **Refresh token** (for getting new access tokens)
7. Store tokens securely for MCP client configuration

**Important Notes:**
- Each user must install the app individually (user-level app)
- MCP access is limited to the scopes granted during installation
- MCP access is also limited by the user's HubSpot permissions
- Refresh tokens will need to rotate when OAuth 2.1 support is added (later in 2025)

### Testing with MCP Inspector

Before using with production MCP clients, test your configuration:

1. **Install MCP Inspector:**
   ```bash
   npx @modelcontextprotocol/inspector
   ```

2. **Configure the connection:**
   - Transport Type: `Streamable HTTP`
   - URL: `https://mcp.hubspot.com/`
   - Bearer Token: Your access token
   - Client ID: Your app's client ID

3. **Test the connection:**
   - Click **Connect**
   - Navigate to **Tools** → **List Tools**
   - Select `get_user_details`
   - Click **Run tool**
   - Verify you receive user and account information

### Available MCP Tools

After successful connection, these tools become available through natural language:

1. **get_user_details** - Get authenticated user and account information
2. **search_contacts** - Search and retrieve contacts from HubSpot CRM
3. **search_companies** - Search and retrieve companies from HubSpot CRM
4. **search_deals** - Search and retrieve deals from HubSpot CRM
5. **search_tickets** - Search and retrieve support tickets from HubSpot

**Note:** The HubSpot MCP server currently supports **read-only access** to CRM objects and is based on the CRM search API (no vector search).

## Development

### Testing Locally

1. **Set up OAuth server** - You need a server to handle the OAuth callback and token exchange. See [HubSpot's sample OAuth app](https://github.com/hubspotdev/oauth-quickstart-nodejs) for a basic implementation.

2. **Configure local redirect URLs** - The app is already configured with:
   - `http://localhost`
   - `http://localhost:3000/oauth-callback`

3. **Test with MCP Inspector:**
   ```bash
   npx @modelcontextprotocol/inspector
   ```

4. **Update configuration:**
   - Modify `zerotwo-hsmeta.json` as needed
   - Run `hs project upload` to apply changes

### Deployment

1. **Update redirect URLs** in `zerotwo-hsmeta.json`:
   ```json
   "redirectUrls": [
     "https://oauth.zerotwo.ai/oauth-callback",
     "http://localhost:3000/oauth-callback"
   ]
   ```

2. **Upload to HubSpot:**
   ```bash
   hs project upload
   ```

3. **Update MCP clients** with production OAuth tokens

4. **Monitor OAuth flow** and handle token refreshes

## Security Notes

- **Client ID and Client Secret** are stored in environment variables on your OAuth server
- **Access tokens** should be encrypted when stored
- **Refresh tokens** are single-use and must be rotated (especially after OAuth 2.1 migration)
- **Token refresh** should happen automatically when access tokens expire
- **All API calls** use HTTPS with Bearer authentication
- **User-level permissions** ensure users can only access data they have permission to view in HubSpot
- **Scope limitations** restrict what data the MCP server can access based on installation choices

## Future Changes (2025)

HubSpot will implement OAuth 2.1 requirements:
- **PKCE** (Proof Key for Code Exchange) will be required
- **Refresh token rotation** will be enforced (single-use refresh tokens)
- Migration guidance will be provided ahead of the change

Design your OAuth implementation to handle refresh tokens that change with each renewal.

## Resources

### Documentation
- [HubSpot MCP Server Integration Guide](https://developers.hubspot.com/docs/apps/developer-platform/build-apps/integrate-with-hubspot-mcp-server)
- [HubSpot App Configuration Reference](https://developers.hubspot.com/docs/apps/developer-platform/build-apps/app-configuration)
- [Model Context Protocol Specification](https://modelcontextprotocol.io)
- [HubSpot OAuth Documentation](https://developers.hubspot.com/docs/api/oauth)

### Example Apps
- [HubSpot User-Level App Template](https://github.com/hubspotdev/user-level-app-template)
- [HubSpot OAuth Quickstart](https://github.com/hubspotdev/oauth-quickstart-nodejs)

### MCP Clients
- [Claude Desktop](https://claude.ai/download)
- [MCP Inspector](https://github.com/modelcontextprotocol/inspector) (for testing)
- [List of MCP-compatible applications](https://modelcontextprotocol.io/clients)

### Support
- ZeroTwo Support: support@zerotwo.ai
- HubSpot Developer Forums: [community.hubspot.com](https://community.hubspot.com/t5/APIs-Integrations/ct-p/apis)

## License

Copyright © 2024 ZeroTwo AI. All rights reserved.

