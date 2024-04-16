# Writing sample - false positive investigations in InsightVM

> [This article](https://docs.rapid7.com/insightvm/false-positive-investigations) was published on [October 7, 2020](https://docs.rapid7.com/release-notes/insightvm/20201007/) to inform customers about a new false positive investigation and reporting tool in InsightVM.
>
> For context, the InsightVM software performs vulnerability assessments by applying "vulnerability checks" to a computer device (referred to as an **asset** in Rapid7 terminology) during a scan. Each of these checks are designed to look for a specific software version, system configuration, or other characteristic that could indicate the presence of an exploitable vulnerability. If the conditional logic of a check is satisfied during a scan, InsightVM reports that the asset is positive for that vulnerability in the results.
>
> Sometimes vulnerability checks return a positive result erroneously (known as a "false positive") - an unwanted occurrence that makes it harder for customers to prioritize their remediation efforts. Originally, customers would have to assemble the scan findings themselves and dispute the positive result by filing a case with the Rapid7 Support team. This manual process was an unnecessary burden on the customer, so an automated investigation tool was developed and added to InsightVM to make reporting false positives easier.
>
> I wrote the following article to comprehensively document not only how the investigation tool works, but how customers should engage with it according to its design intent.

# Investigate false positives

InsightVM allows you to investigate vulnerable results as potential false positives directly from the Security Console. If your investigation shows that the result could be a false positive, you can report the findings to the Rapid7 Support team in a single mouse-click.

By the time you’re ready to create your case in the Customer Portal, the Support team will already have the information they need to troubleshoot the issue.

## What is a false positive?

A "false positive" is when InsightVM incorrectly determines that a target asset is vulnerable to a specific vulnerability check. False positives can appear due to an error in check logic or changes in the target software that the check is not designed to handle.

You should report false positives to Rapid7 immediately if they appear in your results. This gives us the chance to fix the vulnerability check and make sure your assessment results are as accurate as possible.

## How false positive investigations work

An investigation is a rescan of the affected asset that's limited to the vulnerability check in question. This rescan uses the [Full Audit without Web Spider](https://docs.rapid7.com/insightvm/scan-templates#full-audit-without-web-spider) built-in scan template with enhanced logging enabled. If this rescan produces the same vulnerable result as before with all prerequisites satisfied, you can report the result as a potential false positive. The investigation tool sends false positive report packages to Rapid7 in XML format.

Like regular scans, you can [run investigations immediately or schedule them](#start-an-investigation) to run automatically at a later time.

False positive investigations are [vulnerability finding-based](https://docs.rapid7.com/insightvm/vulnerability-metrics-explained). This means any investigation you submit for a vulnerable result includes all detected instances of that vulnerability on the asset (if more than one instance is found).

## How to use this tool

The design intent of the false positive investigation tool is to help you identify how your scanning configuration (which includes the presence and strength of credentials and the coverage of your scan template) could be producing inaccurate results and suggest changes to correct it. You must perform this due diligence before we allow you to report potential false positives to the Rapid7 Support team for further investigation.

This tool should **not** be used as a means to clear inaccurate results that are due to known scan misconfigurations. When using false positive investigations, keep these goals in mind:

* Take action on configuration suggestions provided by investigation results to ensure that your scanning configuration is in the best position to scan accurately going forward.
* Help Rapid7 prioritize true false positive candidates by reporting those investigations that are not related to inadequate credentials or scan template coverage.

## False positive reporting requirements

You must meet the following requirements to run false positive investigations.

### User role permissions

InsightVM has two permissions related to false positive investigations (also detailed on the [Managing Users and Authentication](https://docs.rapid7.com/insightvm/managing-users-and-authentication) page):

* View Vulnerability Investigations
* Manage Vulnerability Investigations

Your user role must have the **Manage Vulnerability Investigations** permission assigned to create new investigations, submit eligible results to Rapid7, and close investigations.

### Check type prerequisites

Any vulnerable result that you want to report as a potential false positive must meet some prerequisites based on its check type:

* **Authenticated checks** - The accuracy of authenticated vulnerability checks depends on whether the Scan Engine can successfully authenticate to the target asset with the credentials you’ve specified and how certain it is about identifying the asset’s software. After your investigation finishes, you can only report a vulnerable result for an authenticated check as a potential false positive if the scan successfully applied credentials and if the system fingerprint returned a [certainty value](https://docs.rapid7.com/insightvm/fingerprint-certainty) of `1.0`.
  * If your investigation shows that you did not meet both of these prerequisites, you’ll need to check your credentials (or use a stronger credential set) and run the investigation again.
* **Unauthenticated (remote) checks** - Unauthenticated checks are "outside-in" checks that do not use authentication. For this reason, you can report vulnerable results for unauthenticated checks as potential false positives based on a positive investigation result alone.

## Start an investigation

You can start a false positive investigation from any of the following locations in InsightVM:

* The Vulnerabilities table on any asset detail page
* The Instances table on any vulnerability detail page (when accessed through an asset detail page)
* The Affects table on any vulnerability detail page (when accessed through the **Vulnerabilities** tab on your left menu)

**You cannot start investigations from node detail pages.** A "node" is a device on your network that a site was able to scan based on the configuration of the site’s inclusion list. Nodes are not considered assets until the scan data is fully integrated into InsightVM. Although you can browse a node’s detected vulnerabilities through any site’s completed scan history, you cannot start investigations from them.

After you locate the vulnerable result that you think could be a false positive, complete the following steps to start the investigation:

1. In the table that contains the vulnerable result, click **Investigate** in the Investigation column.
2. The Vulnerability Investigation window appears. This window details the investigation process.
   * If you want InsightVM to run the investigation at a later time, check the box next to **Do you want to run this investigation later?** to schedule the investigation.
3. Click **Investigate**. The window closes and returns you to the previous table view. Your vulnerable result now shows a **Pending** status. The status changes to **In Progress** when the rescan portion of the investigation starts. You can continue using InsightVM as you normally would while the investigation runs.
   * You can click the status link of your investigation to see a list of any other investigations that you’ve run before.

**InsightVM says my investigation failed. What now?** Running an investigation means that InsightVM must rescan the affected asset. The investigation will fail if your asset is not reachable on your network at the time your investigation starts or if InsightVM loses connectivity to your asset during the scanning process. If this occurs, check the connectivity of your asset before trying again.

## Review investigation results

After your investigation completes successfully, its status changes to **Review**. Click this **Review** link to see the results.

**Lost track of your investigation?** You can access the investigation history table by navigating through the **Administration** tab, in the **Vulnerabilities** section, click **Review** vulnerability investigation requests.

After clicking **Review**, the Investigation Results window appears. InsightVM details the steps of the investigation in this window based on the type of vulnerability check that it ran. The [number of steps](#check-type-prerequisites) will vary depending on whether the vulnerability check requires authentication or not:

* If the investigation meets all the required criteria and produces the same vulnerable result as before, the result **is eligible** to report as a potential false positive.
  * Click **Send Results** to send a package containing the XML report and advanced scan logs to Rapid7. This action changes your investigation status to **Submitted**.
* If the investigation does not return a vulnerable result, or if the investigation fails to meet the criteria of [any other step](#check-type-prerequisites), the result **is not eligible** to report as a potential false positive.
  * If the investigation result is not eligible for reporting because of failed credentials or low system fingerprint certainty, check that [the credentials you’ve configured](https://docs.rapid7.com/insightvm/configuring-scan-credentials) can give InsightVM the access it needs to scan accurately before trying again.
  * If the investigation determines that the asset is not vulnerable after all, this is probably due to a configuration issue with your original scan template. Custom scan templates that you have tuned for specific scanning scenarios often lack the comprehensive coverage required to scan for certain vulnerabilities accurately. The [Full Audit without Web Spider](https://docs.rapid7.com/insightvm/scan-templates#full-audit-without-web-spider) template that the investigation uses is the standard scan template for almost all InsightVM scanning scenarios. If you find that your custom scan template is producing false positive results that are then resolved by a subsequent investigation, consider modifying your custom scan template or replacing it with something more robust in coverage.

**Reminder - this investigation tool has a specific purpose.** Using false positive investigations to clear what you know to be inaccurate results caused by an inadequate scan template is **not** the [design intent of this tool](#how-to-use-this-tool). The goal here is to identify what configuration conditions could be causing inaccurate scan results and make changes to correct them. If an investigation shows that a questioned vulnerability result is indeed not configuration related, those results should be reported to Rapid7 for additional testing.

Investigation results that you don’t submit to Rapid7 stay in the **Review** status until you decide to submit them, reinvestigate them, or close them at your discretion. InsightVM keeps all closed investigations in the Vulnerability Investigations table for your records.

## Create a case with Rapid7 Support

If you send your investigation results to Rapid7, InsightVM tells you to create a case in the Customer Portal so our Support team can start the troubleshooting process. When creating your case, make sure to show that you already sent a false positive report package directly from InsightVM.

**Rapid7 Support is on the job!** Thank you for using this false positive investigation and reporting tool! Your efforts help us ensure that assessment results for all InsightVM customers are as accurate as possible.
