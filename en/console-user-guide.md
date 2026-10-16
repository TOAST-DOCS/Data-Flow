<!-- machine_translated: true -->

<!-- pre-align:aligned sig=5f4a4fb25208 -->

<a id="data-analytics-dataflow-console-user-guide"></a>
## Data & Analytics > DataFlow > Console User Guide { #data-analytics-dataflow-console-user-guide }

DataFlow can be used in the following order:

Enable Service
1. Create a project.
2. Select the desired project.
3. Enable the DataFlow service.
Execute Flow
1. Create a flow.
2. Add the nodes you need, and enter settings to define how each node behaves.
3. Node connections determine the order of nodes' actions to complete the flow.
4. Execute the file.
5. Verify that the flow ran successfully by checking the log information.

<a id="management"></a>
## Management { #management }

Page that queries and manages the flow metadata information.
Click **Data & Analytics > DataFlow > Management**.

![management_main.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/management_main_2025_06.png)

<a id="search"></a>
### Search { #search }

Search for flows with given criteria. Searches for flows that contain search items in the name.

<a id="filter"></a>
### Filter { #filter }

Searches flows with given conditions. Provides filtering options based on the flow status values.

<a id="flow-list"></a>
### Flow List { #flow-list }

Display query results of flows in a table form.

* Displays simple flow metadata and current flow execution status.
* Sort based on last modified date.
* You can select a flow to use management features such as changing flows, viewing flow details, and starting flow.
* The filter condition feature allows to query specific flows in certain states.
* Once queried flows need to be refreshed to update Query results.
* You can view 12 flows per page and move pages with the previous, next buttons.

<a id="flow-list-flow-status-information"></a>
#### Flow Status Information

| Flow Running Status                                         | Description |
|---------------------------------------------------| --- |
| START_FAILED  | Failed to request flow running. |
| QUOTA_EXCEEDED | Failed to run the flow due to insufficient resources. |
| STARTING       | Freeing up resources to run the flow. |
| PREPARING      | Ready to run the flow. |
| RUNNING        | Flow is running. |
| ERROR              | An error occurred during flow running due to communication failure or authentication failure. If `ERROR` continues to occur, contact us via **Customer Support > Contact Us**. |
| STOP_FAILED   | Failed to request flow stop. |
| STOPPED        | Flow is stopped. |
| DRAINING       | Flow is draining. |
| UNKNOWN            | An error occurred for unknown reasons during the running of the flow. If `UNKNOWN` continues to occur, contact us via **Customer Support > Contact Us**. |

<a id="flow-list-flow-status-change-notifications"></a>
#### Flow Status Change Notifications
* You can be notified via email when the flow status is changed to a status set for notifications
* Flow status for notifications
    * RUNNING
    * ERROR
    * STOPPED
* Default recipients
    * A member with the **DataFlow ADMIN** role in the project where the **DataFlow** service you are using is enabled.


<a id="create-flow"></a>
### Create Flow { #create-flow }

Create metadata to define flows.

* Create flow metadata by adding name and description to identify a flow.
* Flow names can overlap with other flows.
* Select the execution mode according to the flow purpose.
* You can specify Flow Template to easily load flows of features users wants.
* You can set an instance type to run flows.

<a id="change-flow"></a>
### Change Flow { #change-flow }

Modify metadata of flows.

* Modify the existing flow name and description to reflect it in flow metadata.
* Flow templates cannot be specified.
* Changing flows are possible even when the flow is running.
* You cannot change the instance type to run flows. However, the changed instance type applies when running the next flow.

<a id="copy-flow"></a>
### Copy Flow { #copy-flow }

Create new metadata with existing flow definitions.

* Create new metadata with the phrase `_copy` added to the name of existing flow.
* Copy the flow logic of existing flow exactly as it is.
* You can also copy flows in running.
* Even though you copied a running flow, the flow is copied in a stopped status.
* If the current storage state of flows is temporary, it does not copy the last saved version of existing flows.
* If Scheduler copies the registered flow, the copied flow does not register the Scheduler.
* Copied flow is completely separate from an existing flow.

<a id="delete-flow"></a>
### Delete Flow { #delete-flow }

Delete flow metadata

* Completely delete flow metadata
* Deleted flow cannot be recovered again.
* Running flow cannot be deleted.

<a id="more---start-a-flow"></a>
### More - Start a flow { #more---start-a-flow }

Start a flow that is stopped.

* Each flow can run only one at a time.
* Temporarily saved flow starts with the version that was last saved.
* You cannot start a flow that has never been saved, only drafted.
* You cannot start a flow if it has never been saved even once.
* A flow cannot be started the same as the flow initiated by the user, even if the flow is already being run by Scheduler.

<a id="more---end-a-flow"></a>
### More - End a flow { #more---end-a-flow }

* You can end flows that are preparing to run, running, or draining.
* End flows without processing any remaining events.

<a id="more---end-after-flow-draining"></a>
### More - End after flow draining { #more---end-after-flow-draining }

* You can end a running flow after draining it.
* Draining means processing the remaining events in the flow.
* If the timeout time is exceeded, the draining will end without finishing.
* If the draining ends with timeouts remaining, exit immediately.
* A flow that is draining can be terminated directly via End Flow.

<a id="see-flow-in-details"></a>
## See Flow in Details { #see-flow-in-details }

Detail page that checks the details of selected flow.
Go to **Data & Analytics > DataFlow > Management > Click one of the flows**.
You can adjust the screen ratio by moving the boundary between the flow list area and the flow detail view area.
You can also adjust the screen proportion to specified percentage by using Resize Area button located in the upper right corner of Flow detail view area.

<a id="basic-information"></a>
### Basic Information { #basic-information }

Displays detailed flow metadata.

![management_basicinfo.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/management_basicinfo_2025_08.png)

* Display the name and description information entered when creating a flow.
* Display the flow creation date and creator name, latest modification date/modifier, and latest execution date/runner information.
* Display the total running time at the time of the most recent run.
* Display the instance type at the time of the most recent run.

<a id="basic-information---recent-logs"></a>
### Basic Information - Recent Logs { #basic-information---recent-logs }

* You can check Log information for current running flow directly through the Recent Logs.
* You can view logs for the recent 15 minutes.
* Logs for the V2 engine type can be viewed by selecting one of the following two options.
    * V2-JOB: Logs for flow execution scheduling and status management.
    * V2-TASK: Logs for data processing tasks defined in the flow.

<a id="basic-information---all-logs"></a>
### Basic Information - All Logs { #basic-information---all-logs }
* When you enable Log & Crash Search, you can copy Lucene Query to view flow logs from Log & Crash Search.

<a id="basic-information---instance-type"></a>
### Basic Information - Instance Type { #basic-information---instance-type }

* You can check and change the instance type to run flows.
    * However, the changed instance type applies when running the next flow.

<a id="flow-information"></a>
### Flow Information { #flow-information }

Define a flow logic.

![management_detail.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/management_detail_2025_08.png)

* Define a flow by importing flow components from Node type or Template from flow settings.
    * The Collapse/Expand button allows to zoom-in/zoom-out the flow screen.
* Adjust the flow screen to define flows and change the graph configuration.

    * Define a node behavior by entering the appropriate settings for each node.
        * [Detailed Node Guide](./node-config-guide/)
    * Define the flow of messages by linking connections among nodes.
        * Each flow can define only one Directed Acyclic Graph (DAG).
        * Connection that fits the category by respective type of node must be defined in order to be saved.
        * A node can have multiple connections.
            * If there are multiple input connections, messages from each connection are processed as single Flow.
            * If there are multiple output connections, it replicates messages that the node outputs to all connections to create a flow.
* Scaling adjustment and initializing, screen adjustment, and node alignment allow to adjust a flow so that it is visible on screen.
    * Adjust Scale
        * Scale the screen on which the flow graph is displayed.
        * Support scaling from 0.5 times to 2 times.
    * Initialize
        * Scale adjusted by 1x.
    * Adjust Screen
        * Adjust the scale and center the screen so that the flow is visible on one screen.
    * Align Node
        * Arrange the arrangement of flows so that the each step is well visible.
        * Adjust the scale and center the screen so that the Flow is visible on one screen.
* You can cancel/save/temporarily save the edited flow definition.
    * Cancel
        * Return the edited flow definition to the last saved/temporarily saved.
    * Save Temporarily
        * Provide features to store flows in incomplete form.
        * Temporarily saved flows cannot be started.
        * Temporarily saved flows cannot revert to the point of the last save.
        * You can record any changes made by entering commit name when temporarily saved.
    * Save
        * Statically assess and save the completeness of the flow.
        * If you fail the flow completeness evaluation, the Save request fails and displays the reason why  evaluation failed.
        * Saved flows can be requested to start.
        * When saving, you can enter the commit name to record changes made.

<a id="flow-information-schema"></a>
#### Schema
View the schema information of the flow.
* If a schema is defined in a Source node, you can view the input/output schema of each node.
* Schema information is displayed only when a schema is defined for all Source nodes in the flow.
* You can visually check the schema conversion results using field colors.
    * Green: Added fields
    * Blue: Fields with converted types
    * Yellow: Fields with detected type conflicts
* A warning is displayed when the same field name is defined with different types across multiple parent nodes.
* When the properties of a Filter node are changed, you can preview the schema conversion results in real time.
* For information on how to define schemas and the conversion behavior of each node, refer to the [Node Configuration Guide](./node-config-guide/).

<a id="modification-history"></a>
### Modification History { #modification-history }

Displays the history of flow modification.

![management_flowhistory.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/management_flowhistory_2025_08.png)

* Display the date and user information that has been modified.
* Display the commit name entered during Save/Temporarily Save.
* Display how the method was saved at the time, whether it was saved/temporarily saved.

<a id="execution-history"></a>
### Execution History { #execution-history }

Displays the history of flow execution in execution units. Each time a flow is started, one execution entry is created, and each row in the list represents one execution entry.
Executions that failed to start are also displayed along with the reason for failure.

![management_actionhistory.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/management_actionhistory_2025_08.png)

#### Execution List

* Displays the execution ID, status, start time, end time, duration, executor, Instance Type, and failure reason.
* Status
    * Completed executions are displayed as **Success** or **Failure**.
    * Ongoing executions display the current status (Starting, Preparing, Running, Draining, etc.).
    * Executions in the Start Failed, Quota Exceeded, Error, or Stop Failed status are displayed as **Failure**. Executions whose previous status was Error are also displayed as **Failure**.
    * Executions in the Error status have not yet ended, so the end time is displayed as **In Progress**.
* Start Time, End Time, and Duration
    * For ongoing executions, the end time is displayed as **In Progress**, and the duration is calculated based on the current time.
    * Executions that have just been requested to start and do not yet have results display the end time as **Preparing**.
    * Executions that failed to start have no execution period, so the start time and duration are displayed as `-`, and the end time shows the time the failure was processed.
* Executor
    * Displays the email of the user who requested the execution with partial masking.
    * If the execution was performed by the system rather than a user, it is displayed as **System (Schedule)**.
* Instance Type
    * Displays the Instance Type at the time of execution.
* Failure Reason
    * Displays the failure reason for failed executions. The failure reason is one of the following four:
        * The Engine Type of the flow cannot be executed. Contact the Customer Center.
        * The executable flow resources have been exceeded. Stop the running flow and try again.
        * Failed to execute the flow. If it continues to fail, contact the Customer Center.
        * The flow was stopped because Log & Crash Search authentication failed. Check the User Access Key ID, Secret Access Key, and appkey of the Source Node, then run the flow again.
    * Successful executions and executions before this feature was introduced are displayed as `-`.
    * Except for Log & Crash Search authentication failure, the cause of errors that occur during execution is not displayed in the failure reason. Check the infrastructure events and logs in the execution details.
* Filter
    * You can narrow the list by selecting one of the following from the Status Filter: All, Success, or Failure.
    * You can select one of the following from the Period Filter: Last 1 Day, Last 7 Days, Last 30 Days, or All. The default is All, and the period is based on the time the execution was requested.
    * If no executions match the conditions, click **Reset Filter** to return to the full list.
* If there are ongoing executions, the list is automatically refreshed periodically to reflect status changes.
* Click a row to go to the execution details.

#### Execution Details

Displays information for the selected execution. Click **Back to List** to return to the execution list.

* Overview
    * Displays the execution ID, execution status, start time, end time, duration, executor, stop requester, Instance Type, and failure reason.
    * The execution status displays the current state of that execution, not success or failure.
    * For executions stopped by the system due to a Log & Crash Search authentication failure, the stop requester is displayed as **System (Schedule)**.
* Infrastructure Events
    * Displays infrastructure events that occurred during this execution (such as image pull failure, out-of-memory termination, etc.).
    * Displays the message, count, first occurrence time, and last occurrence time.
    * Click a row to expand and view the full message.
* Logs
    * **View Real-Time Logs**: Displayed only when the selected execution is still in progress. The error, Draining, and stop-failed states are also considered as in-progress. You can view logs for the recent 15 minutes. Select either V2-JOB or V2-TASK to query logs.
    * **Copy Log Query**: Displayed when Log & Crash Search is enabled. Copies the Lucene Query that allows you to view only the logs for this execution in Log & Crash Search. For completed executions, view the logs in Log & Crash Search.
* Click **View Metric Details** to view the metrics for this execution in a modal window.
    * Executions that failed to start have no metrics, so the button is disabled.

#### Execution Metrics

Displays a graph and metric charts for the selected execution in a modal window.

* Flow Graph
    * Displays the graph based on the flow definition saved at the time of execution. Even if the flow was modified afterward, the graph from the time of execution is displayed.
    * Clicking a node changes the Event In/Out chart to the metrics for that node. Deselecting a node returns the view to the overall flow metrics.
    * If the graph from the time of execution cannot be found, a message is displayed and per-node metrics cannot be viewed.
* Chart
    * Displays charts for Event In/Out, CPU usage, memory usage, and network data transmission and reception.
    * Excludes data from other executions of the same flow and displays only the data for the selected execution.
    * CPU usage and memory usage display both V2-JOB and V2-TASK graphs.
* Duration
    * Select one of the following: Full Execution, Last 1 Hour, Last 6 Hours, or Last 24 Hours. Periods longer than the execution time are not displayed.
    * For an ongoing execution, metrics up to the current time are displayed.
    * Metrics are retained for up to the last 16 days. If an execution lasted more than 16 days, selecting **Full Execution** displays only the metrics from the last 16 days.
* If the execution time is less than 5 minutes, the chart may not display trends due to insufficient aggregation intervals.

<a id="schedule-list"></a>
### Schedule List { #schedule-list }

![management_schedulelist.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/management_schedulelist_2025_08.png)

* You can register schedules on a new window by clicking the **Go to Cloud Scheduler** button in the upper left corner.
* Display the schedule list of the flows registered through the Cloud Scheduler.
* Display schedule name, target template name, execution type, start date, end date, and active status.
* You can check details of the schedule from a new window by clicking the button to the right of the schedule name.

!!! danger "Caution"
    Only display schedules registered in Cloud Scheduler within the same project as Flow.
    Schedules registered in Cloud Scheduler of other projects will not be displayed.

<a id="monitoring"></a>
## Monitoring { #monitoring }

Display Monitoring information for running flow or node.
Click **Data & Analytics > DataFlow > Monitoring**.
You can adjust the screen ratio by moving boundaries between flow screen area and monitoring area.
Also you can adjust screen proportion to the specified percentage by using the Resize Area button located in upper right corner of the Monitoring area.

![monitoring.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/monitoring_2025_08.png)

!!! tip "Note"
    The Flow Graph on the Monitoring tab always reflects the latest flow definition.
    If you view metrics from a past execution after modifying a flow, the graph and metrics may appear misaligned.
    To view the metrics of a specific execution against the graph at the time of that execution, use **View Metric Details** for that execution in **Management > Execution History**.

<a id="monitoring-flow-list"></a>
### Flow List { #monitoring-flow-list }

Display a list of flows that can be monitored.

* You can select a flow to view detailed monitoring information.
* You can expand the flow list tree to select each Node belongs to the Flow for Monitoring Details information.
* By collapsing the list, you can expand the detailed page for monitoring.
* You can also check Monitoring information of deleted flows by clicking **View Deleted Flow Diagrams** button.


<a id="flow-screen-area"></a>
### Flow Screen Area { #flow-screen-area }

Area that displays the appearance of flow.

* Display a complete graph of currently running flow.
* You can select a node to view monitoring information specific to the respective node.
* You can modify the flow screen to position overall graph as desired.

<a id="monitoring-area"></a>
### Monitoring Area { #monitoring-area }

It is area to display the monitoring chart.

* You can view the monitoring information for selected flow or node in a chart.
* Scaling Adjustment and initializing allow you to adjust a flow so that it is visible on the screen.
    * Adjust Scale
        * Scale the screen on which the flow graph is displayed.
        * Support the scaling from 0.5x to 2x.
    * Initialize
        * The scale is adjusted by 1x through scaling adjustment.
* Select Chart Period
    * Adjust Overall Period
        * Click the toggle button to synchronize Chart zoom-in actions to all Charts.
    * Current Time
        * Set the chart duration to the current time.
    * Select Period
        * Select the time period to check monitoring information.
        * Use Refresh Settings to select how often the monitoring information is refreshed.
        * The cancel button allows you not to apply the time period you want to change.
    * Chart
        * You can zoom the Chart zoom-in/zoom-out by specifying the chart range.
* Select Type
    * V2-ALL
        * Event In/Out and network data transmission and reception are displayed in a single graph.
        * CPU usage rate and memory usage display the V2-JOB and V2-TASK graphs separately.
    * V2-JOB, V2-TASK
        * Event In/Out and network data transmission and reception are not displayed.

<a id="template"></a>
## Template { #template }

It is a page that queries, creates, and modifies the template metadata information.
Click **Data & Analytics>DataFlow> Templates**.

![template_main.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/template_main_2025_08.png)

<a id="template-search"></a>
### Search { #template-search }

Searche templates based on the given criteria. If Search by template name, it searches for templates that contain search terms in their names.

<a id="query-templates"></a>
### Query Templates { #query-templates }

Display Query result template in a table form.

* Display simple template metadata.
* Sort based on the last modified date.
* Selecting a template allows you to use features such as Changing Template, Viewing Template details, and so on.
* For once queried templates, you have to click Refresh to update query results.
* You can query 12 templates per page and navigate through the Previous and Next buttons.

<a id="create-templates"></a>
### Create Templates { #create-templates }

Create metadata to define a template.

* Create template metadata by adding name and description to identify a template.
* Select an engine type. The default is V1.
* Flow templates cannot be specified.
* Created templates can be found in the node type list when defining flows or template logics.

<a id="change-templates"></a>
### Change Templates { #change-templates }

Modify metadata in the template.

* Modify the existing template name and description to be reflected in the template metadata.
* Flow templates cannot be specified.

<a id="copy-templates"></a>
### Copy Templates { #copy-templates }

Create new metadata with existing template definitions.

* Create new metadata with the `_copy` statement added to the existing template name.
* Copy the flow logic of the existing template as it is.
* Copied template is completely separate template from the existing template.

<a id="delete-templates"></a>
### Delete Templates { #delete-templates }

Delete template metadata

* Completely delete template metadata
* Deleted template cannot be recovered.

<a id="see-templates-in-details"></a>
## See Templates in Details { #see-templates-in-details }

This is a detail page for viewing detailed information about the selected template.
Go to **Data & Analytics > DataFlow > Templates > and Click one of the template**
You can adjust the screen ratio by moving boundaries between the template list area and See Templates in Details.
Also you can adjust the screen proportion to the specified percentage by using the Resize Area button located in upper right corner of See Templates in Details.

![template_detail.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/template_detail_2025_08.png)

<a id="template-information"></a>
### Template Information { #template-information }

Define template logics

* Define a template by importing flow components from the node type or template from Template Settings.
    * The **Collapse** and **Expand** button allows you to zoom-in/zoom-out the template screen.
* Adjust the template screen to define a template and change graph configuration.
    * Unlike flows, templates can freely save unfinished flows.
* Scaling adjustment and initializing, screen adjustment, and node alignment allow to adjust a flow so that it is visible on the screen.
    * Adjust Scale
        * Scale the screen on which the flow graph is displayed.
        * Support scaling from 0.5x to 2x.
    * Initialize
        * Resets the adjusted scale to 1x.
    * Adjust Screen
        * Adjust the scale and center the screen so that the flow is visible on one screen.
    * Align Node
        * Arrange the arrangement of flows so that the each step is well visible.
        * Adjust the scale and center the screen so that the flow is visible on one screen.
* You can cancel/save edited the flow definition.
    * Cancel
        * Returns the edited template definition to the state it was in when it was last saved/saved temporarily.
    * Save
        * Allows you to save a flow in incomplete form.
        * Saved templates can be loaded from the template category in the node type.

<a id="settings"></a>
## Settings { #settings }
Manage the settings for the service.
Go to **Data & Analytics > DataFlow > Settings**.

<a id="log-crash-search-settings"></a>
### Log & Crash Search settings { #log-crash-search-settings }
The feature to integrate your flow logs with Log & Crash Search you set.
To store logs in the Log & Crash Search service, you must enable the Log & Crash Search service, which is available at an additional cost.


![settings_lncs_v2.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/settings_lncs_v2.png)

① Click **Log & Crash Search Save Settings**.
② Enter Log & Crash Search **Appkey** where you want to save the logs and **Log Level** to save.
③ Click **Save** to complete settings.
④ If you don't want to use the Log & Crash Search integration feature, click **Delete** to delete the save information.

Log Field

|             Name |                              Description |
|---------------|--------------------------------|
|       logLevel | Log level (INFO, WARN, ERROR, FATAL) |
|      logSource |                          `Flow` |
|           host |                      `DataFlow` |
|         appkey |                 DataFlow service appkey |
|         flowId |                          Flow ID |
| flowInstanceId |                     Flow Instance ID |

<a id="validation-settings"></a>
### Validation Settings { #validation-settings }
![settings_acc.png](http://static.toastoven.net/prod_dataflow/ko/console_user_guide/settings_acc.png)

You can set whether to use validation for flows and nodes.
If Validity Check is set to **Not Use**, node validation is not automatically performed when saving flows, validation for each node is not available.