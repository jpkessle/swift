---

copyright:
  years: 2018, 2019
lastupdated: "2019-12-04"

keywords: deploy swift app, deploy swift, serverless swift, deploy swift cloud foundry, swift kubernetes

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Deploying and integrating Swift apps
{: #deploy_apps-swift}

You can deploy your Swift apps with a toolchain or by using the command-line interface. A toolchain is a set of tool integrations. The command-line interface is a simple way to deploy your apps and service instances.

For more information, see [Deploying apps](/docs/apps?topic=creating-apps-deploying-apps).

## Developing serverless Swift apps
{: #serverless-swift}

To facilitate serverless development, you can use the IBM Function as a Service (FaaS) offering, [{{site.data.keyword.openwhisk}}](https://www.ibm.com/cloud/functions){: new_window} ![External link icon](../../icons/launch-glyph.svg "External link icon"). {{site.data.keyword.openwhisk_short}} enables application logic to be run in response to events or direct invocations from web or mobile apps over HTTP without provisioning or managing servers.

{{site.data.keyword.openwhisk_short}} performs system administration like auto-scaling, availability management, and maintenance, allowing developers to remain focused on writing application logic.

For more information, see:
* [Developing serverless apps](/docs/apps/deploying?topic=creating-apps-serverless)
* [IBM Cloud Functions](https://www.ibm.com/cloud/functions){: new_window} ![External link icon](../../icons/launch-glyph.svg "External link icon")
* [FaaS (Function-as-a-Service)](https://www.ibm.com/cloud/learn/faas){: new_window} ![External link icon](../../icons/launch-glyph.svg "External link icon")

## Integrating back-end services with a generated SDK
{: #backend_gensdk-swift}

The {{site.data.keyword.IBM_notm}} SDK Generator plug-in easily integrates back-end services to your app with a generated SDK. When a change to a REST API occurs, you can regenerate the SDK and replace the old one for to upgrade the SDK. You can also integrate the CLI into a DevOps pipeline and ensure that the SDK is always consistent with the API spec each time the app is built.

For more information, see [Integrating back-end services with a generated SDK](/docs/swift/backend?topic=swift-sdkgen-cli#sdkgen-cli).

## Deploying to a Kubernetes cluster
{: #deploy_kube-swift}

You can learn how to use {{site.data.keyword.cloud_notm}} Kubernetes Service to deploy a containerized app that leverages Watson Tone Analyzer. In provided scenario, a fictional PR firm uses the {{site.data.keyword.cloud_notm}} service to analyze their press releases and receive feedback on the tone of their messages. For more information, see the tutorial [Deploying apps into Kubernetes clusters](/docs/containers?topic=containers-cs_apps_tutorial).

## Deploying to Cloud Foundry
{: #swift-deploy-cf}

This option deploys your cloud-native app without you needing to manage the underlying infrastructure.

If you plan to deploy your app to [{{site.data.keyword.cfee_full}}](/docs/cloud-foundry?topic=cloud-foundry-about), you must [prepare your {{site.data.keyword.cloud_notm}} account](/docs/cloud-foundry?topic=cloud-foundry-prepare).

If your account has access to {{site.data.keyword.cfee_full_notm}}, you can select a deployer type of either [Public Cloud or Enterprise Environment](/docs/cloud-foundry?topic=cloud-foundry-what-is-cloud-foundry#ibmcf-offerings), which you can use to create and manage isolated environments for hosting Cloud Foundry applications exclusively for your enterprise.
