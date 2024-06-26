# Writing sample - connectivity requirements for security monitoring software

> [This article](https://docs.rapid7.com/insight-agent/network-traffic-and-connectivity-requirements) was published on [May 17, 2023](https://docs.rapid7.com/release-notes/insightagent/20230517/) as a major rewrite to the Insight Agent connectivity requirements article that existed previously.
> 
> For context, the Insight Agent software collects security-related data on a computer device (referred to as an **asset** in Rapid7 terminology) and transmits that data to a cloud for analysis. Customers can view this analysis in Rapid7 applications (InsightVM, InsightIDR, etc). If the Insight Agent can't communicate with the cloud, the data can't be analyzed, and the device can't be monitored. Therefore, uninterrupted and stable Insight Agent communication with the cloud is very important.
>
> Steady reports of customer pain with Insight Agent connectivity revealed that the original article (also written by me years before) did not adequately present the required endpoint traffic rules or document common pitfalls that customers could encounter during a deployment. I worked closely with Engineering, Product Management, and Customer Advisors to gather comprehensive information on the topic and wrote this new article to fill in these gaps. Notable additions and changes include:
> 
> * Expandable endpoint communication tables for each Rapid7 data storage region.
>   * These tables also list all required endpoints as literals, which replaced the strategy of naming only a few endpoints using wildcards. Customers reported their IT teams are often not allowed to configure firewall rules based on wildcards at all, so we made this change to accommodate that.
> * A warning on using deep packet inspection software in an Insight Agent deployment.
> * Clear indications on whether a specific endpoint supports static IP addresses.

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

> The original article provides an endpoint requirement table for each region. I've only reproduced the **United States - 1** table here for brevity.

### United States - 1

All endpoints listed here must be reachable through port **443**. `" "` in a table cell denotes that the content is identical to the previous cell in the column.

| Optional wildcard | Endpoint | Description | Supported static IP addresses |
| --- | --- | --- | --- |
| *endpoint.ingress.rapid7.com | endpoint.ingress.rapid7.com | Insight Agent messages and beacons. | 34.226.68.35, 54.144.111.231, 52.203.25.223, 34.236.161.191 |
| " " | us.storage.endpoint.ingress.rapid7.com | Insight Agent file uploads. | 34.226.68.35, 54.144.111.231, 52.203.25.223, 34.236.161.191 |
| " " | us.api.endpoint.ingress.rapid7.com | Insight Agent messages, beacons, and file uploads. | 34.226.68.35, 54.144.111.231, 52.203.25.223, 34.236.161.191 |
| " " | us.bootstrap.endpoint.ingress.rapid7.com | Updates for the Insight Agent software. | 34.226.68.35, 54.144.111.231, 52.203.25.223, 34.236.161.191 |
| " " | us.deployment.endpoint.ingress.rapid7.com | Certificate files for token-based Insight Agent installations. | 34.226.68.35, 54.144.111.231, 52.203.25.223, 34.236.161.191 |
| " " | us.main.endpoint.ingress.rapid7.com | Insight Agent messages and beacons. | 193.149.136.0/24 |
| " " | us.storage.main.endpoint.ingress.rapid7.com | Insight Agent file uploads. | 193.149.136.0/24 |
| " " | us.api.main.endpoint.ingress.rapid7.com | Insight Agent file uploads and beacons. | 193.149.136.0/24 |
| *.insight.rapid7.com | data.insight.rapid7.com | Insight Agent messages, beacons, update requests, and file uploads for collection using the Rapid7 Collector. | None |
| " " | us.data.insight.rapid7.com | " " | None |
| " " | us.data.logs.insight.rapid7.com | Logs to InsightOps. | None |

## Port requirements for assets when using Rapid7 Collectors as proxies

If you use the Rapid7 Collector as a proxy destination for Insight Agent traffic, your assets must also be allowed to communicate with your Collector host through these ports:

* **5508** - Used for agent messages and beacons.
* **6608** - Used for agent update requests and file uploads for collection.
* **8037** - Used for agent messages and beacons.
