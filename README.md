# ZeroTwo HubSpot App

This directory contains the configuration for the ZeroTwo HubSpot application integration.

## Overview

The ZeroTwo HubSpot App enables AI-powered interactions with HubSpot CRM data through the HubSpot Model Context Protocol (MCP) server.

## Features

- **Contact Management**: Search, read, and manage HubSpot contacts
- **Company Management**: Access and update company information
- **Deal Management**: Track and manage deals through AI conversations
- **Ticket Management**: Handle support tickets efficiently
- **AI-Powered**: Natural language interface for all CRM operations

## Setup Instructions

### Prerequisites

1. A HubSpot developer account
2. HubSpot CLI installed (`npm install -g @hubspot/cli`)
3. ZeroTwo account with appropriate subscription level

### Creating the App

1. **Initialize the HubSpot CLI:**
   ```bash
   hs init
   ```

2. **Create the app:**
   ```bash
   hs project create
   ```

3. **Deploy the app configuration:**
   ```bash
   hs project upload
   ```

### Configuration

The app configuration is defined in `app.json`:

- **Scopes**: Defines the CRM permissions required
  - Contact read/write access
  - Company read/write access
  - Deal read/write access
  - Ticket read/write access
  - Schema read access for all object types

- **Redirect URLs**: OAuth callback endpoints
  - Production: `https://zerotwo.ai/auth/callback`
  - Development: `http://localhost:5173/auth/callback`

### OAuth Flow

1. User initiates connection from ZeroTwo app
2. Redirects to HubSpot authorization page
3. User authorizes the app with selected scopes
4. HubSpot redirects back to ZeroTwo with authorization code
5. ZeroTwo exchanges code for access and refresh tokens
6. Tokens are stored in user profile (`profile.settings.oauth_tokens.hubspot`)
7. MCP integration is created for HubSpot tools

### MCP Server Integration

Once OAuth is complete, ZeroTwo connects to the HubSpot MCP server:
- **Endpoint**: `https://mcp.hubspot.com/`
- **Transport**: Streamable HTTP
- **Authentication**: Bearer token using stored access token

### Available Tools

After connection, the following MCP tools become available:

1. **get_user_details** - Get authenticated user and account information
2. **search_contacts** - Search and retrieve contacts from HubSpot CRM
3. **search_companies** - Search and retrieve companies from HubSpot CRM
4. **search_deals** - Search and retrieve deals from HubSpot CRM
5. **search_tickets** - Search and retrieve support tickets from HubSpot

## Development

### Testing Locally

1. Set environment variables:
   ```bash
   export HUBSPOT_CLIENT_ID="your_client_id"
   export HUBSPOT_CLIENT_SECRET="your_client_secret"
   ```

2. Run ZeroTwo in development mode:
   ```bash
   npm run dev
   ```

3. Test OAuth flow with localhost redirect URL

### Deployment

1. Update `app.json` with production redirect URLs
2. Upload to HubSpot:
   ```bash
   hs project upload
   ```

3. Request app review (if making public)
4. Deploy ZeroTwo with production environment variables

## Security Notes

- Client ID and Client Secret are stored in environment variables
- Access tokens are encrypted in the database
- Refresh tokens are used to maintain long-term access
- Token refresh happens automatically when needed
- All API calls use HTTPS with Bearer authentication

## Support

For issues or questions:
- ZeroTwo Support: support@zerotwo.ai
- HubSpot Developer Docs: https://developers.hubspot.com
- MCP Server Docs: https://developers.hubspot.com/docs/apps/developer-platform/build-apps/integrate-with-hubspot-mcp-server

## License

Copyright © 2024 ZeroTwo AI. All rights reserved.

