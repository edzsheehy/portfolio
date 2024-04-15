# Writing sample - UUID asset correlation

> [This article](https://docs.rapid7.com/insightvm/correlate-assets-with-insight-agent-uuids/) was published in early 2020 to inform InsightVM customers about a new setting that could prevent duplicate vulnerability assessment results.
>
> For context, the InsightVM product is designed to scan devices on a company network for vulnerabilities and generate reports on those findings for an IT team. InsightVM can scan devices (known as **assets** in Rapid7 terminology) in 2 ways:
>
> * Using a scan engine, which is a separate application installed on a dedicated host on the same network.
>   * This is the traditional method of scanning.
> * Using the Insight Agent, which is monitoring software installed on each asset.
>   * This is a newer, alternative method of scanning.
>
> To ensure that unique devices are recognized from scan to scan, InsightVM runs a process called "asset correlation" which compares the attributes of a scan target (IP address, MAC address, etc) with historical asset records from prior scans. This process is essential for establishing vulnerability trend data for assets on a network and prevents the same asset from being counted more than once.
>
> The asset correlation process is straightforward when using scan engines alone, but becomes complicated when Insight Agents are performing vulnerability assessments as well. Scan engines perform their job from the outside, meaning the richness of the data they collect depends on what the asset allows an unauthenticated connection to see (you can also provide scan engines with sets of credentials to get around this). On the other hand, Insight Agents are installed on an asset with Administrator privileges up front, so their vulnerability assessments are considered "authenticated" by default. If a scan engine and an Insight Agent with different levels of access perform a vulnerability assessment on the same asset, disparities in the data collected from each could pose a problem for the asset correlation process and result in InsightVM counting the two assessments as separate devices.
>
> I wrote the following article to explain the nature of asset correlation and document a UUID checking feature that Rapid7 released to help scan engines and Insight Agents work together more effectively.

# Correlate Assets with Insight Agent UUIDs

If you assess your assets using Scan Engines and Insight Agents together, you may occasionally see multiple entries for the same asset in the Security Console. While several environmental and configuration factors can contribute to duplications like this, the Agent Management experience in the Insight Platform now includes a Universally Unique Identifier (UUID) retrieval feature that can address this problem.

## Asset Correlation Explained

The Security Console is designed to correlate multiple assessments of the same asset into a single asset record, even if the asset gets a new IP address or if the asset gets scanned multiple times under different sites. However, asset record duplication can occur if the Security Console does not have enough data to correlate with.

This UUID asset correlation feature is designed to improve the Security Console’s correlation accuracy even if scan data is otherwise insufficient.

## How InsightVM Uses UUIDs

A UUID is one of several asset attributes that the Security Console uses when it comes to differentiating one asset from another. InsightVM can enumerate multiple UUIDs for each of your assets depending on the assessment method:

* Scan Engines pull a UUID from the asset operating system during a scan. These appear in the Security Console as either Windows or Unix based.
* Insight Agents generate their own UUID for each asset they are installed on. These appear in the Security Console with the R7 Agent ID label.

## How This Asset Correlation Feature Works

The feature itself is a global setting that you can switch on or off for all agents in your environment. Enabling it instructs your agents to provide the UUIDs they have generated for their corresponding assets to the Scan Engine upon request. An agent knows that a Scan Engine is asking for this information when the engine scans UDP port 31400 on the asset.

As the Security Console integrates new assessment data, it will know if a scanned asset is already accounted for by an agent and won’t run the risk of counting them twice in your results.

## When to Turn This Feature On

You should have this asset correlation feature enabled if any of the following scenarios apply to you:

* You assess your assets using both Insight Agents and traditional on-premises Scan Engines.
* You use Scan Engines to run complementary unauthenticated scans on agent assets for remote assessments. Unauthenticated scans yield far less data than authenticated scans produce. This condition leaves the Security Console with fewer data points that it can use for correlation. Enabling this feature is especially important if you want to mitigate the impact of this data gap.

## Prepare Your Environment

Verify the following scan template and agent asset configurations before enabling this correlation feature.

### Scan Template Configuration

The scan template you intend to use must be configured to scan UDP port 31400. As of early 2019, this port is already included in the default **Well-known port numbers** option in the service discovery configuration of InsightVM’s built-in scan templates. If you intend to scan with one of these built-in templates or a custom template that was created after this change took effect, then no further configuration is required.

However, if you intend to scan with a custom template that existed prior to this change, you’ll have to specify this port manually. If this scenario applies to you, follow these steps:

1. In InsightVM, click **Administration** in your left menu.
2. In the Scans > Scan Templates section, click **Manage** scan templates.
3. Browse to the custom scan template you need to adjust and click the edit icon.
4. Click the **Service Discovery** tab.
5. In the UDP Scanning section, enter `31400` in the Additional ports field.
6. Click **Save**.

### Agent Asset Configuration

Your agent assets must meet the following requirements for this correlation feature to be effective:

1. Agent assets must run at least version 2.0 of the Insight Agent software.
2. Agent assets must have UDP port 31400 open.
   * This allows the agent to respond to the Scan Engine during scanning. If this port is closed, the agent will not provide its UUID to the Scan Engine and the Security Console will not be able to use this information for correlation purposes.

## How to Enable the Asset Correlation Switch

After you verify that your environment is prepared for this capability, you’re ready to turn it on. The switch is currently disabled by default, but a platform or product administrator can enable it in the Agent Management experience of their Insight account page.

Follow these steps to turn on the asset correlation switch:

1. Go to insight.rapid7.com and sign in with your Insight account email and password.
   * If you do not see the My Products & Services screen upon signing in, open the app switcher in the upper left corner of the screen and click **My Account**.
2. On the My Products & Services screen, click the **Data Collection Management** tab on your left menu. The interface will default to the Agent Management view.
3. Expand the **Settings** dropdown in the upper right corner. Click **Asset Correlation**.
4. Switch the feature to the **On** setting.
5. Click **Save** to apply your changes.

From this point forward, your agents will know to provide their UUIDs for correlation purposes.
