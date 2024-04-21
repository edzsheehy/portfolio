# Writing sample - Release notes for version 6.6.81 of InsightVM

> This release note group was published on [May 5, 2021](https://docs.rapid7.com/release-notes/insightvm/20210505/) to inform customers about product changes for InsightVM version 6.6.81.

## New

* **More web application fingerprinting capabilities:** We expanded the number of web applications that the Scan Engine can detect by incorporating the [Recog HTML title tag database](https://github.com/rapid7/recog/blob/master/xml/html_title.xml).
* **New scan template configuration option:** Scan templates now have the option to disable fingerprinting for scans that don't involve vulnerabilities.

## Improved

* **(Customer Requested) Upgraded JRE:** This 6.6.81 product version upgrades the Java Runtime Environment (JRE) included with the Security Console to Zulu OpenJDK 1.8.0_292. This upgrade improves the security of the InsightVM application.
* **Updated Apache Tomcat policy:** We updated our Center for Internet Security (CIS) Apache Tomcat 9 policy to version 1.1.0.

## Fixed

* We fixed a discrepancy between the counts of Discovered Assets shown in the Security Console and cloud-based pages. Assets sourced from discovery connections are no longer included in the Security Console-based Discovered Assets count and Assessment Status pie chart.
* The Current Scans For All Sites table in the Security Console will now be properly localized according to the user's language settings. In addition, we fixed an issue that prevented the Asset Groups listing page from loading for non-admin users with a language setting other than English.
* We fixed an issue where vulnerability scans could fail to complete when scanning HP-UX targets.
* AWS Asset Sync discovery connections that have already completed their initial import will now be able to perform more lightweight CloudTrail-based updates even if other discovery connections of the same type have yet to complete that initial import. This change ensures that the import progress of one discovery connection does not unnecessarily get applied to others.
* We fixed the vulnerability descriptions in the Executive Summary and Trend Analysis sections of the Executive Summary Report to describe the type of vulnerabilities that are presented.
* When using the global search field in the Security Console, the Asset Results table will no longer sort results on a case sensitivity basis. In addition, asset records in this table will now also sort the Address column according to IP address ordering instead of sorting the address based on raw text.
  * **Note:** This change is being re-released after being previously reverted in product version 6.6.76.

## Known Issues

* This product version has a minor fingerprinting issue that specifically affects the identification of Apache on older versions of OpenSSL when only SSLv3 is used. This issue can ultimately prevent Apache vulnerability checks from running, but any underlying SSL or TLS properties (such as protocols, ciphers, and certificates) will still be collected properly and authenticated scans will continue to fingerprint installed software. To mitigate this issue, use TLSv1 or later.

## Security Updates

* We fixed CVE-2021-3535, a non-persistent cross-site scripting vulnerability affecting the Filtered Asset Search feature in the Security Console. A specific search criterion and operator combination in Filtered Asset Search could have allowed a user to pass code through the provided search field. Since this vulnerability is non-persistent, only the user attempting to exploit this vulnerability would be affected. This issue affects all Security Console versions up to and including 6.6.80. If your Security Console currently falls on or within this affected version range, ensure that you update your Security Console to the latest version. Special thanks to Philipp Behmer for reporting this issue to Rapid7.
