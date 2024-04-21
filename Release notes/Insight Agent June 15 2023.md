# Writing sample - Release notes for June 15, 2023 version of the Insight Agent

> This release note group was published on [June 15, 2023](https://docs.rapid7.com/release-notes/insightagent/20230615/) to inform customers about product changes for the latest edition of the Insight Agent.
>
> Due to the specific subject matter of the announcement, this example does not use the standard release note template. While rare, some product changes are too narrow in scope for a bullet-style format while also being too complicated to fit neatly under the typical categories of "New", "Improved", and "Fixed". I used an article-style format for these reasons:
>
> * The release itself contains only one change.
> * There are circumstances outside of Rapid7's control that are preventing the change from being available to everyone and must be explained.
> * The user needs to be aware of configuration requirements for the change to be applied to their Insight Agents.

## Rapid7-Managed Sysmon 14.16 Upgrade Progress

The [Sysmon Installer component](https://docs.rapid7.com/insight-agent/sysmon-installer-events-monitor-overview) that is included with all [InsightIDR and MDR-subscribed Insight Agents](https://docs.rapid7.com/insight-agent/data-collected) has been updated to deploy Sysmon version 14.16 to all **desktop editions** of Windows 8.1, 10, and 11. This milestone is part of a larger effort to upgrade all Rapid7-managed deployments of the Sysmon service to remediate [CVE-2023-29343](https://nvd.nist.gov/vuln/detail/CVE-2023-29343), a privilege elevation vulnerability affecting Sysmon version 14.13.

Due to [an issue reported on Microsoft's Q&A forum](https://learn.microsoft.com/en-us/answers/questions/1295545/sysmon-14-16-crashes-servers) indicating that Sysmon 14.16 could be causing system crashes on assets running Windows Server, we are pausing the 14.16 upgrade rollout for assets running Windows Server 2012, 2012 R2, 2016, 2019, and 2022 while we investigate. We plan to continue the 14.16 rollout in early July and will provide another release update here with the latest progress.

While the Sysmon Installer component is managed independently from the Insight Agent itself, its update behavior is still subject to the [update settings](https://docs.rapid7.com/insight-agent/agent-settings#automatic-insight-agent-update-controls) you have configured in Agent Management. As long as **Enable automatic updates** and **Keep me on the latest version** are selected for your organization, your assets with installed Insight Agents will receive the Sysmon 14.16 upgrade automatically. If your organization does not currently have automatic updates enabled, or does but with a version lock applied, you will need to change your update settings as stated to receive the Sysmon 14.16 upgrade.
