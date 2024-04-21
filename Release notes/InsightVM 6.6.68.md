# Writing sample - Release notes for version 6.6.68 of InsightVM

> This release note group was published on [February 17, 2021](https://docs.rapid7.com/release-notes/insightvm/20210217/) to inform customers about product changes for InsightVM version 6.6.68.

## New

* **New vulnerability content:** We added a remote check for [SNWLID-2021-0001](https://psirt.global.sonicwall.com/vuln-detail/SNWLID-2021-0001) (CVE-2021-20016), a SQL injection vulnerability affecting the SonicWall SMA 100 series of products.

## Improved

* **Improved asset correlation accuracy:** To account for situations where an asset's Universally Unique Identifier (UUID) may change, the asset correlation process in the Security Console will no longer weigh UUIDs as the highest priority correlation attribute compared to hostnames, IP addresses, and MAC addresses. Asset correlation will still take place with a UUID match, but a non-matching UUID will no longer outweigh other matching asset attributes.
* **Improved Metasploit Remote Check Service command console responses:** The command console will now respond with better detail if you try to enable or disable the Metasploit Remote Check Service on a Scan Engine that is ineligible for the service.
* **New filtered asset search criteria:** You can now perform filtered asset searches in the Security Console based on an asset's primary hostname.

## Fixed

* In cases where a scan was performed with a Scan Engine pool, **GET** requests to `/api/3/scans/{id}` will now return all Scan Engine IDs contained in the pool instead of just one.
* The following APIv3 endpoints now return the correct values for an asset group's vulnerability count and risk score rather than values of 0, and also return the correct asset group `type`:
  * `/api/3/sites/{id}/included_asset_groups`
  * `/api/3/sites/{id}/excluded_asset_groups`
* We fixed an issue affecting the `/api/3/assets/search` APIv3 endpoint that caused software and file information to be missing from the response.
* We fixed an issue affecting the `/api/3/users/{id}/password` APIv3 endpoint that would not allow **PUT** requests to update a user's password.
* **POST** requests to `/api/3/administration/commands` with `show asset vulns {assetID}` in the body will now return results as expected.
* **POST** requests to `/api/3/asset_groups` now only require the `searchCriteria` field to be populated if the asset group type is set to dynamic. This field is now optional when creating static asset groups. In addition, dynamic asset group creation requests that are missing this field will now return a constructive response to inform the user.
* We updated the APIv3 documentation to reflect that **POST** requests to `/api/3/scan_engines` do not require the `id` field.
* Asset table records will now display the primary hostname in the Name column consistent with the hostname displayed in other areas of the Security Console.
* The Email Sender Address field in the **SMTP** tab on the Security Console Configuration page will now validate to alert the user if the specified email address contains invalid special characters.
* The filter field in the Scan Credentials table of site configurations will now respond correctly to user input as expected.
* We fixed an issue that allowed the seconds conversion figure located next to the Connection timeout (ms) field in the **Scan Engines** tab on the Security Console Configuration page to revert back to the default value even after the millisecond value had been modified and saved.
* We fixed an issue that prevented context-driven risk scores in site views from updating correctly if a Low or Very Low criticality tag was applied to the asset.
* We fixed an issue that prevented the Asset Results table in Security Console search results from loading if the Scanned table on the **Assets** page was previously sorted according to the Assessed column.
* We fixed an issue where CSV exports of the Completed Assets table in scan detail pages showed authentication results that differed from what was displayed in the Security Console interface.
* We fixed an issue where KernelCare and Ksplice were misidentified on systems that did not have them installed, resulting in false positives.
* We fixed an issue that prevented vulnerability investigations from being reviewable if their associated vulnerability exceptions were rejected or approved.
* We improved the way the Scan Engine handles custom excluded file paths to be more compatible with certain Unix systems.
* We improved the way the Scan Engine identifies the Remote Method Invocation (RMI) protocol.
