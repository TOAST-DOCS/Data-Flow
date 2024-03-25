## Data & Analytics > DataFlow > Release Notes

### March 26, 2024
#### Added Features
* Added the feature to end after flow draining
    * Added the feature to end a flow after draining that processes all remaining events in the flow.
    * A flow that is draining can be ended directly via End Flow.
    * If the draining ends within the timeout, or if the timeout is exceeded during draining, the flow will end at that point.

### February 27, 2024
#### Feature Updates
* Changed the words "file" and "object" in the descriptions of S3 and OBS nodes to "object".
* Fixed loading UI to appear on flow save, start, stop, and validation requests.
* Made the order of node setup more natural.

#### Bug Fixes
* Fixed an issue where saving flows with S3, OBS Sink nodes would intermittently leave temporary objects for testing during validation.
* Fixed an issue where deleting a flow would not delete the scheduling stored in that flow.
* Fixed an issue where, when creating a flow immediately after activating a project, the flow would fail to run.
* Fixed an issue where, when looking up monitoring for a long period of time, the lookup would fail.
* Fixed an issue where the execution state of a validation feature would go from "Activating" to "Active" faster than it should.

### January 23, 2024
#### Feature Updates
* Fixed a bug where validation would not turn on unless creating the first flow.

### December 19, 2023
#### Added Features
* Added a new node
    * Source
        * Added the feature to run queries against the DB to get data.
    * Sink
        * Added the feature to store data in Object Storage with the parquet type.
        * Added the feature to store data in S3 with the parquet type.

#### Bug Fixes
* Fixed a bug where validation would not turn on properly after creating a flow.

### November 28, 2023
#### Feature Updates
* Added error codes when saving or validating flows.
* Changed to allow you to choose whether or not to use the validation feature.

### October 31, 2023
#### Feature Updates
* Improved the error messages that occur while initializing the DataFlow service environment to be more user-friendly.

### October 17, 2023
#### Feature Updates
* Added the SecretKey property to Log & Crash Search Source nodes.

### September 26, 2023
#### Feature Updates
* Modified to support At Least Once when processing data.
* Added new options for time format in Prefix Settings for S3 and OBS Sink nodes.

#### Bug Fixes
* Fixed a bug where the flow would not terminate if an error occurred during the shutdown of the Log & Crash Search node.
* Fixed a bug where, when copying and running a flow containing a Cipher Filter node, the flow would not work correctly.
* Fixed a bug where, when an error occurred while terminating a flow, a request to terminate again would fail.

### July 25, 2023

#### Bug Fixes

* Modified so that, when a flow fails abnormally during execution, it can resume execution from the last execution point.

### June 27, 2023

#### Feature Updates
* Added a feature to enable Log & Crash Search
  * Added a feature to save flow logs in Log & Crash Search.

#### Bug Fixes
* Modified the activation timing of the View Log button
  * Modified the activation timing of the View Log button to the PREPARING stage.

### March 28, 2023

#### Feature Updates

* Added new nodes
    * Filter
        * Added various data processing methods such as Alter, Date, UUID, Split, and Truncate.
* Added a flow usage exposure feature
    * Added a feature to check flow usage from the console in real time.

#### Bug Fixes

* Fixed a bug where flow usage begins to appear before entering the PREPARING stage.

### February 28, 2023

#### Feature Updates

* Added new nodes
    * Source
        * Added a feature to import data from NHN Cloud Object Storage node.
        * Added a feature to import data through Amazon S3 interface.
        * Added a feature to import data through Apache Kafka.
    * Filter
        * Added a feature to preprocess data in various ways by adding grok, json, csv nodes.

### January 6, 2023

#### Bug Fixes

* Fixed an issue where the first button click after adding a node in the flow edit screen does not work.
* Fixed an issue where a field is not added when the cipher node is configured to add fields.

### December 27, 2022

#### Release of a New Service

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