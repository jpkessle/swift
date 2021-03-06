---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-19"

keywords: server-side swift, swift cli, swift dependency, swift commands app, create app swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Creating a server-side Swift app with the CLI
{: #swift_cli}

{{site.data.keyword.cloud}} offers solutions and services to enable Swift Developers and applications with the security, AI, and value demanded by your customers. With a broad portfolio of offerings and SDKs, you can use these services, and bring your cutting-edge applications to market quickly.
{: shortdesc}

The following guide is intended to help you build, run locally, and deploy a server-side Swift app. Learn how to use the {{site.data.keyword.dev_cli_long}} to run these actions with standard commands.

You can use the {{site.data.keyword.dev_cli_short}} to manage your server-side applications with more than a dozen commands. Learn more about the `ibmcloud dev` commands at [{{site.data.keyword.dev_cli_notm}} CLI](/docs/cli/idt?topic=cloud-cli-idt-cli).

## Step 1. Requirements for Developers
{: #prereqs-swift-cli}

To get started with {{site.data.keyword.cloud_notm}}, make sure that you meet the following requirements.

### Operating System
{: #swift-cli-os-reqs}

Develop Swift apps with best practice by using the latest MacOS supported hardware, and testing with the latest iOS releases. Sign up for an [Apple Developer](https://developer.apple.com/){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon") account to enable testing on a physical device.

### iOS and Xcode
{: #swift-cli-ios_xcode}

- [Install Xcode 8+ (or higher)](https://developer.apple.com/xcode/){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon")
- [Deploy to iOS devices 8 (or higher)](https://support.apple.com/downloads/ios){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon")
- Before app submission to Apple, review the [App Store Submission Guidelines](https://developer.apple.com/app-store/resources/){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon")

### SDKs and Dependency management
{: #swift-cli-sdk-dependency}

The following tools ensure that you can install the native SDKs to work with the various {{site.data.keyword.cloud_notm}} Services.

- [Install CocoaPods for {{site.data.keyword.cloud_notm}} SDKs](https://cocoapods.org/){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon")
  ```
  sudo gem install cocoapods
  ```
  {: codeblock}
  
- [Install Homebrew to help install Carthage](https://brew.sh/){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon")
  ```
  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
  ```
  {: codeblock}

- [Install Carthage for Watson SDKs](https://github.com/Carthage/Carthage){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon")
  ```
  brew install carthage
  ```
  {: codeblock}

## Step 2. Installing tools for local development
{: #swift-cli-install-tools}

The {{site.data.keyword.cloud}} provides local CLI tools that help you work with various aspects of the {{site.data.keyword.cloud_notm}}. For more information, see [{{site.data.keyword.dev_cli_long}} Information](/docs/cli?topic=cloud-cli-getting-started). You can use the tools for testing a Swift backend in a local Docker image before cloud deployment.

* For MacOS and Linux, run the following command:
  ```
  curl -sL http://ibm.biz/idt-installer | bash
  ```
  {: codeblock}

* For Windows 10, run the following command as an administrator:
  ```
  Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
  ```
  {: codeblock}

  Right-click the Windows PowerShell icon, and select **Run as administrator**.
  {: tip}

## Step 3. Creating a Swift app
{: #create-swift-app-cli}

1. Run the `ibmcloud dev create` command from {{site.data.keyword.dev_cli_short}} CLI to generate a pre-configured starter. 
  ```
  ibmcloud dev create
  ```
  {: codeblock}

  Be sure to log in with an {{site.data.keyword.cloud_notm}} account to create a project. First-time users can [register](https://{DomainName}/registration){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon") for a free account. Use the `ibmcloud login` command to log in on the command line.
  {: tip}

2. When prompted, select options 1, then 6, and lastly 2, as displayed in the following example:
  ```
  ? Select a service type:                  
  1. Backend Service / Web App
  2. Mobile Client
  Enter a number> 1

  ? Select a language:
  1. Java - MicroProfile / Java EE
  2. Java - Spring
  3. Node
  4. Python - Django
  5. Python - Flask
  6. Swift
  Enter a number> 6

  ? Select a Starter Kit:
  1. Backend for Frontend - Swift Kitura - A starter for building 
  backend-for-frontend APIs in Swift, using the Kitura framework.
  2. Web App - Create Project
  3. Web App - Swift Kitura Basic - A starter that provides a basic web serving 
  application in Swift, using the Kitura framework.
  Enter a number> 2
  ```
  {: screen}

3. Provide a name for your application:
  ```
  ? Enter a name for your application> swift_project

  Generating application ...
  ```
  {: screen}

## Step 4. Building, running, and deploying your application
{: #swift-cli-deploy}

You can now build, run, and deploy your application by using the {{site.data.keyword.dev_cli_short}}.

### Building your app
{: #swift-build-cli}

You can now build your application, which is a prerequisite to running your application. Use the following command in the root of the application directory to build your app:
```
ibmcloud dev build
```
{: codeblock}

### Running your app
{: #swift-run-cli}

After a successful build, you can run your application in a local container with the following command:
```
ibmcloud dev run
```
{: codeblock}

A local host and port to view your application's landing page is displayed if the command runs successfully.

### Deploying your app
{: #swift-deploy-cli}

Deploy your application to the {{site.data.keyword.cloud_notm}} with the `deploy` command:
```
ibmcloud dev deploy
```
{: codeblock}

## Next steps
{: #swift-cli-next notoc}

Learn to use the {{site.data.keyword.cloud_notm}} Developer Console for Apple that enables Developers to create apps from various Starter Kits, create and connect key {{site.data.keyword.cloud_notm}} optimized services, and then quickly download working code or set up for continuous delivery. Users can create, view, configure, and manage your app, as well as download your app's code. By using the Developer Console for Apple, you can quickly evaluate, and test {{site.data.keyword.cloud_notm}} services with a brand new app.

Ready to jump in? Visit the [{{site.data.keyword.cloud_notm}} Developer Console for Apple](https://{DomainName}/developer/appledevelopment/starter-kits){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon") now to get started.
{: tip}

For more information, see [Developing Swift apps with Starter Kits](/docs/swift/starter_kit?topic=swift-starterkits-intro#starterkits-intro).
