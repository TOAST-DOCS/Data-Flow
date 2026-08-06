<a id="data-analytics-dataflow-release-notes"></a>
## Data & Analytics > DataFlow > Release Notes { #data-analytics-dataflow-release-notes }

<a id="may-27-2026"></a>
## May 27, 2026 { #may-27-2026 }

<a id="feature-updates"></a>
### Feature Updates { #feature-updates }
* Added new nodes
    * Source
        * (NHN Cloud) EasyQueue
        * (NHN Cloud) Data Lake Storage
    * Sink
        * (NHN Cloud) EasyQueue
        * (NHN Cloud) Data Lake Storage

<a id="april-28-2026"></a>
## April 28, 2026 { #april-28-2026 }
<a id="added-features"></a>
### Added Features { #added-features }
* Added a feature to define and use the schema of Source nodes in flow information.

<a id="april-28-2026-feature-updates"></a>
### Feature Updates { #april-28-2026-feature-updates }
* Added new nodes
    * Filter
        * Tokenizer
        * Sampling
        * Stop Words Remover
        * Pattern Extractor (Grok)
    * Branch
        * Dataset Split
* Added the **Schema** property to the JSON node.


<a id="march-24-2026"></a>
## March 24, 2026 { #march-24-2026 }
<a id="march-24-2026-feature-updates"></a>
### Feature Updates { #march-24-2026-feature-updates }
* End of support for V1 engine type
    * Support for the V1 engine type has ended, and existing V1 engine type flows can no longer be executed.
    * All flows are created with the V2 engine type.
* Improved the recent log feature for the V2 engine type.
* Improved the monitoring feature for the V2 engine type.
* Added nodes supported by the V2 engine type.
    * Source
        * Kafka
    * Filter
        * Cipher
        * Remove Fields
    * Sink
        * Kafka
* Added the Parquet codec to the (Amazon) S3 and (NHN Cloud) Object Storage Sink nodes.

<a id="bug-fixes"></a>
### Bug Fixes { #bug-fixes }
* Fixed an issue where the collapse button in the left tree structure of the monitoring screen did not work.

<a id="february-10-2026"></a>
## February 10, 2026 { #february-10-2026 }
<a id="february-10-2026-feature-updates"></a>
### Feature Updates { #february-10-2026-feature-updates }
* End of support for Cipher node in V1 engine
    * Support for the Cipher node feature in the V1 engine type will be discontinued as of February 10, 2026.
    * If a Cipher node is included in an existing V1 engine type flow, the flow cannot be executed.
    * The Cipher node cannot be selected in flows newly created with the V1 engine type.

<a id="december-23-2025"></a>
## December 23, 2025 { #december-23-2025 }

<a id="december-23-2025-added-features"></a>
### Added Features { #december-23-2025-added-features }
* Added engine type
    * V1: it is a legacy engine and is fully compatible with all standard nodes and existing templates.
    * V2: it provides faster performance than V1 with the latest architecture-based engine.

<a id="october-28-2025"></a>
## October 28, 2025 { #october-28-2025 }

<a id="october-28-2025-bug-fixes"></a>
### Bug Fixes { #october-28-2025-bug-fixes }
* Fixed an issue where CPU, Memory, and Network metrics in the Monitoring tab were only displayed for the most recently executed flow.
* Fixed an issue where saving a flow or template configured using a template containing sensitive information would fail.

<a id="september-23-2025"></a>
## September 23, 2025 { #september-23-2025 }

<a id="september-23-2025-feature-updates"></a>
### Feature Updates { #september-23-2025-feature-updates }
* Made modification so that sensitive information is marked with asterisks when setting up nodes.
    * (NHN Cloud) Object Storage > Secret Key
    * (NHN Cloud) CloudTrail > Appkey
    * (NHN Cloud) Log & Crash Search > SecretKey
    * JDBC > Password
    * (Amazon) S3 > Secret Key
* (NHN Cloud) Added **Search Query** attribute to the Log & Crash Search node.

<a id="september-23-2025-bug-fixes"></a>
### Bug Fixes { #september-23-2025-bug-fixes }
* Fixed an issue where charts would not be displayed when selecting a flow that had never been run in the Monitoring tab.

<a id="august-26-2025"></a>
## August 26, 2025 { #august-26-2025 }

<a id="august-26-2025-feature-updates"></a>
### Feature Updates { #august-26-2025-feature-updates }
* Added a new **Schedule List** tab on the Details screen where you can check the reservation schedule.
* Moved the **Go to Cloud Scheduler Console** button for scheduling flows from the Basic Info tab to the **Schedule List** tab.

<a id="july-29-2025"></a>
## July 29, 2025 { #july-29-2025 }

<a id="july-29-2025-feature-updates"></a>
### Feature Updates { #july-29-2025-feature-updates }
* Updated the execution mode to be configured at the flow.
* Renamed CloudTrail event names to match the terminology used in the DataFlow console.

<a id="july-29-2025-bug-fixes"></a>
### Bug Fixes { #july-29-2025-bug-fixes }
* Fixed an issue where the flow did not terminate properly after draining.
* Fixed an issue where log retrieval requests were still made when the View Recent Logs window was open and the flow had ended.

<a id="june-24-2025"></a>
## June 24, 2025 { #june-24-2025 }

<a id="june-24-2025-feature-updates"></a>
### Feature Updates { #june-24-2025-feature-updates }
* Replaced the scheduling functionality to integrate with the Cloud Scheduler service.
* Added an execution mode setting to the source node.
    * STREAMING: Processes data in real time without exiting the flow.
    * BATCH: Processes a set amount of data and then terminates the flow.

<a id="may-27-2025"></a>
## May 27, 2025 { #may-27-2025 }

<a id="may-27-2025-feature-updates"></a>
### Feature Updates { #may-27-2025-feature-updates }
* Added new nodes
    * Filter
        * Mutate: A node that can rename fields or transform field values.
    * Sink
        * Stdout: A node that outputs flow events to the log. It can be used for debugging purposes.

<a id="may-27-2025-bug-fixes"></a>
### Bug Fixes { #may-27-2025-bug-fixes }
* Fixed an issue where View Logs feature did not function properly when logs accumulated too quickly.

<a id="march-4-2025"></a>
## March 4, 2025 { #march-4-2025 }

<a id="march-4-2025-bug-fixes"></a>
### Bug Fixes { #march-4-2025-bug-fixes }
* Fixed an issue where flow event in/out graphs are not displayed correctly.

<a id="december-24-2024"></a>
## December 24, 2024 { #december-24-2024 }

<a id="december-24-2024-feature-updates"></a>
### Feature Updates { #december-24-2024-feature-updates }
* Integrated (Amazon) S3 Sink node and (Amazon) S3 - Parquet Sink node.
* Integrated (NHN Cloud) Object Storage Sink node and (NHN Cloud) Object Storage - Parquet Sink node.

<a id="september-25-2024"></a>
## September 25, 2024 { #september-25-2024 }

<a id="september-25-2024-feature-updates"></a>
### Feature Updates { #september-25-2024-feature-updates }
* Stabilized the flow startup process.
  
<a id="august-27-2024"></a>
## August 27, 2024 { #august-27-2024 }

<a id="august-27-2024-feature-updates"></a>
### Feature Updates { #august-27-2024-feature-updates }
* Improved how the Last Executed Time is calculated. 
* Made modifications so that, when displaying the node settings, required items are displayed first.

<a id="july-23-2024"></a>
## July 23, 2024 { #july-23-2024 }

<a id="july-23-2024-feature-updates"></a>
### Feature Updates { #july-23-2024-feature-updates }
* Improved to use the enter key to input data when entering data in the `array of strings` type in the node settings screen.
* Separated the **Match** setting for the Date node into a **Source Field** setting and a **Formats** setting.
* Made notifications so that, when starting or ending a flow that has already started or ended, `FLOW_ALREADY_STARTED`/`FLOW_ALREADY_STOPPED` instead of `ERROR` appears.
* Made notifications so that, when saving or deleting Log & Crash Search log save settings, "Save Log & Crash Search save settings" or "Delete Log & Crash Search save settings" are left in CloudTrail logs.

<a id="july-1-2024"></a>
## July 1, 2024 { #july-1-2024 }

<a id="july-1-2024-feature-updates"></a>
### Feature Updates { #july-1-2024-feature-updates }
* Added the feature to set the instance type when running flows 
* (Amazon) Changed the endpoint, region settings for the (Amazon) S3 Source, Sink, and (Amazon) S3 - Parquet Sink nodes from required to optional 
    * The nodes will work correctly if only one of the endpoint, region settings is entered.

<a id="july-1-2024-bug-fixes"></a>
### Bug Fixes { #july-1-2024-bug-fixes }
* Fixed an issue where no CloudTrail logs were left when exiting after flow draining, Log & Crash Search logs save settings, enabling and disabling validation.
* Fixed an issue where the scheduling feature was not working intermittently. 
* Fixed an issue where the Cipher node was not working intermittently. 
* Fixed an issue where the (Amazon) S3 Source, Sink, and (Amazon) S3 - Parquet Sink nodes were not able to access the public bucket.
* Fixed an issue where using an unsupported JDBC driver when saving a flow containing a JDBC node with validation disabled would expose `JDBC_UNSUPPORTED_DRIVER` instead of `ERROR`. 
* Fixed an issue where saving a flow containing a Cipher node with validation enabled would expose the appropriate error code instead of `ERROR` if the Cipher node information was entered incorrectly. 
* Fixed an issue where status channge notifications were not sent for flows run by a user who had canceled membership.

<a id="may-28-2024"></a>
## May 28, 2024 { #may-28-2024 }

<a id="may-28-2024-feature-updates"></a>
### Feature Updates { #may-28-2024-feature-updates }
* Deleted some settings.
    * Common > Enable Metrics
    * Filter Node Common > Periodic Flush
    * (NHN Cloud) Object Storage > ACL
    * (NHN Cloud) Object Storage > Storage Class
    * (NHN Cloud) Object Storage - Parquet > ACL
    * (NHN Cloud) Object Storage - Parquet > Storage Class
* Made modifications so that, when monitoring for periods longer than 7 days, data would be more precise.

<a id="may-28-2024-bug-fixes"></a>
### Bug Fixes { #may-28-2024-bug-fixes }
* Fixed the revision history of unsubscribed users to appear as "UNKNOWN USER" instead of blank.
* Fixed validation of Object Storage, S3 nodes to expose "S3_NO_SUCH_BUCKET" instead of "ERROR" if an invalid bucket name is entered.
* Fixed an issue where node names were different on the flow setup screen and the monitoring screen.
* Fixed an issue where state change notifications were not being sent for flows run via scheduling.

<a id="april-23-2024"></a>
## April 23, 2024 { #april-23-2024 }

<a id="april-23-2024-added-features"></a>
### Added Features { #april-23-2024-added-features }
* Added the flow status change notifications feature.

<a id="april-23-2024-bug-fixes"></a>
### Bug Fixes { #april-23-2024-bug-fixes }
* Fixed an issue where, when querying flow monitoring with many nodes, the query fails.

<a id="march-26-2024"></a>
## March 26, 2024 { #march-26-2024 }

<a id="march-26-2024-added-features"></a>
### Added Features { #march-26-2024-added-features }
* Added the feature to end after flow draining
    * Added the feature to end a flow after draining that processes all remaining events in the flow.
    * A flow that is draining can be ended directly via End Flow.
    * If the draining ends within the timeout, or if the timeout is exceeded during draining, the flow will end at that point.

<a id="february-27-2024"></a>
## February 27, 2024 { #february-27-2024 }

<a id="february-27-2024-feature-updates"></a>
### Feature Updates { #february-27-2024-feature-updates }
* Changed the words "file" and "object" in the descriptions of S3 and Object Storage nodes to "object".
* Fixed loading UI to appear on flow save, start, stop, and validation requests.
* Made the order of node setup more natural.

<a id="february-27-2024-bug-fixes"></a>
### Bug Fixes { #february-27-2024-bug-fixes }
* Fixed an issue where saving flows with S3, Object Storage Sink nodes would intermittently leave temporary objects for testing during validation.
* Fixed an issue where deleting a flow would not delete the scheduling stored in that flow.
* Fixed an issue where, when creating a flow immediately after activating a project, the flow would fail to run.
* Fixed an issue where, when looking up monitoring for a long period of time, the lookup would fail.
* Fixed an issue where the execution state of a validation feature would go from `Activating` to `Active` faster than it should.

<a id="january-23-2024"></a>
## January 23, 2024 { #january-23-2024 }

<a id="january-23-2024-feature-updates"></a>
### Feature Updates { #january-23-2024-feature-updates }
* Fixed a bug where validation would not turn on unless creating the first flow.

<a id="december-19-2023"></a>
## December 19, 2023 { #december-19-2023 }

<a id="december-19-2023-added-features"></a>
### Added Features { #december-19-2023-added-features }
* Added a new node
    * Source
        * Added the feature to run queries against the DB to get data.
    * Sink
        * Added the feature to store data in Object Storage with the Parquet type.
        * Added the feature to store data in S3 with the Parquet type.

<a id="december-19-2023-bug-fixes"></a>
### Bug Fixes { #december-19-2023-bug-fixes }
* Fixed a bug where validation would not turn on properly after creating a flow.

<a id="november-28-2023"></a>
## November 28, 2023 { #november-28-2023 }

<a id="november-28-2023-feature-updates"></a>
### Feature Updates { #november-28-2023-feature-updates }
* Added error codes when saving or validating flows.
* Changed to allow you to choose whether or not to use the validation feature.

<a id="october-31-2023"></a>
## October 31, 2023 { #october-31-2023 }

<a id="october-31-2023-feature-updates"></a>
### Feature Updates { #october-31-2023-feature-updates }
* Improved the error messages that occur while initializing the DataFlow service environment to be more user-friendly.

<a id="october-17-2023"></a>
## October 17, 2023 { #october-17-2023 }

<a id="october-17-2023-feature-updates"></a>
### Feature Updates { #october-17-2023-feature-updates }
* Added the SecretKey property to Log & Crash Search Source nodes.

<a id="september-26-2023"></a>
## September 26, 2023 { #september-26-2023 }

<a id="september-26-2023-feature-updates"></a>
### Feature Updates { #september-26-2023-feature-updates }
* Modified to support At Least Once when processing data.
* Added new options for time format in Prefix Settings for S3 and Object Storage Sink nodes.

<a id="september-26-2023-bug-fixes"></a>
### Bug Fixes { #september-26-2023-bug-fixes }
* Fixed a bug where the flow would not terminate if an error occurred during the shutdown of the Log & Crash Search node.
* Fixed a bug where, when copying and running a flow containing a Cipher Filter node, the flow would not work correctly.
* Fixed a bug where, when an error occurred while terminating a flow, a request to terminate again would fail.

<a id="july-25-2023"></a>
## July 25, 2023 { #july-25-2023 }

<a id="july-25-2023-bug-fixes"></a>
### Bug Fixes { #july-25-2023-bug-fixes }

* Modified so that, when a flow fails abnormally during execution, it can resume execution from the last execution point.

<a id="june-27-2023"></a>
## June 27, 2023 { #june-27-2023 }

<a id="june-27-2023-added-features"></a>
### Added Features { #june-27-2023-added-features }

<!-- TODO: translate body -->

<!-- pre-align: ko에 대응 섹션 없음 — 검토 필요 (Mistranslation of k70 (기능 추가): target reads 'Feature Updates' but no ko '기능 개선/변경' exists in this section; 'Added Features' is the correct counterpart) -->
<a id="june-27-2023-bug-fixes"></a>
### Feature Updates { #june-27-2023-bug-fixes }
* Added a feature to enable Log & Crash Search
    * Added a feature to save flow logs in Log & Crash Search.

### Bug Fixes
* Modified the activation timing of the View Log button
    * Modified the activation timing of the View Log button to the PREPARING stage.

## March 28, 2023

### Added Features

<!-- TODO: translate body -->

<!-- pre-align: ko에 대응 섹션 없음 — 검토 필요 (Mistranslation of k73 (기능 추가): target reads 'Feature Updates' but no ko '기능 개선/변경' exists in this section; 'Added Features' is the correct counterpart) -->
### Feature Updates

* Added new nodes
    * Filter
        * Added various data processing methods such as Alter, Date, UUID, Split, and Truncate.
* Added a flow usage exposure feature
    * Added a feature to check flow usage from the console in real time.

### Bug Fixes

* Fixed a bug where flow usage begins to appear before entering the PREPARING stage.

<a id="march-28-2023"></a>
## February 28, 2023 { #march-28-2023 }

<a id="march-28-2023-added-features"></a>
### Added Features { #march-28-2023-added-features }

<!-- TODO: translate body -->

<!-- pre-align: ko에 대응 섹션 없음 — 검토 필요 (Mistranslation of k76 (기능 추가): target reads 'Feature Updates' but no ko '기능 개선/변경' exists in this section; 'Added Features' is the correct counterpart) -->
<a id="march-28-2023-bug-fixes"></a>
### Feature Updates { #march-28-2023-bug-fixes }

* Added new nodes
    * Source
        * Added a feature to import data from NHN Cloud Object Storage node.
        * Added a feature to import data through Amazon S3 interface.
        * Added a feature to import data through Apache Kafka.
    * Filter
        * Added a feature to preprocess data in various ways by adding Grok, JSON, and CSV nodes.

<a id="february-28-2023"></a>
## January 6, 2023 { #february-28-2023 }

<a id="february-28-2023-added-features"></a>
### Bug Fixes { #february-28-2023-added-features }

* Fixed an issue where the first button click after adding a node in the flow edit screen does not work.
* Fixed an issue where a field is not added when the cipher node is configured to add fields.

<a id="january-6-2023"></a>
## December 27, 2022 { #january-6-2023 }

<a id="january-6-2023-bug-fixes"></a>
### Release of a New Service { #january-6-2023-bug-fixes }

* DataFlow is a service that creates and run ETL flows.
* Supports sources listed below.
    * NHN Cloud Log & Crash Search
    * NHN Cloud CloudTrail
* Supports a filter below.
    * Cipher (Required to connect with NHN Cloud Secure Key Manager)
* Supports sinks below.
    * NHN Cloud Object Storage
    * Amazon S3 (Compatible)
    * Apache Kafka
