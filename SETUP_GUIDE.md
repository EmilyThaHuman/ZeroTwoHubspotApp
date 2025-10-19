# HubSpot App Setup Guide

This guide walks you through creating and configuring the ZeroTwo HubSpot app.

## Prerequisites

1. **HubSpot Developer Account**
   - Sign up at https://developers.hubspot.com/
   - Create a developer test account (free)

2. **HubSpot CLI**
   ```bash
   npm install -g @hubspot/cli@latest
   ```

3. **Authentication**
   ```bash
   hs init
   # Follow the prompts to authenticate with your HubSpot account
   ```

## Step 1: Create the App in HubSpot

### Option A: Via HubSpot Developer Portal (Recommended)

1. Go to https://app.hubspot.com/developer/
2. Click "Create app"
3. Fill in the basic information:
   - **App name**: ZeroTwo AI Assistant
   - **Description**: AI-powered assistant for HubSpot CRM
   - **Logo**: Upload ZeroTwo logo (optional)

4. Configure OAuth:
   - Go to "Auth" tab
   - Add redirect URLs:
     - Production: `https://zerotwo.ai/auth/callback`
     - Development: `http://localhost:5173/auth/callback`
   
5. Configure Scopes:
   - Go to "Scopes" tab
   - Add the following scopes:
     - `crm.objects.contacts.read`
     - `crm.objects.contacts.write`
     - `crm.objects.companies.read`
     - `crm.objects.companies.write`
     - `crm.objects.deals.read`
     - `crm.objects.deals.write`
     - `crm.objects.tickets.read`
     - `crm.objects.tickets.write`
     - `crm.schemas.contacts.read`
     - `crm.schemas.companies.read`
     - `crm.schemas.deals.read`
     - `crm.schemas.tickets.read`

6. Get your credentials:
   - Go to "Auth" tab
   - Copy the **Client ID**
   - Copy the **Client Secret**

### Option B: Via CLI

```bash
cd /Users/reedvogt/Documents/GitHub/ZeroTwoHubSpotApp
hs project create
# Follow the CLI prompts to create the app
```

## Step 2: Configure Environment Variables

1. Copy the example environment file:
   ```bash
   cp env.example .env
   ```

2. Edit `.env` and add your credentials:
   ```bash
   HUBSPOT_CLIENT_ID=your_actual_client_id
   HUBSPOT_CLIENT_SECRET=your_actual_client_secret
   ```

3. Add these same credentials to your ZeroTwo backend:
   - In ZeroTwo project: `ZeroTwoApi/.env`
   - Add:
     ```bash
     HUBSPOT_CLIENT_ID=your_actual_client_id
     HUBSPOT_CLIENT_SECRET=your_actual_client_secret
     HUBSPOT_REDIRECT_URI=https://zerotwo.ai/auth/callback
     ```

## Step 3: Deploy the App Configuration

If you created the app via CLI, upload the configuration:

```bash
hs project upload
```

## Step 4: Test the Integration

### Test OAuth Flow

1. Start ZeroTwo in development mode:
   ```bash
   cd /Users/reedvogt/Documents/GitHub/ZeroTwo
   npm run dev
   ```

2. Navigate to integrations/connectors
3. Click "Connect" on HubSpot
4. Authorize the app when redirected to HubSpot
5. Verify successful connection

### Test MCP Tools

1. In a chat, verify HubSpot tools are available:
   - Click on the tools icon
   - Look for HubSpot connector
   - Should show available tools (search_contacts, etc.)

2. Test a simple query:
   ```
   Search my HubSpot contacts for John
   ```

## Step 5: Production Deployment

### Update Production Environment Variables

In your production environment (Netlify, Vercel, etc.):

1. Add HubSpot credentials to environment variables
2. Ensure redirect URI is set to production URL
3. Deploy your changes

### Make App Public (Optional)

If you want to make the app available to all HubSpot users:

1. Go to HubSpot Developer Portal
2. Navigate to your app
3. Click "Request Listing Review"
4. Fill out the marketplace listing information
5. Submit for review

## Troubleshooting

### OAuth Errors

**Error**: "Invalid redirect URI"
- **Solution**: Verify redirect URI in HubSpot app settings matches exactly (including https/http and trailing slashes)

**Error**: "Invalid client credentials"
- **Solution**: Double-check CLIENT_ID and CLIENT_SECRET are correct and not swapped

**Error**: "Insufficient scopes"
- **Solution**: Verify all required scopes are added in HubSpot app settings

### MCP Connection Errors

**Error**: "Failed to connect to MCP server"
- **Solution**: Verify HubSpot OAuth is completed and tokens are stored
- Check browser console for detailed error messages
- Verify `https://mcp.hubspot.com/` is accessible

**Error**: "Tools not appearing"
- **Solution**: Refresh the page after OAuth completion
- Check that synthetic MCP integration was created in browser console

### Token Refresh Issues

**Error**: "Access token expired"
- **Solution**: This should refresh automatically
- If not, disconnect and reconnect HubSpot
- Check backend logs for token refresh errors

## Security Checklist

- [ ] Client Secret is stored securely (never commit to git)
- [ ] Environment variables are properly configured
- [ ] Redirect URIs use HTTPS in production
- [ ] Tokens are encrypted in database
- [ ] Access tokens are refreshed automatically
- [ ] Proper error handling for authentication failures

## Next Steps

1. **Test with Real Data**: Try searching contacts, companies, and deals
2. **Configure Scopes**: Add more scopes as needed for additional features
3. **Monitor Usage**: Check HubSpot API usage in developer portal
4. **User Documentation**: Create guides for end users on how to connect
5. **Error Monitoring**: Set up logging and alerts for integration issues

## Resources

- [HubSpot Developer Docs](https://developers.hubspot.com/)
- [HubSpot MCP Server Docs](https://developers.hubspot.com/docs/apps/developer-platform/build-apps/integrate-with-hubspot-mcp-server)
- [OAuth 2.0 Best Practices](https://oauth.net/2/)
- [ZeroTwo Documentation](https://docs.zerotwo.ai)

## Support

For issues or questions:
- HubSpot Developer Support: developers@hubspot.com
- ZeroTwo Support: support@zerotwo.ai
- GitHub Issues: https://github.com/zerotwo/zerotwo/issues

