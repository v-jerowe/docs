---
title: "Service Trace Viewer Tool (SvcTraceViewer.exe)"
description: Use Service Trace Viewer to merge, view, and filter trace messages in the log so that you can diagnose, repair, and verify WCF service issues.
ms.date: "03/30/2017"
---
# Service Trace Viewer Tool (SvcTraceViewer.exe)

Windows Communication Foundation (WCF) Service Trace Viewer Tool helps you analyze diagnostic traces that are generated by WCF. Service Trace Viewer provides a way to easily merge, view, and filter trace messages in the log so that you can diagnose, repair, and verify WCF service issues.

## Configuring Tracing

Diagnostic traces provide you with information that shows what is happening throughout your application's operation. As the name implies, you can follow operations from their source to destination and through intermediate points as well.

You can configure tracing using the application's configuration file—either Web.config for Web-hosted applications, or *Appname*.config for self-hosted applications. The following is an example:

```xml
<system.diagnostics>
    <trace autoflush="true" />
    <sources>
            <source name="System.ServiceModel"
                    switchValue="Information, ActivityTracing"
                    propagateActivity="true">
            <listeners>
               <add name="sdt"
                   type="System.Diagnostics.XmlWriterTraceListener"
                   initializeData= "SdrConfigExample.e2e" />
            </listeners>
         </source>
    </sources>
</system.diagnostics>
```

In this example, the name and type of the trace listener is specified. The Listener is named `sdt` and the standard .NET Framework trace listener (System.Diagnostics.XmlWriterTraceListener) is added as the type. The `initializeData` attribute is used to set the name of the log file for that Listener to be `SdrConfigExample.e2e`. For the log file, you can substitute a fully-qualified path for a simple file name.

The example creates a file in the root directory called SdrConfigExample.e2e. When you use the Trace Viewer to open the file as described in the "Opening and Viewing WCF Trace Files" section, you can see all the messages that have been sent.

The tracing level is controlled by the `switchValue` setting. The available tracing levels are described in the following table.

|Trace Level|Description|
|-----------------|-----------------|
|Critical|-   Logs Fail-Fast and Event Log entries, and trace correlation information. The following are some examples of when you might use the Critical level:<br />-   Your AppDomain went down because of an unhandled exception.<br />-   Your application fails to start.<br />-   The message that caused the failure originated from the process MyApp.exe.|
|Error|-   Logs all exceptions. You can use the Error level in the following situations:<br />-   Your code crashed because of an Invalid Cast Exception.<br />-   A "failed to create endpoint" exception is causing your application to fail on startup.|
|Warning|-   A condition exists that may subsequently result in an error or critical failure. You can use this level in the following situations:<br />-   The application is receiving more requests than its throttling settings allows.<br />-   The receiving queue is at 98 percent of its configured capacity.|
|Information|-   Messages helpful for monitoring and diagnosing system status, measuring performance, or profiling are generated. You can utilize such information for capacity planning and performance management. You can use this level in the following situations:<br />-   A failure occurred after the message reached the AppDomain and was deserialized.<br />-   A failure occurred while the HTTP binding was being created.|
|Verbose|-   Debug-level tracing for both user code and servicing. Set this level when:<br />-   You are not sure which method in your code was called when the failure occurred.<br />-   You have an incorrect endpoint configured and the service failed to start because the entry in the reservation store is locked.|
|ActivityTracing|Flow events between processing activities and components.<br /><br /> This level allows administrators and developers to correlate applications in the same application domain.<br /><br /> -   Traces for activity boundaries: start/stop.<br />-   Traces for transfers.|

 You can use `add` to specify the name and type of the trace listener you want to use. In the example configuration, the Listener is named `sdt` and the standard .NET Framework trace listener (`System.Diagnostics.XmlWriterTraceListener`) is added as the type. Use `initializeData` to set the name of the log file for that Listener. In addition, you can substitute a fully-qualified path for a simple file name.

Starting in .NET Framework 4.8, ComboBox controls in some high contrast themes are displayed in the correct color. You can disable this change by removing the following setting from the *svcTraceViewer.exe.config* file:

```xml
<AppContextSwitchOverrides value="Switch.UseLegacyAccessibilityFeatures=false;Switch.UseLegacyAccessibilityFeatures.2=false" />
```

## Using the Service Trace Viewer Tool

### Opening and Viewing WCF Trace Files

The Service Trace Viewer supports three file types:

- WCF Tracing File (.svcLog)

- Event Tracing File (.etl)

- Crimson Tracing File

 Service Trace Viewer enables you to open any supported trace file, add and integrate additional trace files, or open and merge a group of trace files simultaneously.

##### To open a trace file

1. Start Service Trace Viewer by using a command window to navigate to your WCF installation location (C:\Program Files\Microsoft SDKs\Windows\v6.0\Bin), and then type `SvcTraceViewer.exe`.

> [!NOTE]
> The Service Trace Viewer tool can associate with two file types: .svclog and .stvproj. You can use two parameters in command line to register and unregister the file extensions.
>
> /register: register the association of file extensions ".svclog" and ".stvproj" with SvcTraceViewer.exe
>
> /unregister: unregister the association of file extensions ".svclog" and ".stvproj" with SvcTraceViewer.exe

1. When Service Trace Viewer starts, click **File** and then point to **Open**. Navigate to the location where your trace files are stored.

2. Double-click the trace file that you want to open.

    > [!NOTE]
    > Press SHIFT while clicking multiple trace files to select and open them simultaneously. Service Trace Viewer merges the content of all files and presents one view. For example, you can open trace files of both client and service. This is useful when you have enabled message logging and activity propagation in configuration. In this way, you can examine message exchange between client and service. You can also drag multiple files into the viewer, or use the **Project** tab. See the Managing Project section for more details.

3. To add additional trace files to the collection that is open, click **File** and then point to **Add**. In the window that opens, navigate to the location of the trace files and double-click the file you want to add.

> [!CAUTION]
> It is not recommended that you load a trace log file bigger than 200MB. If you attempt to load a file larger than this limit, the loading process may take a long time, depending on your computer resource. The Service Trace Viewer tool may not be responsive for a long time, or it may exhaust your machine's memory. It is recommended that you configure partial loading to avoid this. For more information on how to do this, see "Loading Large Trace Files" section.

#### Event Tracing and Crimson Tracing

The viewer's native format is the activity tracing format that WCF emits. Traces emitted in a different format must be converted before the viewer displays them. Currently, in addition to the activity tracing format, the viewer supports event tracing and crimson tracing.

When you open a file that does not contain activity traces, the viewer attempts to convert the file. You must specify the name and location of the file that will contain the converted trace data. Once the data has been converted, the viewer displays the content of the new file.

> [!NOTE]
> Conversion requires disk space to store the converted trace data. Make sure you have enough disk space available to store the data before you start a conversion. Otherwise, the conversion fails.

### Managing Projects

The viewer supports projects to facilitate viewing multiple trace files. For example, if you have a client trace file and a service trace file, you can add them to a project. Then, every time you open the project, all the trace files in the project are loaded simultaneously.

There are two ways to manage projects:

- In the **File** menu, you can open, save and close projects.

- In the **Project** tab, you can add files to a project.

### Viewing WCF Traces

WCF emits traces using the activity tracing format. In the activity tracing model, individual traces are grouped in activities according to their purpose. Logical control flow is transferred between activities. For example, during the lifetime of an application, many "message send activities" appear and disappear. For more information on viewing traces and activities, and the user interface of the Service Trace Viewer too, see [Using Service Trace Viewer for Viewing Correlated Traces and Troubleshooting](./diagnostics/tracing/using-service-trace-viewer-for-viewing-correlated-traces-and-troubleshooting.md).

#### Switching to Different Views

The Service Trace Viewer provides the following different views. They are displayed as tabs on the left pane of the Viewer, and can also be accessed from the **View** menu.

- Activity View

- Project View

- Message View

- Graph View

##### Activity view

Once the trace files are opened, you can see the traces grouped into activities and displayed in the **Activity** view in the left-hand pane.

The **Activity** view displays activity names, number of traces in the activity, duration time, start time and end time.

By clicking any of the listed activities, the traces in this activity are displayed in the trace pane on the right. You can then select a trace to view its details.

You can select multiple activities by pressing the <kbd>Ctrl</kbd> or <kbd>Shift</kbd> key and clicking the desired activities. The trace pane displays all the traces of the selected activities.

You can double-click an activity to display it in **Graph** View. The alternative way is to select an activity and switch to **Graph** View.

> [!NOTE]
> The activity "000000000000" is a special activity that cannot be displayed in the Graph View. Because all other activities are linked to it, displaying this activity has a severe performance impact.

You can click the column title to sort the activity list. Activities that contain warning traces have a yellow background and those that contain error traces have a red one.

There are different types of activities and each type corresponds to an icon on the left side of each activity. You can refer to the Understanding Trace Icons section for their meaning.

##### Project View

This view enables you to manage trace files in the current project. See the Managing Project section for more details.

##### Message View

This view enables you to view all message log traces, including Action, Date/Time, Process, Activity and From/To, and navigate to the details of the associated message log trace. You can group the message log traces by Activity Boundary, Process/Thread, or Send & Receive for easier navigation of the message flow.

##### Graph View

This view displays the trace data for a given activity in chart form. The chart form enables you to see the stepwise execution of events and the interrelationships between multiple activities as data moves between them.

To switch to **Graph** view, select an activity in the **Activity** view and click the **Activity** tab, or a message log trace in the **Message** View. If multiple trace files are loaded and the activity involves traces from more than one file, all of the relevant traces appear in the graph view. Double-clicking on the activities and message log traces also leads you to the **Graph** view.

In **Graph** view, each vertical column represents an activity, and each block in the column represents a trace. The activities are grouped by process (or thread). The small arrows between activities represent transfers. The big arrows between processes represent message exchange. The activity in selection is always in yellow.

###### Selecting Traces in the Graph

1. Click a block in the graph.

2. Use the up and down keys to select its neighboring traces.

3. Observe the trace information in the Trace Pane and Detail Pane.

###### Expanding or Collapsing Activity Transfers

You can expand activity transfers when the activity in selection transfers out to another activity. It enables you to follow the transfers.

To expand or collapse activity transfers,

1. Locate the transfer trace with a "+" sign on the left of the transfer icon.

2. Click the "+", or press <kbd>Ctrl</kbd> and "+" using the keyboard.

3. The next activity appears in the graph.

4. A "-" appears on the left of the transfer icon. Click the "-" sign or press Ctrl and "-", the activity transfer collapses.

> [!NOTE]
> When an activity has multiple transfers into it and you expand one of the transfers, activities that lead up to the new activity from the root activity are displayed. These new activities appear in collapsed form. If you want to see the details of these activities, expand them vertically by clicking the expand icon in the header of the graph.

###### Expanding or Collapsing Activities Vertically

The viewer hides unnecessary detail in the activity graph by collapsing activities. In a collapsed activity, individual traces are not displayed. Only transfers trace appear. If you want to view all traces in an activity, expand the activity vertically by clicking the expand symbol of the activity in the header of the graph.

To expand or collapse activities vertically,

1. Click the "+" icon in the activity header to expand the activity vertically.

2. Notice that all traces are displayed in the graph.

3. Click the "-" icon in the activity header to collapse the activity vertically.

4. Notice that only important transfers, message logs, warning and exception traces are shown in the activity.

###### Options

You can select two options from the **Option** menu in Graph view.

- Show Activity Boundary Traces, which when unchecked ignore the activity boundary traces in the graph.

- Show Non-message Verbose Traces, which when unchecked ignore verbose level traces, except for message traces. In most cases, verbose level traces are less important for analysis. This option is helpful when you do not want to analyze verbose level traces and only want to focus on more important traces.

###### Layout Mode

The viewer has two Layout Modes: **Process** and **Thread**. This setting defines the largest unit of organization. The default Layout Mode is **Process**, which means that activities are grouped by processes in the graph.

###### Execution List

You can select which process or thread to be displayed in the graph from this drop-down list. For example, if you have the trace files of two clients (A and B) and one service opened, and you only want to display the service and client A in the graph, you can deselect client B from the list.

#### Viewing Trace Details

To view a trace detail, select a trace in the Trace pane. The details are displayed in the Detail pane.

##### Trace Pane

The upper right pane in the Service Trace Viewer is the Trace Pane. It lists all the traces in the selected activity with extra information, for example, trace level, thread ID, and process name.

You can copy the raw XML of the trace to the clipboard by right-clicking a trace and selecting **Copy Trace to Clipboard**.

##### Detail Pane

The bottom left pane in the Service Trace Viewer is the Detail Pane. It provides three tabs to view trace details.

The **Formatted** view displays the information in a more organized way. It lists all known XML elements in tables and trees, making it easier to read and understand the information.

The **XML** view displays XML corresponding to the selected trace. It supports highlighting and syntax color. When you use **Find** to search strings, it highlights the search results.

The **Message** view displays the message part of the XML in message log traces. It is invisible when you select a non-message trace.

### Filtering WCF Traces

To make the analysis of trace easier, you can filter them in the following ways:

- The filter toolbar provides access to pre-defined and custom filters. It can be enabled through the **View** menu.

- The pre-defined filter of the viewer can be used to selectively filter parts of the WCF traces. By default, it is set to allow all infrastructure traces to pass through. The settings of this filter are defined in the **Filter Options** sub-menu under **View** menu.

- Custom XPath filters give users full control over filtering. They can be defined in the **Custom Filter** under **View** menu.

Only traces that passes through all filters is displayed.

#### Using the Filter Toolbar

The filter toolbar appears across the top of the tool. If it is not present, you can activate it in the **View** menu. The bar has three components:

- Look for: **Look for** defines the subject to look for in the filter operation. For example, if you want to find all traces that were emitted in the context of process X, set this field to X and the **Search In** field to 'Process Name'. This field changes to a DateTime selector control when a time-based filter is selected.

- Search in: This field defines the type of filter to apply.

- Level: The level setting defines the minimum trace level allowed by the filter. For example, if the level is set to Error and Up, only traces at the Error and critical level are displayed. This filter combines with the criteria specified by Look For and Search In.

The **Filter Now** button starts the filter operation. Some filters, especially when they are applied to a large data set, take a long time to complete. You can cancel the filter operation by pressing the **Stop** button that appears in the status bar under the **Operations** menu.

The **Clear** button resets pre-defined and custom filters to allow all traces to pass through.

#### Filter Options

The viewer can automatically remove WCF traces from the view. It can selectively remove traces emitted by specific areas of WCF, for example, removing transaction related traces from the view.

The settings of this filter are defined in the **Filter Options** sub-menu under **View** menu.

#### Custom Filters

If you are familiar with the XML Path Language (XPath), you can use it to construct custom filters to search the trace data for any XML element of interest. The filters are accessible through the filter toolbar.

Custom filters can include parameters. You can also import pre-existing custom filters.

##### Creating a custom filter

Filters can be created in two ways:

###### Creating a Custom Filter using the Template Wizard

You can click an existing trace and create a filter based on the structure of the trace. This example creates a custom filter based on thread ID.

1. In the trace pane in the top right area of the viewer, select a trace that includes the element you want to filter for.

2. Click the **Create Custom Filter** button located at the top of the trace pane.

3. In the dialog box that appears, enter a name for your filter. In this example, enter `Thread ID`. You can also provide a description of your filter.

4. The tree view on the left displays the structure of the trace record you selected in step 1. Browse to the element you want to create a condition for. In this example, browse to the ThreadID to be located in the XPath: /E2ETraceEvent/System/Execution/@ThreadID node. Double-click the ThreadID attribute in the tree view. This creates an expression for the attribute on the right of the dialog.

5. Change the parameter field for the ThreadID condition from None to '{0}'. This step enables the ThreadID value to be configured when the filter is applied. (See the How to Apply a Filter section) You can define up to four parameters. Conditions are combined using the OR operator.

6. Click **Ok** to create the filter.

> [!NOTE]
> Once a filter has been created using the template wizard, it can only be edited manually. It is not possible to activate the wizard for a filter that has been created previously. In addition, the conditions of an XPath filter created in the template wizard are combined using the OR operator. If you require an AND operation, you can edit the filter expression after it has been created.

###### Creating a Custom Filter Manually

The Custom Filters menu allows you to enter XPath filters manually.

1. In the View menu, click the **Custom Filters** menu item.

2. In the dialog that appears, click **New.**

3. At the minimum, specify a Filter Name and XPath expression.

4. Click **OK**.

###### Applying a Custom Filter

Once a custom filter has been created, it is accessible though the filter toolbar. Select the filter you want to apply in the **Search In** field of the filter toolbar. For the previous example, select 'Thread ID'.

1. Specify the value you are looking for in the **Find What** field. In our example, enter the ID of the thread you want to search for.

2. Click **Filter Now**, and observe the result of the operation.

If your filter uses multiple parameters, enter them using ';' as a separator in the **Find What** field. For example, the following string defines 3 parameters: '1;findValue;text'. The viewer applies '1' to the {0} parameter of the filter. 'findValue' and 'text' are applied to {1} and {2} respectively.

###### Sharing custom Filters

Custom filters can be shared between different sessions and different users. You can export the filters to a definition file and import this file at another location.

 To import a custom filter:

1. In the **View** menu, click **Custom Filters**.

2. In the dialog box that opens, click the **Import** button.

3. Navigate to the custom filter file (.stvcf), click the file, and click the **Open** button.

To export a custom filter:

1. In the View menu, click **Custom Filters**.

2. In the dialog box that opens, select the filter you want to export.

3. Click the **Export** button.

4. Specify the name and location of the custom filter definition file (.stvcf), and click the **Save** button.

> [!NOTE]
> These custom filters can only be imported and exported from Service Trace Viewer. They cannot be read by other tools.

### Finding Data

The viewer provides the following ways to find data:

- The Find toolbar provides a quick access to the most common find options.

- The Find dialog provides more find options. It is accessible through the **Edit** menu, or by the short key Ctrl + F.

The find toolbar appears at the top of the viewer. If it is not present, you can activate it in the **View** menu. The bar has two components:

- Find What: Allows you to enter search keyword.

- Look In: Allows you to enter the search scope. You can select whether to search in all activities or in the current activity only.

The find dialog provides two additional options:

- Find target:

  - The "Raw log data" option searches the keyword in all raw data.

  - The "XML Text" and "XML Attribute" options only search in XML elements.

  - The "Logged Message" option searches the keyword only in messages.

- Ignore root activity: The search ignores the traces in the "000000000000" activity. This improves performance in large trace files when the root activity has thousands of traces, most of which are transfers.

### Navigating Traces

Because traces are recorded step by step during application runtime, navigating traces can help you to debug your application. The Service Trace Viewer provides various ways to navigate in traces.

#### Step Forward or Backward

If you consider each trace as a line of code in the program, stepping forward is very similar to "Step over" in the Visual Studio Integrated Development Environment (IDE). The difference is that you can also step backward in the traces. Stepping forward means moving to the next trace in the activity.

- Step Forward: Use the **Activity** menu, or press "F10". You can also use arrow key "down" in the trace pane.

- Step Backward: Use the **Activity** menu, or press "F9". You can also use arrow key "up" in the trace pane.

> [!NOTE]
> This can take you to an activity occurring in a different process or even on a different computer, because WCF messages can carry activity IDs that span machines.

#### Follow Transfer

Transfer traces are special traces in the trace file. An activity may transfer to another activity by a transfer trace. For example, "Activity A" may transfer to "Activity B". In such case, there is a transfer trace in the "Activity A" with the name "To: Activity" and the transfer icon. This transfer trace is a link between the two traces. In "Activity B", there might also be a transfer trace at the end of the activity to transfer back to "Activity A". This is similar to function calls in programs: A calls B, then B returns.

"Follow transfer" is similar to "Step into" in a debugger. It follows the transfer from A to B. It does not have any effect on other traces.

There are two ways to follow a transfer: by mouse or by keyboard:

- By Mouse: Double-click the transfer trace in the trace pane.

- By Keyboard: Select a transfer trace, and use "Follow Transfer" in the **Activity** menu, or press "F11"

> [!NOTE]
> In many cases, when Activity A transfers to Activity B, Activity A waits until Activity B transfers back to Activity A. This means that Activity A has no trace logged during the period when Activity B is actively tracing. However, it is also possible that Activity A does not wait, and continues to log traces. It is also possible that Activity B does not transfer back to Activity A. Therefore, activity transfers are still different from function calls in this sense. You can understand activity transfers better in Graph view.

#### Jump to Next or Previous Transfer

When you are analyzing the current activity, or selected activities when multiple activities are selected, you may want to quickly find the activities it transfers to. "Jump to next transfer" allows you to locate the next transfer trace in the activity. Once you find the transfer trace, you can use "Follow transfer" to step into the next activity.

- Jump to Next Transfer: Use the **Activity** menu, or press "Ctrl + F10".

- Jump to Previous Transfer: Use the **Activity** menu, or press "Ctrl + F9".

#### Navigate in Graph View

Although navigating in the activity pane and trace pane is similar to debugging, using **Graph** view provides a much better experience in navigation. See "Graph View" section for more information.

### Loading Large Trace Files

Trace files can be very large. For example, if you turn on tracing on the "Verbose" level, the resulting trace file for running a few minutes can easily be hundreds of megabytes or even larger, depending on network speed and communication pattern.

When you open a very large trace file in the Service Trace Viewer, system performance can be negatively impacted. The loading speed and the response time after loading can be slow. Actual speed differs from time to time, depending on your hardware configuration. In most PCs, loading a trace file larger than 200M has a severe performance impact. For traces files larger than 1G, the tool may use up all available memory, or stop responding for a very long time.

In order to avoid the slow loading and response time in analyzing large trace files, the Service Trace Viewer provides a feature called "Partial Loading", which only loads a small part of the trace at a time. For example, you may have a trace file over 1GB, running for several days on the server. When some errors have occurred and you want to analyze the trace, it is not necessary to open the entire trace file. Instead, you can load the traces within a certain period of time when the error might have occurred. Because the scope is smaller, the Service Trace Viewer tool can load the file faster and you can identify the errors using a smaller set of data.

#### Enabling Partial Loading

You do not need to manually enable partial loading. If the total size of the trace file(s) you attempt to load exceeds 40MB, Service Trace Viewer automatically displays a Partial Loading dialog for you to select the part that you want to load.

> [!NOTE]
> Because traces may not be distributed evenly in the time span, the length of the time period you specify in the Partial Loading toolbar may not be proportional to the loading size shown. The actual loading size can be smaller than the Estimated Size in the partial loading dialog.

#### Adjusting Partial Loading

After you have partially loaded the trace file, you may want to change the data set being loaded. You can do so by adjusting the Partial Loading toolbar at the top of the viewer.

1. Move the toolbar by mouse, or input the Begin and End time.

2. Click the **Adjust** button.

## Understanding Trace Icons

The following is a list of icons that the Service Trace Viewer tool uses in the **Activity** view, **Graph** view and **Trace** pane to represent different items.

> [!NOTE]
> Some traces that are not categorized (for example, "a message is closed") have no icon.

### Activity Tracing Traces

|Icon|Description|
|----------|-----------------|
|![Warning trace](./media/7457c4ed-8383-4ac7-bada-bcb27409da58.gif "7457c4ed-8383-4ac7-bada-bcb27409da58")|Warning trace: A trace that is emitted at the warning level|
|![Error trace](./media/7d908807-4967-4f6d-9226-d52125db69ca.gif "7d908807-4967-4f6d-9226-d52125db69ca")|Error trace: A trace that is emitted at the error level.|
|![Activity Start trace:](./media/8a728f91-5f80-4a95-afe8-0b6acd6e0317.gif "8a728f91-5f80-4a95-afe8-0b6acd6e0317")|Activity Start trace: A trace that marks the beginning of an activity. It contains the name of the activity. As the application designer or developer, you should define one activity Start trace per activity id per process or thread.<br /><br /> If the activity id is propagated across trace sources for trace correlation, you can then see multiple Starts for the same activity id (one per trace source). The Start trace is emitted if ActivityTracing is enabled for the trace source.|
|![Activity Stop trace](./media/a0493e95-653e-4af8-84a4-4d09a400bc31.gif "a0493e95-653e-4af8-84a4-4d09a400bc31")|Activity Stop trace: A trace that marks the end of an activity. . It contains the name of the activity. As the application designer or developer, you should define one activity Stop trace per activity id per trace source. No traces from a given trace source appear after the activity Stop emitted by that trace source, except if the trace time granularity is not sufficiently small. When that happens, two traces with the same time, including a Stop, may be interleaved when displayed. If the activity id is propagated across trace sources for trace correlation, you can see multiple Stops for the same activity id (one per trace source). The Stop trace is emitted if ActivityTracing is enabled for the trace source.|
|![Activity Suspend trace](./media/6f7f4191-df2b-4592-8998-8379769e2d32.gif "6f7f4191-df2b-4592-8998-8379769e2d32")|Activity Suspend trace: A trace that marks the time an activity is paused. No traces are emitted in a suspended activity until the activity resumes. A suspended activity denotes that no processing is happening in that activity in the scope of the trace source. Suspend/Resume traces are useful for profiling. The Suspend trace is emitted if ActivityTracing is enabled for the trace source.|
|![Activity resume trace](./media/1060d9d2-c9c8-4e0a-9988-cdc2f7030f17.gif "1060d9d2-c9c8-4e0a-9988-cdc2f7030f17")|Activity resume trace: A trace that marks the time an activity is resumed after it had been suspended. Traces may be emitted again in that activity. Suspend/Resume traces are useful for profiling. The Resume trace is emitted if ActivityTracing is enabled for the trace source.|
|![Transfer](./media/b2d9850e-f362-4ae5-bb8d-9f6f3ca036a5.gif "b2d9850e-f362-4ae5-bb8d-9f6f3ca036a5")|Transfer: A trace that is emitted when logical control flow is transferred from one activity to another. The activity the transfer originates from may continue to perform work in parallel to the activity the transfer goes to. The Transfer trace is emitted if ActivityTracing is enabled for the trace source.|
|![Transfer From](./media/1df215cb-b344-4f36-a20d-195999bda741.gif "1df215cb-b344-4f36-a20d-195999bda741")|Transfer From: A trace that defines a transfer from another activity to the current activity.|
|![Transfer To](./media/74255b6e-7c47-46ef-8e53-870c76b04c3f.gif "74255b6e-7c47-46ef-8e53-870c76b04c3f")|Transfer To: A trace that defines a transfer of logical control flow from the current activity to another activity.|

### WCF Traces

|Icon|Description|
|----------|-----------------|
|![Message Log trace](./media/7c66e994-2476-4260-a0db-98948b9af197.gif "7c66e994-2476-4260-a0db-98948b9af197")|Message Log trace: A trace that is emitted when a WCF message is logged by the message logging feature, when the `System.ServiceModel.MessageLogging` trace source is enabled. Clicking on this trace displays the message. There are four configurable logging points for a message: ServiceLevelSendRequest, TransportSend, TransportReceive, and ServiceLevelReceiveRequest, which can also be  specified by the `messageSource` attribute in the message log trace.|
|![Message Received trace](./media/de4f586c-c5dd-41ec-b1c3-ac56b4dfa35c.gif "de4f586c-c5dd-41ec-b1c3-ac56b4dfa35c")|Message Received trace: A trace that is emitted when a WCF message is received, if the `System.ServiceModel` trace source is enabled at the Information or Verbose level. This trace is essential for viewing the message correlation arrow in the Activity **Graph** view.|
|![Message Sent trace](./media/558943c4-17cf-4c12-9405-677e995ac387.gif "558943c4-17cf-4c12-9405-677e995ac387")|Message Sent trace: A trace that is emitted when a WCF message is sent if the `System.ServiceModel` trace source is enabled at the Information or Verbose level. This trace is essential for viewing the message correlation arrow in the Activity **Graph** view.|

### Activities

|Icon|Description|
|----------|-----------------|
|![Activity](./media/wcfc-defaultactivityc.gif "wcfc_defaultActivityc")|Activity: Indicates that the current activity is a generic activity.|
|![Root activity](./media/5dc8e0eb-1c32-4076-8c66-594935beaee9.gif "5dc8e0eb-1c32-4076-8c66-594935beaee9")|Root activity: Indicates the root activity of a process.|

### WCF Activities

|Icon|Description|
|----------|-----------------|
|![Environment activity](./media/29fa00ac-cf78-46e5-822d-56222fff61d1.gif "29fa00ac-cf78-46e5-822d-56222fff61d1")|Environment activity: An activity that creates, opens, or closes a WCF host or client. Errors that have happened during these phases will appear in this activity.|
|![Listen activity](./media/d7b135f6-ec7d-45d7-9913-037ab30e4c26.gif "d7b135f6-ec7d-45d7-9913-037ab30e4c26")|Listen activity: An activity that logs traces related to a listener. Inside this activity, we can view listener information and connection requests.|
|![Receive Bytes activity](./media/2f628580-b80f-45a7-925b-616c96426c0e.gif "2f628580-b80f-45a7-925b-616c96426c0e")|Receive Bytes activity: An activity that groups all traces related to receiving incoming bytes on a connection between two endpoints. This activity is essential in correlating with transport activities that propagate their activity id such as http.sys. Connection errors such as aborts will appear in this activity.|
|![Process Message activity](./media/wcfc-executionactivityiconc.GIF "wcfc_ExecutionActivityIconc")|Process Message activity: An activity that groups traces related to creating a WCF message. Errors due to a bad envelope or a malformed message will appear in that activity. Inside this activity, we can inspect message headers to see if an activity id was propagated from the caller. If this is true, when we transfer to Process Action activity (the next icon), we can also assign to that activity the propagated activity id for correlation between the caller and callee's traces.|
|![Message Log trace](./media/7c66e994-2476-4260-a0db-98948b9af197.gif "7c66e994-2476-4260-a0db-98948b9af197")|Process Action activity: An activity that groups all traces related to a WCF request across two endpoints. If `propagateActivity` is set to `true` on both endpoints in configuration, all traces from both endpoints are merged into one activity for direct correlation. Such activity will contain errors due to transport or security processing, extending to the user code boundary and back (if a response exists).|
|![Process Message activity](./media/wcfc-executionactivityiconc.GIF "wcfc_ExecutionActivityIconc")|Execute User Code activity: An activity that groups user code traces for processing a request.|

## Troubleshooting

If you do not have permission to write to the registry, you get the following error message "The Microsoft Service Trace Viewer was not registered to the system" when you use the "`svctraceviewer /register`" command to register the tool. If this occurs, you should log in using an account that has write access to the registry.

In addition, the Service Trace Viewer tool writes some settings (for example, custom filters and filter options) to the SvcTraceViewer.exe.settings file in its assembly folder. If you do not have read permission for the file, you can still launch the tool, but you cannot load the settings.

If you get the error message "An unknown error occurred while processing one or more traces" when opening the .etl file, it means that the format of the .etl file is invalid.

If you open a trace log created using an Arabic operating system, you may notice that the time filter does not work. For example, year 2005 corresponds to year 1427 in Arabic calendar. However, the time range supported by the Service Trace Viewer tool filter does not support a date earlier than 1752. This can imply that you are not able to select a correct date in the filter. To resolve this problem, you can create a custom filter (**View/Custom Filters**) using an XPath expression to include a specific time range.

## See also

- [Using Service Trace Viewer for Viewing Correlated Traces and Troubleshooting](./diagnostics/tracing/using-service-trace-viewer-for-viewing-correlated-traces-and-troubleshooting.md)
- [Configuring Tracing](./diagnostics/tracing/configuring-tracing.md)
- [End-to-End Tracing](./diagnostics/tracing/end-to-end-tracing.md)
