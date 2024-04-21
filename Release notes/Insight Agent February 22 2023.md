# Writing sample - Release notes for February 22, 2023 version of the Insight Agent

> This release note group was published on [February 22, 2023](https://docs.rapid7.com/release-notes/insightagent/20230222/) to inform customers about product changes for the latest edition of the Insight Agent.

## New

* **New data collection for Patch Tuesday:** We updated the Insight Agent's data collection capabilities on Windows assets to support Patch Tuesday vulnerability checks for February 2023.

## Improved

* **Improved fingerprinting accuracy for InsightVM assessments:** We updated the Insight Agent's data collection methods to more accurately fingerprint the following software which helps prevent false positives and false negatives during InsightVM vulnerability assessments:
  * Azul Zulu JRE
  * Windows Defender
  * Mozilla Firefox plugin
  * SharePoint
* **Improved asset information collection for macOS:** We updated how the Insight Agent collects asset information to more accurately identify macOS versions 11 and later.
* **Improved data collection for Log4j vulnerabilities:** We updated how the Insight Agent collects data for Log4j vulnerability assessments to prevent false positives when the version of Log4j is upgraded inside the JAR without changing the name of the file itself.
* **(Customer Requested) Lustre file systems are now excluded:** The Insight Agent will no longer consider Lustre file systems during assessments in alignment with the Scan Engine and to avoid searching across network file systems.

## Fixed

* We fixed multiple Sysmon Installer bugs related to the installation and uninstallation of the Sysmon driver and service that caused data collection interruptions and asset crashes. 
  * Refer to [How the Insight Agent Works](https://docs.rapid7.com/insight-agent/data-collected/) for more information on the `rapid7_sysmon_installer.exe` process.
* We fixed a bug in the `tem_realtime` vulnerability data collection job that resulted in `temp` folders not being removed when the memory limit was exceeded.
* The `tem_realtime` job no longer displays the `Completed` log message if it does not finish successfully.
* We fixed a system-specific configuration issue where Log4j-related data collection would fail due to the Insight Agent failing to find the PowerShell executable path.
* We removed a deprecated library within the Insight Agent (`cryptography.hazmat.bindings._openssl.pyd`) that was causing event log errors.

## Other Changes

* The [token-based](https://docs.rapid7.com/insight-agent/using-a-token) Insight Agent installer will now automatically retry connecting to the token service several times if the initial attempt fails due to connectivity issues during an installation.
  * Our troubleshooting data on issues related to the token-based installation method have shown that manually reattempting the connection to the token service was effective in resolving some of these cases. Automating this functionality in the installer itself will mitigate similar unforeseen connectivity-related issues during installations going forward. While we are aware of additional issues related to this installation method, this change is meant to lessen the impact as the team continues to work on a long-term solution for token-based installations generally.
* We moved the Sysmon configuration file to the `/common` folder in the Insight Agent directory to support future dynamic configuration changes without requiring a software release.
