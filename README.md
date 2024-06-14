# meraki
documenting various api with the command line


Working with the Meraki Dashboard API from the command line can be a powerful way to manage and automate tasks for your Meraki network. Here's a step-by-step guide to get you started:

### 1. Prerequisites

Before you begin, ensure you have the following:
- An API key from your Meraki Dashboard account.
- `curl` and `jq` installed on your system.

### 2. Get Your API Key

1. Log in to your Meraki Dashboard account.
2. Go to **Organization > Settings**.
3. Generate a new API key and save it securely.

### 3. Set Up Your Environment

To avoid typing your API key every time, you can store it in an environment variable. Add the following line to your `.bashrc` or `.zshrc` file:

```bash
export MERAKI_API_KEY="your_api_key_here"
```

Reload your shell configuration:

```bash
source ~/.bashrc  # or source ~/.zshrc
```

### 4. Basic API Requests

#### List Organizations

To list the organizations associated with your API key:

```bash
curl -s -H "X-Cisco-Meraki-API-Key: $MERAKI_API_KEY" \
     -H "Content-Type: application/json" \
     "https://api.meraki.com/api/v1/organizations" | jq
```

#### List Networks in an Organization

Replace `organizationId` with the actual ID from the previous response:

```bash
ORGANIZATION_ID="your_organization_id_here"

curl -s -H "X-Cisco-Meraki-API-Key: $MERAKI_API_KEY" \
     -H "Content-Type: application/json" \
     "https://api.meraki.com/api/v1/organizations/$ORGANIZATION_ID/networks" | jq
```

#### Get Network Devices

Replace `networkId` with the actual ID from the previous response:

```bash
NETWORK_ID="your_network_id_here"

curl -s -H "X-Cisco-Meraki-API-Key: $MERAKI_API_KEY" \
     -H "Content-Type: application/json" \
     "https://api.meraki.com/api/v1/networks/$NETWORK_ID/devices" | jq
```

### 5. Example Script

Here's a script that lists all networks in an organization and their devices:

```bash
#!/bin/bash

# Replace with your organization ID
ORGANIZATION_ID="your_organization_id_here"

# Fetch networks
networks=$(curl -s -H "X-Cisco-Meraki-API-Key: $MERAKI_API_KEY" \
                -H "Content-Type: application/json" \
                "https://api.meraki.com/api/v1/organizations/$ORGANIZATION_ID/networks")

# Extract network IDs and names
network_ids=$(echo $networks | jq -r '.[].id')
network_names=$(echo $networks | jq -r '.[].name')

# Iterate over each network
for id in $network_ids; do
    name=$(echo $network_names | jq -r --arg id "$id" 'select(.id == $id) | .name')
    echo "Network: $name (ID: $id)"
    
    # Fetch devices for each network
    devices=$(curl -s -H "X-Cisco-Meraki-API-Key: $MERAKI_API_KEY" \
                    -H "Content-Type: application/json" \
                    "https://api.meraki.com/api/v1/networks/$id/devices")
    
    # Print device details
    echo $devices | jq '.'
    echo
done
```

### 6. Advanced Usage

As you get more comfortable with the API, you can start incorporating more advanced features such as:

- **Error Handling**: Check for HTTP status codes and handle errors gracefully.
- **Rate Limiting**: Meraki API has rate limits, so ensure your scripts handle rate limits by checking response headers and implementing delays if necessary.
- **Automation**: Use cron jobs or other schedulers to automate tasks such as backups, monitoring, or configuration changes.

### 7. Documentation

For detailed API documentation, refer to the [Meraki Dashboard API Documentation](https://developer.cisco.com/meraki/api-v1/).

By following these steps, you can effectively use the Meraki Dashboard API from the command line to manage your Meraki network. As you become more familiar with the API, you can expand your scripts to automate various network management tasks.
