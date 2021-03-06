---
title: Sign in users in JavaScript single-page apps with auth code | Azure
titleSuffix: Microsoft identity platform
description: Learn how a JavaScript app can call an API that requires access tokens using the Microsoft identity platform.
services: active-directory
author: hahamil
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
#Customer intent: As an app developer, I want to learn how to get access tokens and refresh tokens by using the Microsoft identity platform endpoint so that my JavaScript app can sign in users of personal accounts, work accounts, and school accounts.
---

# Quickstart: Sign in users and get an access token in a JavaScript SPA using the Auth Code Flow 

> [!IMPORTANT]
> This feature is currently in preview. Previews are made available to you on the condition that you agree to the [supplemental terms of use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Some aspects of this feature might change before general availability (GA).


This quickstart uses MSAL.js 2.0 with the Authorization Code flow. To use MSAL.js 1.0 with the implicit flow, view [this quickstart](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

In this quickstart, you use a code sample to learn how a JavaScript single-page application (SPA) can sign in users of personal accounts, work accounts, and school accounts. A JavaScript SPA can also get an access token to call the Microsoft Graph API or any web API. See [How the sample works](#how-the-sample-works) for an illustration.

## Prerequisites

* Azure subscription - [Create an Azure subscription for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (to edit project files)


> [!div renderon="docs"]
> ## Register and download your quickstart application
> To start your quickstart application, use either of the following options.
>
> ### Option 1 (Express): Register and auto configure your app and then download your code sample
>
> 1. Sign in to the [Azure portal](https://portal.azure.com).
> 1. If your account gives you access to more than one tenant, select the account at the top right, and then set your portal session to the Azure Active Directory (Azure AD) tenant you want to use.
> 1. Select [App registrations](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Enter a name for your application.
> 1. Under **Supported account types**, select **Accounts in any organizational directory and personal Microsoft accounts**.
> 1. Select **Register**.
> 1. Go to the quickstart pane and follow the instructions to download and automatically configure your new application.
>
> ### Option 2 (Manual): Register and manually configure your application and code sample
>
> #### Step 1: Register your application
>
> 1. Sign in to the [Azure portal](https://portal.azure.com).
>
> 1. If your account gives you access to more than one tenant, select your account at the top right, and then set your portal session to the Azure AD tenant you want to use.
> 1. Select [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Select **New registration**.
> 1. When the **Register an application** page appears, enter a name for your application.
> 1. Under **Supported account types**, select **Accounts in any organizational directory and personal Microsoft accounts**.
> 1. Select **Register**. On the app **Overview** page, note the **Application (client) ID** value for later use.
> 1. In the left pane of the registered application, select **Authentication**.
> 1. Under **Platform Configurations**, select **Add a platform**. A panel opens on the left. There, select the **Single-Page Applications** region.
> 1. Still on the left, set the **Redirect URI** value to `http://localhost:3000/`. 
> 1. Select **Configure**.

> [!div class="sxs-lookup" renderon="portal"]
> #### Step 1: Configure your application in the Azure portal
> To make the code sample in this quickstart work, you need to add a `redirectUri` as `http://localhost:3000/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-javascript/green-check.png) Your application is configured with these attributes.

#### Step 2: Download the project

> [!div renderon="docs"]
> To run the project with a web server by using Node.js, [download the core project files](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Run the project with a web server by using Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Download the code sample](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### Step 3: Configure your JavaScript app
>
> In the *app* folder, edit *authConfig.js*, and set the `clientID`, `authority` and `redirectUri` values under `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Where:
> - *\<Enter_the_Application_Id_Here>* is the **Application (client) ID** for the application you registered.
> - *\<Enter_the_Cloud_Instance_Id_Here>* is the instance of the Azure cloud. For the main or global Azure cloud, simply enter *https://login.microsoftonline.com/*. For **national** clouds (for example, China), see [National clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - *\<Enter_the_Tenant_info_here>* is set to one of the following options:
>    - If your application supports *accounts in this organizational directory*, replace this value with the **Tenant ID** or **Tenant name** (for example, *contoso.microsoft.com*).
>    - If your application supports *accounts in any organizational directory*, replace this value with **organizations**.
>    - If your application supports *accounts in any organizational directory and personal Microsoft accounts*, replace this value with **common**. To restrict support to *personal Microsoft accounts only*, replace this value with **consumers**.
> - *\<Enter_the_Redirect_Uri_Here>* is `http://localhost:3000`
> > [!TIP]
> > To find the values of **Application (client) ID**, **Directory (tenant) ID**, and **Supported account types**, go to the app registration's **Overview** page in the Azure portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### Step 3: Your app is configured and ready to run
> We have configured your project with values of your app's properties.

> [!div renderon="docs"]
>
> Then, still in the same folder, edit *graphConfig.js* file to set the `graphMeEndpoint` and `graphMailEndpoint` for the `apiConfig` object.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> *\<Enter_the_Graph_Endpoint_Here>* is the endpoint that API calls will be made against. For the main or global Microsoft Graph API service, enter `https://graph.microsoft.com`. For more information, see [National cloud deployment](https://docs.microsoft.com/graph/deployments).
>
> #### Step 4: Run the project

Run the project with a web server by using [Node.js](https://nodejs.org/en/download/):

1. To start the server, run the following commands from within the project directory:
    ```bash
    npm install
    npm start
    ```
1. Browse to `http://localhost:3000/`.

1. Select **Sign In** to start the sign-in process and then call Microsoft Graph API.

    The first time you sign in, you're prompted to provide your consent to allow the application to access your profile and sign you in. After you're signed in successfully, your user profile information should be displayed on the page.

## More information

### How the sample works

![How the sample JavaScript SPA works: 1. The SPA initiates sign in. 2. The SPA acquires an ID token from the Microsoft identity platform. 3. The SPA calls acquire token. 4. The Microsoft identity platform returns an Access token to the SPA. 5. The SPA makes and HTTP GET request with the Access Token to the Microsoft Graph API. 6. The Graph API returns an HTTP response to the SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### msal.js

The MSAL.js library signs in users and requests the tokens that are used to access an API that's protected by Microsoft identity platform. The sample's *index.html* file contains a reference to the library:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> You can replace the preceding version with the latest released version under [MSAL.js releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Alternatively, if you have Node.js installed, you can download the latest version by using the Node.js Package Manager (npm):

```batch
npm install msal
```

## Next steps

The [MSAL.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js) contains additional library documentation, a FAQ, and provides issue support.

For a more detailed step-by-step guide on building the application for this quickstart, see:

> [!div class="nextstepaction"]
> [Tutorial to sign in and call MS Graph](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
