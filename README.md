---
page_type: sample
languages:
- csharp
products:
- dotnet
description: "Add 150 character max description"
urlFragment: "update-this-to-unique-url-stub"
---

# Scenario: Native App calling Web API 
>Applies To: AD FS 2019 and later 
 
Learn how to build a native app signing-in users authenticated by AD FS 2019 and acquiring tokens using [MSAL library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)  to call web APIs.  
 
Before reading this article, you should be familiar with the [AD FS concepts](../adfs-modern-auth-concepts.md) and [Authorization code grant flow](../../overview/ad-fs-scenarios-for-developers?branch=pr-en-us-333#authorization-code-grant-flow
)
 
## Overview 
 
 ![Overview](media/adfs-msal-native-app-web-api/native1.png)

In this flow you add authentication to your Native App (public client), which can therefore sign in users and calls a Web API. To call a Web API from a Native App that signs in users, you can use MSAL's [AcquireTokenInteractive](https://docs.microsoft.com/en-us/dotnet/api/microsoft.identity.client.ipublicclientapplication.acquiretokeninteractive?view=azure-dotnet#Microsoft_Identity_Client_IPublicClientApplication_AcquireTokenInteractive_System_Collections_Generic_IEnumerable_System_String__) token acquisition method. To enable this interaction, MSAL leverages a web browser. 

 
To better understand how to configure a Native App in ADFS to acquire access token interactively, let’s use a sample available [here](https://github.com/microsoft/adfs-sample-msal-dotnet-native-to-webapi) and walkthrough the app registration and code configuration steps.  
 

## Pre-requisites 


- GitHub client tools 
- AD FS 2019 or later configured and running 
- Visual Studio 2013 or later 
 

## App Registration in AD FS 
This section shows how to register the Native App as a public client and Web API as a Relying Party (RP) in AD FS 

  1. In **AD FS Management**, right-click on **Application Groups** and select **Add Application Group**.   
  
  2. On the Application Group Wizard, for the **Name** enter **NativeAppToWebApi** and under **Client-Server applications** select the **Native application accessing a Web API** template. Click **Next**.  
  
      ![App Reg](media/native2.png)  

  3. Copy the **Client Identifier** value. It will be used later as the value for **ClientId** in the application's **App.config** file. Enter the following for **Redirect URI:** https://ToDoListClient. Click **Add**. Click **Next**.  
 
     ![App Reg](media/native3.png) 

  4. On the Configure Web API screen, enter the **Identifier:** https://localhost:44321/. Click **Add**. Click **Next**. This value will be used later in the application's **App.config** and **Web.config** files.
 
     ![App Reg](media/native4.png)   
  
  5. On the Apply Access Control Policy screen, select **Permit everyone** and click **Next**. 
  
     ![App Reg](media/native5.png)   
  
  6. On the Configure Application Permissions screen, make sure **openid** is selected and click **Next**.  
     
     ![App Reg](media/native6.png) 

  7. On the Summary screen, click **Next**.
  
  8. On the Complete screen, click **Close**. 
  
  9. In AD FS Management, click on **Application Groups** and select **NativeAppToWebApi**         application group. Right-click and select **Properties**.
  
      ![App Reg](media/native7.png)

  10. On NativeAppToWebApi properties screen, select **NativeAppToWebApi – Web API** under **Web API** and click **Edit…** 
  
      ![App Reg](media/native8.png) 

  11. On NativeAppToWebApi – Web API Properties screen, select **Issuance Transform Rules** tab and click **Add Rule…** 
  
      ![App Reg](media/native9.png) 

  12. On Add Transform Claim Rule Wizard, select **Transform an Incoming Claim** from the **Claim rule template:** dropdown and click **Next**.  
  
      ![App Reg](media/native10.png) 

  13. Enter **NameID** in **Claim rule name:** field. Select **Name** for **Incoming claim type:**, **Name ID** for **Outgoing claim type:** and **Common Name** for **Outgoing name ID format:**. click **Finish**.
  
      ![App Reg](media/native11.png) 

  14. Click OK on NativeAppToWebApi – Web API Properties screen and then NativeAppToWebApi Properties screen.  
 
## Code Configuration 
This section shows how to configure a Native App to sign-in user and retrieve token to call the Web API 

1. Download the sample from [here](https://github.com/microsoft/adfs-sample-msal-dotnet-native-to-webapi) 

2. Open the sample using Visual Studio 

3. Open the App.config file. Modify the following: 
   - ida:Authority - enter https://[your AD FS hostname]/adfs
   - ida:ClientId - enter the **Client Identifier** value from #3 in App Registration in AD FS section above. 
   - ida:RedirectUri - enter the **Redirect URI** value from #3 in App Registration in AD FS section above.
   - todo:TodoListResourceId – enter the **Identifier** value from #4 in App Registration in AD FS section above 
   - ida: todo:TodoListBaseAddress - enter the **Identifier** value from #4 in App Registration in AD FS section above. 
 
     ![code config](media/native12.png)

 4. Open the Web.config file. Modify the following: 
    - ida:Audience - enter the **Identifier** value from #4 in App Registration in AD FS section above 
    - ida: AdfsMetadataEndpoint - enter https://[your AD FS hostname]/federationmetadata/2007-06/federationmetadata.xml 
    
      ![code config](media/native13.png)
 
  
## Test the sample 
This section shows how to test the sample configured above. 

  1. Once the code changes are made rebuild the solution 
 
  2. On Visual Studio, right click on solution and select **Set StartUp Projects…**  
 
     ![App test](media/native14.png)

  3. On the Properties pages make sure **Action** is set to **Start** for each of the Projects 
      
     ![App test](media/native15.png)

  4. At the top of Visual Studio, click the green arrow.  
 
     ![App test](media/native16.png)

  5. On the Native App’s Main screen, click on **Sign In**.  
  
     ![App test](media/native17.png)

> [!NOTE]  
> If you don’t see the native app screen, search and remove *msalcache.bin files from the folder where project repo is saved on your system. 

  6. You will be re-directed to the AD FS sign-in page. Go ahead and sign in. 
  
      ![App test](media/native18.png)

  7. Once signed-in, enter text **Build Native App to Web Api** in the **Create a To Do item**. Click **Add item**.  This will call the **To Do List Service (Web API)** and add the item in the cache. 
    
       ![App test](media/native19.png)


## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
