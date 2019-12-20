---

copyright:
  years: 2018, 2019
lastupdated: "2019-12-19"

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
{: #deploy-kube}

[{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-getting-started) is an open source platform for managing containerized workloads and services across multiple hosts, and offers management tools for deploying, automating, monitoring, and scaling containerized apps with minimal to no manual intervention. [Learn more](https://www.ibm.com/cloud/learn/kubernetes). When you deploy your app to a Kubernetes cluster, you can select either Helm or [Knative](/docs/containers?topic=containers-serverless-apps-knative) as the deployment type.

## Deploying to an OpenShift cluster
{: #deploy-openshift}

[Red Hat OpenShift on {{site.data.keyword.cloud_notm}}](/docs/openshift?topic=openshift-getting-started) is a Kubernetes container platform that provides a trusted environment to run enterprise workloads. It extends the Kubernetes platform with built-in software to enhance app lifecycle development, operations, and security. With OpenShift, you can consistently deploy your workloads across hybrid cloud providers and environments. [View a comparison between Kubernetes and OpenShift clusters](https://cloud.ibm.com/docs/openshift?topic=openshift-cs_ov#openshift_kubernetes).

## Deploying to Cloud Foundry
{: #deploy-cf}

[{{site.data.keyword.cloud_notm}} Foundry](/docs/cloud-foundry-public?topic=cloud-foundry-public-getting-started) is the premier industry standard Platform-as-a-Service (PaaS) that ensures fast, easy, and reliable deployment of cloud-native apps. Cloud Foundry ensures that the build and deploy aspects of coding remain carefully coordinated with any attached services — resulting in quick, consistent, and reliable iterating of applications. Cloud Foundry has a Lite plan that allows quick deployments for testing purposes.
