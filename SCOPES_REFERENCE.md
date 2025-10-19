# HubSpot MCP Scopes Reference

## ✅ Successfully Configured (Build #8)

Your app now has all supported MCP scopes configured!

## 📋 Current Scope Configuration

### Required Scopes (Always Granted)
These scopes are required for basic MCP functionality:
- ✅ `oauth` - OAuth authentication
- ✅ `crm.objects.contacts.read` - Read contact data
- ✅ `crm.objects.companies.read` - Read company data
- ✅ `crm.objects.deals.read` - Read deal data

### Optional Scopes (User Can Choose During Install)
These scopes provide access to additional CRM objects:
- ✅ `crm.objects.carts.read` - Read cart data
- ✅ `crm.objects.products.read` - Read product data
- ✅ `crm.objects.orders.read` - Read order data
- ✅ `crm.objects.line_items.read` - Read line item data
- ✅ `crm.objects.invoices.read` - Read invoice data
- ✅ `crm.objects.quotes.read` - Read quote data
- ✅ `crm.objects.subscriptions.read` - Read subscription data
- ✅ `crm.objects.users.read` - Read user information
- ✅ `crm.objects.owners.read` - Read owner information

## ❌ Scopes NOT Supported by MCP

According to the [HubSpot MCP documentation](https://developers.hubspot.com/docs/apps/developer-platform/build-apps/integrate-with-hubspot-mcp-server), the MCP server currently only supports **READ-ONLY** access. The following scopes are NOT available:

### Write Scopes (Not Supported)
- ❌ `crm.objects.contacts.write`
- ❌ `crm.objects.companies.write`
- ❌ `crm.objects.deals.write`
- ❌ `crm.objects.tickets.write`
- ❌ Any other `.write` scopes

### Tickets Scope (Not Working Yet)
- ❌ `crm.objects.tickets.read` - Currently returns an error during deployment
- ❌ `crm.objects.tickets.write` - Not supported (write scope)

**Note**: The documentation mentions `crm.objects.tickets.read` should be added as a required scope after initial upload, but it's currently not working on platform version 2025.2. This may be a temporary issue or require beta access.

## 🔍 What Each Scope Enables

| Scope | MCP Tools Available | Data Accessible |
|-------|-------------------|-----------------|
| `crm.objects.contacts.read` | `search_contacts` | Contact properties, associations |
| `crm.objects.companies.read` | `search_companies` | Company properties, associations |
| `crm.objects.deals.read` | `search_deals` | Deal properties, pipeline stages |
| `crm.objects.carts.read` | `search_carts` | Shopping cart data |
| `crm.objects.products.read` | `search_products` | Product catalog |
| `crm.objects.orders.read` | `search_orders` | Order data |
| `crm.objects.line_items.read` | `search_line_items` | Line item details |
| `crm.objects.invoices.read` | `search_invoices` | Invoice information |
| `crm.objects.quotes.read` | `search_quotes` | Quote data |
| `crm.objects.subscriptions.read` | `search_subscriptions` | Subscription details |
| `crm.objects.users.read` | User details in responses | User information |
| `crm.objects.owners.read` | Owner details in responses | Owner/agent information |

## 🎯 How to Add More Scopes

If you need to add additional scopes in the future:

1. **Edit the configuration file**: `/src/app/zerotwo-hsmeta.json`
2. **Add to `requiredScopes`** (always granted) or **`optionalScopes`** (user chooses):
   ```json
   "optionalScopes": [
     "crm.objects.carts.read",
     "crm.objects.products.read"
   ]
   ```
3. **Upload the updated config**:
   ```bash
   hs project upload
   ```
4. **Users must reinstall** to get new scopes

## 🔐 Scope Selection During Installation

When users install your app, they will:
1. See a list of all required and optional scopes
2. Be able to **select which optional scopes** to grant
3. Only get access to scopes that match their HubSpot user permissions
4. Be able to modify scope selections by reinstalling

## 📊 Scope Availability by HubSpot Tier

Some CRM objects may only be available in certain HubSpot tiers:

| Object | Availability |
|--------|-------------|
| Contacts, Companies, Deals | All tiers |
| Products, Line Items | Sales Hub Professional+ |
| Quotes | Sales Hub Professional+ |
| Invoices, Subscriptions | Varies by subscription |
| Carts, Orders | Commerce Hub |

## 🚀 Testing Scopes

To verify which scopes are working:

1. **Use MCP Inspector**:
   ```bash
   npx @modelcontextprotocol/inspector
   ```
2. **Connect with your access token**
3. **List available tools** - you'll see tools based on granted scopes
4. **Test `get_user_details`** - shows which objects are accessible

## 📝 Important Notes

1. **Read-Only Access**: MCP server currently only supports reading data, not writing
2. **User Permissions**: Users can only access data they have permission to view in HubSpot
3. **Scope Limitations**: The MCP server is based on the CRM Search API (no vector search)
4. **Future Updates**: Additional scopes may become available as the MCP server evolves
5. **OAuth 2.1**: Later in 2025, PKCE and refresh token rotation will be required

## 📚 References

- [HubSpot MCP Server Integration Guide](https://developers.hubspot.com/docs/apps/developer-platform/build-apps/integrate-with-hubspot-mcp-server)
- [HubSpot OAuth Scopes](https://developers.hubspot.com/docs/api/oauth/scopes)
- [Model Context Protocol Specification](https://modelcontextprotocol.io)

---

**Last Updated**: Build #8 successfully deployed with all supported MCP read scopes
**Platform Version**: 2025.2
**App Status**: Ready for AUP signing and installation

