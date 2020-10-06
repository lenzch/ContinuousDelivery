---

copyright:
  years: 2015, 2020
lastupdated: "2020-08-19"

keywords: tool integrations, IBM Cloud Public, IBM Cloud Dedicated, Slack

subcollection: ContinuousDelivery

---

{:shortdesc: .shortdesc}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}   

# Configuring Slack
{: #slack}

Slack is a cloud-based, real-time messaging and notification system.
{:shortdesc}

Slack provides persistent chat, which is a more interactive alternative to email for team collaboration. You can communicate with your team on a dedicated channel or on a set of channels that is directly related to your work. You can also share files and images through the channels or in direct messages between two or more people. The communications in direct messages and on channels are retained so that you can search them.

 Notifications that are posted to public Slack channels are visible to everyone on the team. You are responsible for the content that you post.
{: important}

Configure Slack to receive notifications about your toolchain from the tool integrations, such as test and deployment activities:

1. If you are configuring this tool integration as you are creating the toolchain, in the Configurable Integrations section, click **Slack**.
1. If you have a toolchain and are adding this tool integration to it, from the {{site.data.keyword.cloud_notm}} console, click the menu icon ![hamburger icon](images/icon_hamburger.svg) and select **DevOps**. On the Toolchains page, click the toolchain to open its Overview page. Alternatively, on your app's Overview page, on the Continuous delivery card, click **View toolchain**. Then, click **Overview**. 

 a. Click **Add tool**.

 b. In the Tool Integrations section, click **Slack**.

1. Type the Slack webhook URL, which is generated by Slack as an incoming webhook. You need a Slack webhook URL for a Slack channel to receive notifications about your toolchain from the tool integrations. For instructions to create or find your webhook, see [Incoming Webhooks](https://api.slack.com/incoming-webhooks){:external}.

 If you use an API key for your Slack channel to receive notifications about your toolchain from the tool integrations, you must update your configuration to use a webhook instead.
 {: tip}

1. Type the name of the Slack channel that you want notifications to be sent to. The channel must exist and be active in your Slack team.
1. Type the URL host name for your Slack team, which is the word or phrase before `.slack.com` in your team URL. For example, if your team URL is `https://team.slack.com`, the host name is `team`.
1. Click **Create Integration**.

 If the Slack channel and team that you specified cannot be reached, the `Setup Failed` error is displayed on the Slack card. Hover over the `Setup Failed` message and click **Reconfigure**. Make sure that you are using valid configuration parameters for the Slack webhook URL, Slack channel, and URL host name for your Slack team. Update the settings as required, and click **Save Integration**.
 {: tip}

1. Click **Slack**. You can view all of the activity for your toolchain in the configured Slack channel.

## Learn more about Slack

To learn more about Slack, see the [Slack article](https://www.ibm.com/cloud/garage/content/culture/tool_slack/){:external} on the IBM Cloud Garage Method or take this tutorial and the Garage Method advocate course:

  * [Use the "Develop and test microservices on Cloud Foundry" toolchain](https://www.ibm.com/cloud/garage/tutorials/use-develop-test-microservices-on-cloud-foundry-toolchain){:external}

  * [Become a Garage Method advocate](https://www.ibm.com/cloud/garage/content/course/gm_advocate/){:external}