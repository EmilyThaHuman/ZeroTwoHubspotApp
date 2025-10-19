# Next Steps for HubSpot MCP Integration

## ✅ Completed
1. ✅ Configured app for MCP access (platform version 2025.2)
2. ✅ Uploaded app to HubSpot successfully (Build #6)

## 📋 Remaining Steps

### Step 1: Get Your App Details

Visit your app in HubSpot:
- **Project URL**: https://app.hubspot.com/developer-projects/244000548/project/zerotwo-ai-assistant
- **Build #6 Details**: https://app.hubspot.com/developer-projects/244000548/project/zerotwo-ai-assistant/activity/build/6

From the app settings, you'll need to collect:
- **Client ID** (you'll see this in the app settings)
- **Client Secret** (you'll see this in the app settings)

### Step 2: Sign the Acceptable Use Policy (AUP)

1. Go to your app management page: https://app.hubspot.com/developer/244000548
2. Find your app "ZeroTwo AI Assistant"
3. Click on the app to open its settings
4. Navigate to the **Distribution** tab
5. Click **Begin publishing**
6. Review and **sign the Acceptable Use Policy (AUP)**
   - This is required to install the app outside of developer test accounts
7. After signing, **close the right panel** (don't complete the marketplace listing)
8. **Copy the install URL** from the distribution settings

### Step 3: Set Up OAuth Server

Before you can install the app, you need an OAuth server to handle the callback and token exchange.

**Option A: Use HubSpot's Sample OAuth App (Recommended for Quick Start)**
```bash
git clone https://github.com/hubspotdev/oauth-quickstart-nodejs.git
cd oauth-quickstart-nodejs
npm install
```

Create a `.env` file:
```env
CLIENT_ID=your_client_id_here
CLIENT_SECRET=your_client_secret_here
REDIRECT_URI=http://localhost:3000/oauth-callback
PORT=3000
```

Run the OAuth server:
```bash
npm start
```

**Option B: Build Your Own OAuth Server**

Your OAuth server needs to:
1. Handle the authorization callback at your configured redirect URL
2. Exchange the authorization code for tokens
3. Store the access token and refresh token securely
4. Handle token refresh when access tokens expire

Key endpoints:
- Authorization: `https://app.hubspot.com/oauth/authorize`
- Token exchange: `https://api.hubapi.com/oauth/v1/token`

### Step 4: Install the App

1. Make sure your OAuth server is running
2. Use the install URL from Step 2
3. **Important**: The URL should contain `/user` (e.g., `https://app.hubspot.com/oauth/authorize/user?...`)
4. **Do NOT** include scopes as query parameters - they're selected during installation
5. Click through the authorization flow
6. Select which scopes to authorize
7. Complete the installation

The OAuth flow will provide:
- **Access Token** - Use this to authenticate with the MCP server
- **Refresh Token** - Use this to get new access tokens when they expire

### Step 5: Configure Your MCP Client

Once you have your access token, configure your MCP client:

**For Claude Desktop** (`~/Library/Application Support/Claude/claude_desktop_config.json`):
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

**For other MCP clients:**
- **URL**: `https://mcp.hubspot.com/`
- **Transport Type**: Streamable HTTP
- **Bearer Token**: Your access token
- **Client ID**: Your app's client ID

### Step 6: Test the MCP Connection

**Option A: Test with MCP Inspector (Recommended)**
```bash
npx @modelcontextprotocol/inspector
```

In the browser that opens:
1. Set **Transport Type**: `Streamable HTTP`
2. Set **URL**: `https://mcp.hubspot.com/`
3. Set **Bearer Token**: Your access token
4. Set **Client ID**: Your app's client ID
5. Click **Connect**
6. Navigate to **Tools** → **List Tools**
7. Select `get_user_details` and click **Run tool**
8. Verify you receive user and account information

**Option B: Test with Claude Desktop**
1. Add the configuration to your `claude_desktop_config.json`
2. Restart Claude Desktop
3. Start a new conversation
4. Try asking: "Can you show me my HubSpot contacts?"
5. Claude should use the MCP tools to query your HubSpot data

## 🔧 Troubleshooting

### Issue: "Token invalid" or "Unauthorized"
- Check that your access token is correct and hasn't expired
- Use the refresh token to get a new access token
- Verify the Client ID matches your app

### Issue: "No tools available"
- Check that you granted the necessary scopes during installation
- Verify your user has permissions in HubSpot to access the data
- Check MCP Inspector to see which tools are available

### Issue: OAuth flow fails
- Verify your OAuth server is running
- Check that redirect URLs in the app config match your server
- Look at OAuth server logs for error details

## 📚 Additional Resources

- **HubSpot MCP Server Guide**: https://developers.hubspot.com/docs/apps/developer-platform/build-apps/integrate-with-hubspot-mcp-server
- **OAuth Quickstart**: https://github.com/hubspotdev/oauth-quickstart-nodejs
- **MCP Inspector**: https://github.com/modelcontextprotocol/inspector
- **MCP Specification**: https://modelcontextprotocol.io

## 🔐 Security Notes

- Store access tokens and refresh tokens securely (encrypted)
- Never commit tokens to version control
- Implement token refresh logic (tokens expire)
- Prepare for OAuth 2.1 migration (PKCE, refresh token rotation) coming later in 2025

## 📝 Current App Configuration

**App Name**: ZeroTwo AI Assistant
**App UID**: zerotwo-ai-assistant
**Platform Version**: 2025.2
**Distribution**: Marketplace (user-level)
**Account ID**: 244000548

**Required Scopes**:
- `oauth`
- `crm.objects.contacts.read`
- `crm.objects.companies.read`
- `crm.objects.deals.read`

**Optional Scopes**: None (you can add more after initial setup)

**Redirect URLs**:
- `http://localhost`
- `http://localhost:3000/auth/callback`
- `https://zerotwo.ai/auth/callback`

## 🎯 What You Can Do With MCP

Once connected, you can use natural language to:
- Search contacts, companies, and deals
- Filter by properties and values
- Get aggregated data and insights
- Automate reporting
- Query your CRM data conversationally

**Note**: Current MCP server is **read-only** for CRM data.

---

Need help? Open an issue or contact support@zerotwo.ai

