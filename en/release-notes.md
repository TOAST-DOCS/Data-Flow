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