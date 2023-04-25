---

copyright:
  years: 2023
lastupdated: "2023-04-20"

keywords: tool integrations, IBM Cloud Public, Event Notifications

subcollection: ContinuousDelivery

---

{{site.data.keyword.attribute-definition-list}}

# Enabling event notifications for toolchains
{: #event-notifications-cd}

As an administrator of {{site.data.keyword.contdelivery_full}} toolchains, you might want to send notifications of events in a toolchain or a tool integration to other users, or human destinations, by using email, SMS, Slack, PagerDuty, or other supported delivery channels. Also, you might want to send these notifications of events to other applications to build logic by using event-driven programming using webhooks, for example. This approach is made possible by the integration between toolchains and {{site.data.keyword.en_full}}.
{: shortdesc}

To send information to {{site.data.keyword.en_short}}, you must [add an {{site.data.keyword.en_short}} tool integration](/docs/ContinuousDelivery?topic=ContinuousDelivery-event-notifications-integration) to your toolchain. For more information about working with {{site.data.keyword.en_short}}, see [Getting started with {{site.data.keyword.en_short}}](/docs/event-notifications?topic=event-notifications-getting-started).

You can now distribute event notifications by using the [{{site.data.keyword.en_short}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-event-notifications-integration) tool integration. {{site.data.keyword.en_short}} is the preferred method for distributing notifications to Slack and other communication channels such as PagerDuty, email, SMS, push notifications, webhook, Microsoft&reg; Teams, ServiceNow, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.openwhisk_short}}.
{: tip}

## How events are collected and sent by toolchains
{: #event-notifications-how-cd}

When an event of interest takes place in a toolchain or one of the supported tool integrations, the toolchain communicates with a connected {{site.data.keyword.en_short}} instance to forward a notification to a [supported destination](/docs/event-notifications?topic=event-notifications-en-destination).

## Events for {{site.data.keyword.contdelivery_short}}
{: #event-notifications-list-cd}

The following table lists the toolchain events.

The `:1` characters that are appended to each subtype represent major version numbers.
{: tip}

| Event Name | Event Type | Subtype | Description |
|:-----------|:----------------|:-----------------------|:--------------|
| `Tool created` | `com.ibm.cloud.toolchain.toolchain` | `toolchain_bind:1`         | This event is sent when a tool integration is created and added to a toolchain. |
| `Tool deleted` | `com.ibm.cloud.toolchain.toolchain` | `toolchain_unbind:1` | This event is sent when a tool integration is deleted and removed from a toolchain. |
| `Pipeline run started` | `com.ibm.cloud.toolchain.pipeline` | `pipeline_start:1` | This event is sent when either a Tekton pipeline run or a Classic pipeline stage starts. |
| `Pipeline run succeeded` | `com.ibm.cloud.toolchain.pipeline` | `pipeline_success:1`  | This event is sent when either a Tekton pipeline run or a Classic pipeline stage completes successfully. |
| `Pipeline run failed` | `com.ibm.cloud.toolchain.pipeline` | `pipeline_fail:1` | This event is sent when either a Tekton pipeline run or a Classic pipeline stage completes with an error or a failure. | 
| `Pipeline run cancelled` | `com.ibm.cloud.toolchain.pipeline` | `pipeline_cancel:1` | This event is sent when either a Tekton pipeline run or a Classic pipeline stage is cancelled. | 
{: caption="Table 1. Actions that generate event notifications" caption-side="bottom"}

## Enabling notifications
{: #event-notifications-enable-cd}

Events that are generated by a toolchain or an associated tool integration can be forwarded to an {{site.data.keyword.en_short}} service instance that is available in the same account.

Make sure that the selected {{site.data.keyword.en_short}} service instance has an [IAM authorization policy](/iam/authorizations/grant) that allows the toolchain to send events to this service instance. For more information about granting authorization with the {{site.data.keyword.en_short}} service instance, see [Why am I denied permission to integrate an Event Notifications instance?](/docs/event-notifications?topic=event-notifications-troubleshoot-integrate).
{: important}

### Connecting to {{site.data.keyword.en_short}} in the console
{: #event-notifications-enable-uicd}
{: ui}

Configure {{site.data.keyword.en_short}} to send critical events from toolchains and tool integration instances:

1. If you have a toolchain and are adding this tool integration to it, from the {{site.data.keyword.cloud_notm}} console, click the menu icon ![hamburger icon](images/icon_hamburger.svg) and select **DevOps**. On the Toolchains page, click the toolchain to open its Overview page. Alternatively, on your app's Overview page, on the Continuous delivery card, click **View toolchain**. Then, click **Overview**.  

   a. Click **Add tool**.

   b. In the Tool Integrations section, click **{{site.data.keyword.en_short}}**.

1. Type the name that you want to display for this tool integration on the {{site.data.keyword.en_short}} card in your toolchain. This name is used to identify the tool integration in your toolchain.
1. Select the **{{site.data.keyword.en_short}}** instance to connect the toolchain to.
1. Click **Create Integration** to add the {{site.data.keyword.en_short}} tool integration to your toolchain.
1. On your Toolchain's Overview page, on the **IBM Cloud tools** card, click **{{site.data.keyword.en_short}}**.

### Connecting to {{site.data.keyword.en_short}} with the API
{: #event-notifications-enable-apicd}
{: api}

You can add the {{site.data.keyword.en_short}} tool integration to your toolchain by using the API.

1. [Obtain an IAM bearer token](https://{DomainName}/apidocs/toolchain#authentication){: external}. Alternatively, if you are using an SDK, [obtain an IAM API key](https://{DomainName}/iam/apikeys){: external} and set the client options by using environment variables.
   
   ```bash
   export CD_TOOLCHAIN_AUTH_TYPE=iam && \
   export CD_TOOLCHAIN_APIKEY={iam_api_key} && \
   export CD_TOOLCHAIN_URL={base_url}
   ```
   {: pre}

1. [Look up the ID of the toolchain](https://{DomainName}/apidocs/toolchain#list-toolchains){: external} in which you want to create your tool integration.
1. Specify `eventnotifications` as the `tool_type_id`.
1. Specify the following `tool_parameters` that are required by the tool integration:

   *  `name`: The name that is used to identify the {{site.data.keyword.en_short}} tool integration.
   *  `instance-crn`: The Cloud Resource Name (CRN) of the {{site.data.keyword.en_short}} service instance.

1. Add the tool integration within the targeted toolchain.
   
   ```curl
   curl -X POST --location --header "Authorization: Bearer {iam_token}" \
     --header "Accept: application/json" \
     --header "Content-Type: application/json" \
     --data '{ "name": "{tool_name}", "tool_type_id": "eventnotifications", "parameters": {tool_parameters} }' \
     "{base_url}/toolchains/{toolchain_id}/tools"
   ```
   {: pre}
   {: curl}

   ```javascript
   const CdToolchainV2 = require('@ibm-cloud/continuous-delivery/cd-toolchain/v2');
   const toolchainService = CdToolchainV2.newInstance();
   ...
   (async() => {
      const toolPrototypeModel = {
         toolchainId: {toolchain_id},
         toolTypeId: "eventnotifications",
         name: {tool_name},
         parameters: {tool_parameters}
      };
      const response = await toolchainService.createTool(toolPrototypeModel);
   })();

   ```
   {: codeblock}
   {: node}

   ```go
   import (
	   "github.com/IBM/continuous-delivery-go-sdk/cdtoolchainv2"
   )
   ...
   toolchainClientOptions := &cdtoolchainv2.CdToolchainV2Options{}
   toolchainClient, err := cdtoolchainv2.NewCdToolchainV2UsingExternalConfig(toolchainClientOptions)
   createToolOptions := toolchainClient.NewCreateToolOptions({toolchain_id}, "eventnotifications")
   createToolOptions.SetName({tool_name})
   createToolOptions.SetParameters({tool_parameters})
   tool, response, err := toolchainClient.CreateTool(createToolOptions)
   ```
   {: codeblock}
   {: go}

   ```python
   from ibm_continuous_delivery.cd_toolchain_v2 import CdToolchainV2
   ...
   toolchain_service = CdToolchainV2.new_instance()
   tool = toolchain_service.create_tool(
      name = {tool_name},
      toolchain_id = {toolchain_id},
      tool_type_id = "eventnotifications",
      parameters = {tool_parameters}
   )
   ```
   {: codeblock}
   {: python}

   ```java
   import com.ibm.cloud.continuous_delivery.cd_toolchain.v2.CdToolchain;
   import com.ibm.cloud.continuous_delivery.cd_toolchain.v2.model.*;
   ...
   CdToolchain toolchainService = CdToolchain.newInstance();
   CreateToolOptions createToolOptions = new CreateToolOptions.Builder()
      .name({tool_name})
      .parameters({tool_parameters})
      .toolchainId({toolchain_id})
      .toolTypeId("eventnotifications")
      .build();
   Response<ToolchainToolPost> response = toolchainService.createTool(createToolOptions).execute();
   ToolchainToolPost tool = response.getResult();
   ```
   {: codeblock}
   {: java}

### Adding a tool integration with Terraform
{: #event-notifications-enable-terraformcd}
{: terraform}

You can add the {{site.data.keyword.en_short}} tool integration to your toolchain by using Terraform.

1. To install the Terraform command-line interface (CLI) and configure the {{site.data.keyword.cloud_notm}} provider plug-in for Terraform, follow the tutorial for [Getting started with Terraform on {{site.data.keyword.cloud}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started).
1. Create a Terraform configuration file that is named `main.tf`. In this file, add the configuration to create resource instances by using the HashiCorp Configuration Language (HCL). For more information about using this configuration language, see the [Terraform documentation](https://www.terraform.io/docs/language/index.html){: external}.

   The following example creates a {{site.data.keyword.deliverypipeline}} tool integration by using the `ibm_cd_toolchain_tool_pipeline` resource, where `toolchain_id` is a GUID that represents the toolchain in which to create the tool integration.

   ```terraform
   data "ibm_cd_toolchain" "cd_toolchain" {
     toolchain_id = {toolchain_id}
   }

   resource "ibm_cd_toolchain_tool_eventnotifications" "en_instance" {
     toolchain_id = data.ibm_cd_toolchain.cd_toolchain.id
     parameters {
       name = "{event_notifications_tool_integration_name}"
       instance_crn = "{event_notifications_service_crn}"
     }
   }
   ```
   {: codeblock}

   For more information about tool integration resources, see the full list of supported tool integration resources in the [{{site.data.keyword.cloud_notm}} Terraform Registry](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs/resources/cd_toolchain){: external}.

1. Initialize the Terraform CLI.

   ```terraform
   terraform init
   ```
   {: pre}
   
1. Create a Terraform execution plan. This plan summarizes the actions that must run to create the tool integration.

   ```terraform
   terraform plan
   ```
   {: pre}

1. Apply the Terraform execution plan. Terraform takes the required actions to create the tool integration.

   ```terraform
   terraform apply
   ```
   {: pre}

The following table lists and describes each of the variables that are used in the previous steps.   
    
| Variable | Description |
|:---------|:------------|
| `{base_url}` | The Toolchain API endpoint URL. For more information about supported values, see [Endpoint URL](https://{DomainName}/apidocs/toolchain#endpoint-url){: external}. |
| `{iam_api_key}` | Your IAM API key. |
| `{iam_token}` | A valid IAM bearer token. |
| `{tool_name}` | The name of the tool integration. |
| `{tool_parameters}` | Unique key-value pairs that represent the parameters to use to create the tool integration. For more information about the supported parameters for each tool integration, see [Tool integrations](/docs/ContinuousDelivery?topic=ContinuousDelivery-integrations&interface=api#tool_integrations). |
| `{toolchain_id}` | The toolchain in which to create the tool integration. |
{: caption="Table 2. Variables for provisioning the tool integration with the API" caption-side="top"}

## Delivering notifications to select destinations
{: #event-notifications-destinations-cd}

After you enable event notifications for a toolchain, create [topics](/docs/event-notifications?topic=event-notifications-en-create-en-topic), [destinations](/docs/event-notifications?topic=event-notifications-en-create-en-destination), and [subscriptions](/docs/event-notifications?topic=event-notifications-en-create-en-subscription) in {{site.data.keyword.en_short}} so that alerts can be forwarded and delivered to your selected destinations.

For a complete list of supported destinations, see the [{{site.data.keyword.en_short}} documentation](/docs/event-notifications?topic=event-notifications-en-destination).
{: tip}

## Notification payload details
{: #event-notifications-payload}

Events that are generated by toolchains and their associated tool integration instances contain various fields that help you to identify the source and details of an event.

Event notifications from toolchains and tool integration instances contain only metadata properties, such as names or identifiers of resources. Sensitive data, such as API keys or passwords, is not included in generated events.
{: tip}

The properties that are sent to {{site.data.keyword.en_short}} vary depending on the event type. For example, if a `com.ibm.cloud.toolchain.pipeline:pipeline_start:1` event takes place, the toolchain sends a notification payload to {{site.data.keyword.en_short}} that is similar to the following example.

```json
{
   "subject": {
      "name": "<user>",
      "email": "<user_email>",
      "iam_id": "<iam_id>"
   },
   "toolchain.instance": {
      "crn": "crn:v1:bluemix:public:toolchain:<region>:a/<account_id>:<toolchain_id>::",
      "href": "https://api.<region>.devops.cloud.ibm.com/toolchain/v2/toolchains/<toolchain_id>",
      "id": "357d4432-964a-46ae-83d4-df91eb539d1a",
      "name": "EventNotifications-toolchain",
      "resource_group_id": "<resource_group_id>",
      "ui_href": "https://cloud.ibm.com/devops/toolchains/<toolchain_id>?env_id=ibm:yp:us-south"
   },
   "toolchain.tool-instance": {
      "href": "https://api.<region>.devops.cloud.ibm.com/toolchain/v2/toolchains/<toolchain_id>/tools/<tool_id>",
      "id": "<tool_id>",
      "name": "ci-pipeline",
      "tool_type_id": "pipeline",
      "referent": {
         "ui_href": "https://cloud.ibm.com/devops/pipelines/<tool_id>?env_id=ibm:yp:us-south"
      }
   },
   "toolchain.pipeline-run": {
      "id": "<run_id>",
      "run_number": 11,
      "start_time": "2023-04-17T16:48:36.928Z",
      "ui_href": "https://cloud.ibm.com/devops/pipelines/<tool_id>/<stage_id>/<run_id>?env_id=<region_id>"
   }
}
```
{: screen}

The following table provides detailed information about each event notification property.

| Property | Description |
| ---- | ---- |
| `subject` | Optional. The object that represents the subject that initiated the event. This object might contain the following fields:  \n  \n `name`: The name of the subject.  \n  \n `email`: The email of the subject.  \n  \n `iam_id`: The IAM ID of the subject.  \n  \n |
| `toolchain.instance` | The object that represents the toolchain where the event originated. This object contains the following fields:  \n  \n `crn`: The CRN of the toolchain.  \n  \n `id`: The ID of the toolchain.  \n  \n `resource_group_id`: The ID of the toolchain's resource group.  \n  \n `name`: The name of the toolchain.  \n  \n `href`: The public API endpoint for the toolchain.  \n  \n `ui_href`: The UI endpoint for the toolchain.  \n  \n |
| `toolchain.tool-instance` | The object that represents the toolchain instance that participates in the event. For the `toolchain_bind` and `toolchain_unbind` subtypes, this object is the tool integration instance that is being bound or unbound. For pipeline events, this object is the pipeline tool integration instance where the event originated. This object contains the following fields:  \n  \n `id`: The ID of the tool integration instance.  \n  \n `tool_type_id`: The ID of the [tool type](/docs/ContinuousDelivery?topic=ContinuousDelivery-integrations&interface=api#tool_integrations).  \n  \n `href`: The public API endpoint for the tool integration instance.  \n  \n `state`: The state of the tool integration instance.  \n  \n `referent`: An object that contains information about the tool that is represented by the tool integration instance. For example, `ui_href` which is the UI endpoint for the tool integration that is represented by the tool integration instance.  \n  \n `name`: Optional. The name of the tool integration instance.  \n  \n |
| `toolchain.pipeline-run` | The object that represents the Tekton pipeline run or Classic pipeline stage run where the event originated. This object is applicable only to pipeline events and contains the following fields:  \n  \n `id`: The ID of the Tekton pipeline run or Classic pipeline stage.  \n  \n `ui_href`: The UI endpoint of the Tekton pipeline run or Classic pipeline stage.  \n  \n `run_number`: Optional. The run number of the Tekton pipeline run or the Classic pipeline stage.  \n  \n `start_time`: Optional. The time that the run started, in ISO 8601 format.  \n  \n `finish_time`: Optional. The time that the run finished, in ISO 8601 format.  \n  \n `duration`: Optional. The duration of the run, in ISO 8601 format.  \n  \n |
{: caption="Table 3. Properties in an event notification payload" caption-side="bottom"}