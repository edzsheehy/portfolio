# Writing sample - Rapid7-managed Sysmon component overview

> [This article](https://docs.rapid7.com/insight-agent/sysmon-installer-events-monitor-overview) was published on [February 22, 2023](https://docs.rapid7.com/release-notes/insightagent/20230222/) to inform customers about how the Rapid7-managed edition of the Sysmon service works.
>
> For context, the InsightIDR and MDR products are designed to log computer events for devices (known as **assets** in Rapid7 terminology) that an IT team has a security interest in. Each of these assets are monitored by an installed Insight Agent that sends these events to the cloud for analysis. For assets running a Windows operating system, the [Sysmon service](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) provides the most robust event logging capabilities and makes it the preferable event collection method.
>
> Sysmon plays an important role in InsightIDR and MDR providing the best value to customers, so the Insight Agent takes exclusive control of the Sysmon configuration on the device to ensure event collection is reliably stable. However, this exclusive control arrangement has some drawbacks:
> 
> * While Sysmon provides better event logging data, it's also error-prone and can cause Windows to crash - an issue that's out of Rapid7's control and is related to Sysmon's kernal-level operation.
> * An IT team might want to use a custom Sysmon configuration for their own purposes.
>
> I wrote the following article to explain how the Sysmon service works alongside Rapid7 products, what customers should expect if the Sysmon service encounters errors, and what accommodations Rapid7 makes for both Sysmon's crash problem and a customer's desire to use their own configuration.

# Sysmon Installer and Events Monitor - how the Insight Agent implements these components for use with InsightIDR and MDR

The [Sysmon Installer and Events Monitor components](https://docs.rapid7.com/insight-agent/data-collected/) included with supported Microsoft Windows-based Insight Agent deployments are responsible for collecting event log data for use with Rapid7's InsightIDR and MDR offerings.

This article explains the role played by each of these components in the Windows events data collection process, how they handle a variety of environmental factors, and what you need to be aware of to ensure they continue to operate effectively.

## How Sysmon and Events Monitor work together

System Monitor (Sysmon) is a Windows service that writes activity to the Windows event log. The Insight Agent deploys the Sysmon service and uses it to collect the following events that InsightIDR and MDR are designed to analyze:

* **Event ID 1: Process creation** (referred to as **Process Starts** in [Log Search](https://docs.rapid7.com/insightidr/search-your-logs/))
* **Event ID 3: Network connection**
* **Event ID 8: CreateRemoteThread**
* **Event ID 10: ProcessAccess**
* **Event ID 13: RegistryEvent (Value Set)**
* **Event ID 25: ProcessTampering (Process image change)**

After Sysmon writes these events to the Windows event log, the Events Monitor component is responsible for sending this data to the Insight Platform for InsightIDR and MDR to use. These events power Rapid7's Attacker Behavior Analytics (ABA) detections and are viewable in InsightIDR's Log Search feature under the **Endpoint Activity** log set.

### ui_realtime - the alternative data collection component

The Insight Agent has its own alternative component called **ui_realtime** that is designed to take over events data collection responsibilities if any of the following scenarios are encountered during the Sysmon deployment phase:

* The operating system the Insight Agent is installed on is outdated or is not compatible with Sysmon.
* A pre-existing, non-Rapid7 deployment of Sysmon is already present on the asset (see the [component overview](#sysmon-installer---component-overview) for details).
* A system crash triggered the removal of the Rapid7-installed Sysmon service (see the [known issues](#known-issues-with-the-sysmon-service) section for details).

The ui_realtime component runs in the user space of the operating system (as opposed to the kernel space where Sysmon runs), which makes it less efficient than Sysmon during times of heavy load. For this reason, ui_realtime is preferable serving a backup collection role.

The Sysmon service is preferred for its performance. The Insight Agent's implementation of Sysmon in concert with Events Monitor provides the best possible visibility on an asset running Windows.

## Sysmon Installer - component overview

The Sysmon Installer component included with the Insight Agent is responsible for installing the Sysmon service on the asset on your behalf and assumes **exclusive management** of the service going forward. This includes the control of configuration, reinstallation, and uninstallation tasks. The Sysmon Installer component continuously monitors Sysmon to ensure the service is [not modified](#how-sysmon-installer-responds-to-modification-attempts) by other third-party software, a user, or any other entity.

### Sysmon Installer will not replace a pre-existing Sysmon installation

When installing the Insight Agent on an asset for the first time, Sysmon Installer **will not** replace an existing Sysmon installation to avoid disrupting your security operations. When this condition applies, the [ui_realtime component](#ui_realtime---the-alternative-data-collection-component) assumes full responsibility for collecting events data for InsightIDR and MDR.

### Configuration details

The Sysmon service is automatically configured by Sysmon Installer. This configuration is maintained and updated by Rapid7. Each event defined in the configuration file has been tuned to emphasize real-time monitoring over logging purposes. This configuration has been tested to ensure it [does not have a material resource utilization impact](#component-resource-utilization) on the asset itself.

**Take care not to modify the Sysmon configuration in any way.** At present, Sysmon configurations are unable to differentiate a potential attacker from a legitimate administrator. To promote the highest level of security, any modification of the Sysmon configuration [will trigger corrective action](#how-sysmon-installer-responds-to-modification-attempts).

### How Sysmon Installer responds to modification attempts

If Sysmon Installer detects any modifications to the Sysmon service or its configuration, the component will take corrective action:

* If the Sysmon service is modified in any way (such as an upgrade or downgrade to a different version), Sysmon Installer will uninstall Sysmon and reinstall the version set by Rapid7.
* If the Sysmon configuration is modified in any way, Sysmon Installer will reload the configuration set by Rapid7.

These corrective measures are necessary to ensure that InsightIDR and MDR continue to receive the events data they rely on to power detections.

### Known issues with the Sysmon service

Sysmon's nature as a kernel-level service gives it the ability to capture events early in the boot process. While desirable from a visibility perspective, this also increases the likelihood of Sysmon causing an overall system crash. As a best practice, Rapid7 mitigates the possibility of this happening by delaying Sysmon version upgrades for a time so that any unknown bugs can be addressed beforehand. As an exception to this, Rapid7 will update Sysmon to the latest version as soon as possible if the installed version is revealed to be vulnerable.

Sysmon Installer includes logic designed to safeguard asset stability. If an asset running a Rapid7-installed version of Sysmon experiences a system crash for any reason (whether attributed to Sysmon or not), Sysmon Installer will uninstall the Sysmon service automatically and promote [ui_realtime](#ui_realtime---the-alternative-data-collection-component) as the sole component responsible for events data collection. From this point, Sysmon Installer would only reinstall Sysmon with the release of a new version.

### How to request a custom Sysmon configuration

While it's possible to implement a custom Sysmon configuration outside of Rapid7's control, be aware that doing so will preclude your ability to use Sysmon to collect events data for use with InsightIDR and MDR. Events Monitor does not support the ingestion of events from any Sysmon deployment other than the one installed by Rapid7's Sysmon Installer. In this scenario, events data collection would once again be the responsibility of [ui_realtime](#ui_realtime---the-alternative-data-collection-component).

If you wish to proceed with a custom Sysmon configuration nonetheless, open a case with the Rapid7 Support team to get started. Rapid7 must first remove the Sysmon Installer component across your entire organization before you can implement your own Sysmon configuration.

## Component resource utilization

This table provides an asset resource utilization breakdown for Events Monitor, the Sysmon service, and Sysmon Installer.

| | Memory | CPU | Disk |
| --- | --- | --- | --- |
| **Events Monitor component** | 60MB | 0.7% | Up to 450MB |
| **Sysmon service** | 15MB | 1% | 64MB |
| **Sysmon Installer component** | 20MB | 1% | 20MB |
