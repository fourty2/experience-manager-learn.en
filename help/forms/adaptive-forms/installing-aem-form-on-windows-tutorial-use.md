---
title: Simplified Steps for Installing AEM Forms on Windows
seo-title: Simplified Steps for Installing AEM Forms on Windows
description: null
seo-description: null
uuid: a148b8f0-83db-47f6-89d3-c8a9961be289
products: SG_EXPERIENCEMANAGER/6.4/FORMS
products: SG_EXPERIENCEMANAGER/6.3/FORMS
discoiquuid: 1182ef4d-5838-433b-991d-e24ab805ae0e
---
# Simplified Steps for Installing AEM Forms on Windows

>[!NOTE]
>Never double click the AEM Quick Start jar, if you intend to use AEM Forms.
>Also, make sure there are no spaces in the AEM Forms Installation folder path.
>For Example do not install AEM Forms in c:\jack and jill\AEM Forms folder

>[!NOTE]
If you are installing AEM Forms 6.5, Please make sure you have installed the following 32 bit Microsoft Visual C++ redistributables
1.Microsoft Visual C++ 2008 redistributable
2.Microsoft Visual C++ 2010 redistributable
3.Microsoft Visual C++ 2012 redistributable
4.Microsoft Visual C++ 2013 redistributable (as of 6.5)

Although we recommend following the [official documentation](https://helpx.adobe.com/experience-manager/6-3/forms/using/installing-configuring-aem-forms-osgi.html) for installing AEM Forms. The following steps can be followed to install and configure AEM Forms on Windows environment:

1. Make sure you have the appropriate JDK installed
   * AEM 6.2 you need: Oracle SE 8 JDK 1.8.x (64bit)
   * AEM 6.3 and AEM 6.4 you need: Oracle SE 8 JDK 1.8.x (64bit)
   * AEM 6.5 you need JDK 8 or JDK 11
   * [Official JDK Requirements](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html) are listed here
1. Make sure the JAVA_HOME is set to point to the JDK you have installed.
1. To create the JAVA_HOME variable in windows follow the steps below:

   * Right-click My Computer and select Properties
   * On the Advanced tab, select Environment Variables and create a new system variable called JAVA_HOME.
   * Set the variable value to point to JDK installed on your system. For example c:\program files\java\jdk1.8.0_25

2.Create a folder called AEMForms on your C drive
3.Locate the AEMQuickStart.Jar and move it into AEMForms folder
4.Copy the license.properties file into this AEMForms folder
5.Create a batch file called "StartAemForms.bat" with the following content:

* java -d64 -Xmx2048M -jar AEM_6.3_Quickstart.jar -gui.
Here AEM_6.3_Quickstart.jsr is the name of my AEM quickstart jar.
You can rename your jar to any name, but please make sure that name is reflected in the batch file.Save the batch file in the AEMForms Folder.

6.Open a new command prompt, and navigate to c:\aemforms.

7.Execute the StartAemForms.bat file from the command prompt.

You should get a small dialog box indicating the progress of the startup.

8.Once the startup is complete, open the sling.propertiesfile. This is located in c:\AEMForms\crx-quickstart\conf folder.

9.Copy the following 2 lines towards the bottom of the file

**sling.bootdelegation.class.com.rsa.jsafe.provider.JsafeJCE=com.rsa.&#42;** **sling.bootdelegation.class.org.bouncycastle.jce.provider.BouncyCastleProvider=org.bouncycastle.&#42;**
These two properties are required for document services to work
Save the sling.properties file

10.[Login to package share](http://localhost:4502/crx/packageshare/login.html)

* You will need AdobeId to login to package share
* Search for AEM Forms Add on package appropriate for your version of AEM Forms and Operating System
* Or [you can download the appropriate forms addon package](https://helpx.adobe.com/aem-forms/kb/aem-forms-releases.html)
* After you have installed the add on package the following is very important

  * **Make sure all the bundles are in active state. (Except for AEMFD Signatures bundle).**
  * **It would typically take 5 or more minutes for all the bundles to get to active state.**

  * **Once all the bundles are active(Except the AEMFD Signatures bundle), restart your system to complete the AEM Forms**

11.Whitelist sun.calendar.util package.

* Open feilix web console in your [browser window](http://localhost:4502/system/console/configMgr)
* Search and open Deserialization Firewall Configuration
In the whitelist field add sun.util.calendar package and save

Congratulations!!! You have now installed and configured AEM Forms on your system.
If you need to configure additional capabilites such as [Reader Extensions](https://helpx.adobe.com/experience-manager/6-3/forms/using/configuring-document-services.html) or [PDFG](https://helpx.adobe.com/experience-manager/6-3/forms/using/install-configure-pdf-generator.html), please follow the official documentation