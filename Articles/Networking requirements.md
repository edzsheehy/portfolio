# Writing sample - connectivity requirements for security monitoring software



# Insight Agent requirements - network traffic and connectivity

In order for the Insight Agent to successfully transmit data between the asset on which it is installed and the Insight Platform, your network must allow communication with a variety of endpoints through specific network ports based on the Rapid7 data storage region to which your organization is subscribed. Additionally, your network must allow agent-related data in transit to reach the Insight Platform without undergoing decryption or any other process that modifies the data from the format Rapid7 services are expecting.

This article covers all network traffic and connectivity requirements you need to be aware of, along with other common network scenarios that could impact the functionality of your agent deployment.

## Insight Agent data must be excluded from SSL decryption

If your network decrypts traffic in transit to perform deep packet inspection (DPI) using a transparent proxy, Insight Agent-related data must be **excluded** from this process.

The Insight Platform will only accept data transmitted by an Insight Agent if the data is accompanied by the X.509 certificate that the Insight Platform is expecting. DPI technologies often replace this certificate with their own as a final step before allowing traffic to continue to its destination. Without the original certificate, the Insight Platform **will not accept** the data.

## Network traffic allowance requirements by region

The assets on which the Insight Agent is installed (or the proxy you configure to receive all agent-related traffic) must be able to communicate with several Insight Platform endpoints for the agent to function properly and power your Insight products. Consult the following data region tables for a breakdown on what endpoints need to be reachable.

If you deploy a network traffic filtering solution that supports wildcards, each table indicates an optional wildcard endpoint that can accommodate multiple endpoint traffic allowances if you want to simplify your configuration.

### Support for alternative static IP addresses

Most, but not all, endpoints documented in these sections support static IP address alternatives. You can configure traffic rules for these IP addresses (if indicated) instead of doing so for the endpoint if you prefer.

Rapid7 does not plan on changing these IP addresses in the near future. If changes are required, we'll update this document and communicate the details on the Insight Agent release notes page.

_The original article provides a collapsible endpoint requirement table for each region. I've only reproduced the **United States - 1** table here for brevity._

### United States - 1

All endpoints listed here must be reachable through port **443**.

| Optional wildcard | Endpoint | Description | Supported static IP addresses |
| --- | --- | --- | --- |
| *endpoint.ingress.rapid7.com | endpoint.ingress.rapid7.com | Insight Agent messages and beacons. | 34.226.68.35 |
| | | | 54.144.111.231 |
| | | | 52.203.25.223 |
| | | | 34.236.161.191 |
| " " | us.storage.endpoint.ingress.rapid7.com | Insight Agent file uploads. | 34.226.68.35 |