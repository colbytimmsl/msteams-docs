### YamlMime:Tutorial
title: Messaging extensions 
metadata:
  title: Messaging extensions 
  description: In this tutorial, you'll learn to set up  Messaging extensions for Teams.
  audience: Developer
  level: Beginner
  ms.date: 12/06/2021
  ms.topic: interactive-tutorial
  nextTutorialHref: apps-in-teams-meetings/enable-and-configure-your-app-for-teams-meetings.md
  nextTutorialTitle: Read more to enable and configure apps for meetings
  ms.custom: mvc
  ms.localizationpriority: high
items:
- durationInMinutes: 1
  content: |
   Messaging extensions allow the users to interact with your web service through buttons and forms in the Microsoft Teams client.

    This step-by-step guide illustrates how to build an Action-based Messaging Extension.
   
    You'll see the following output:

       :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::

- title: Prerequisites
  durationInMinutes: 1
  content: |
    Ensure you install the following tools and set up your development environment:  

    * [.NET Core SDK](https://dotnet.microsoft.com/download) version 3.1
    * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
    * [ngrok](https://ngrok.com/download) latest version (only for devbox testing) or any equivalent tunneling solution
    
      > [!NOTE]
      > After downloading ngrok, sign up and install [authtoken](https://ngrok.com/download).
    
    * [Microsoft Teams](https://teams.microsoft.com/) with valid account
    * [SignalR](https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc) to update agenda in real-time

    > [!NOTE]
    > Use version 1.7.0 or later of [Teams SDK](/javascript/api/overview/msteams-client?view=msteams-client-js-latest&preserve-view=true), as versions prior to it do not support meeting sidepanel.

- title: Set up local environment
  durationInMinutes: 1
  content: |
   Clone `BotBuilder-Samples` repository to your local GitHub:  

    1. Open [Microsoft Teams Samples](https://github.com/OfficeDev/Microsoft-Teams-Samples).
    1. Select **Code**.
    1. From the dropdown menu, select **Open with GitHub Desktop**.

       ![Clone repository](~/assets/images/sbs-messagingextension-action/clonerepository1.png)

    1. Select **Clone**. 

- title: Create and register your bot in Azure AD portal
  durationInMinutes: 5
  content: |
    To create and register your bot in Azure Active Directory, create a tunnel using ngrok, and add messaging endpoint, perform the following steps:

    * Create Azure Bot resource to register bot with Azure Bot Service.
    * Create client secret to enable SSO authentication of the bot.
    * Add Microsoft Teams channel to deploy the bot to a Teams channel.
    * Use ngrok to create a tunnel to your web server's endpoints.
    * Add messaging endpoint to the ngrok tunnel you created.

    **To create Azure Bot resource**

    1. Go to the [Azure portal](https://portal.azure.com/).
    1. Select **Create a resource**.
    1. In the search box, enter **Azure Bot**.
    1. Select **Enter**.
    1. Select **Azure Bot**.

          :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::

    1. Select **Create**.
    1. Enter required bot handle name in **Bot handle**.
    1. From the **Subscription** dropdown list, select **msteams.nonprod.pub.msft.aplt**.
    1. From the **Resource group** dropdown list, select your existing resource group. 
    
         :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::
    
       You can also create a new resource group (select **Create new** > enter resource name > select **OK**).
    
    1. If you have created a new resource group, select the required location from **New resource group location** dropdown list.

         :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::

    1. In the **Microsoft App ID** section, by default **Create new Microsoft App ID** is selected. 
    
       You can either select **Use existing app registration** and enter **Existing app ID** and **Existing app password**, or select **Create new Microsoft App ID**.

       > [!NOTE]
       > You can't create more than one bot with the same **Microsoft App ID**.

    1. Select **Review + create**.

        ![Create Microsoft App ID]
         :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::
        

    1. If the validation passes, select **Create**. 

        It takes a few moments for your bot service to be provisioned. 

    1. Select **Go to resource**. 

        ![Deploy App](~/assets/images/sbs-messagingextension-action/botdeployment.png)

        Your Azure bot is created.

        ![Azure bot resource created](~/assets/images/sbs-messagingextension-action/bot-page.png)

    **To create client secret**

      Perform the following steps if you have created a new **Microsoft App ID**:

    1. In the left panel, select **Configuration**. 

       > [!TIP]
       > Save the **Microsoft App ID** or **Client ID** for future reference.

    1. Next to **Microsoft App ID**, select **Manage**.

    1. Select **Multitenant** under **App Type**.

        ![Microsoft App ID] :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::

    1. In the **Client secrets** section, select **New client secret**. 

        ![New client secret] :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::
    
       The **Add a client secret** window appears.  

    1. Enter **Description**.
    
    1. Select **Add**.

        ![Add client secret to app] :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::

    1. In the **Value** column, select **Copy to clipboard**.

         ![Value of client secret]( :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::
       
       > [!TIP]
       > Save the **Client secrets** value or app password for future reference.

    **To add the Microsoft Teams channel**

    1. Select **Home**.

            :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png"alt-text="output style="border: 1px solid black":::

    1. Select your bot from **Recent resources**.

    1. Select **Channels** in the left pane. 

    1. Select **Microsoft Teams** <img src="~/assets/images/bots/teamsicon.png" alt="Teams icon" width="20"/>.

    1. Select the checkbox to accept the **Terms of Service**.
    
    1. Select **Agree**.

          ![Terms of service](~/assets/images/sbs-messagingextension-action/terms.png)

    1. Select **Save**.

          ![Select Teams](~/assets/images/sbs-messagingextension-action/config-teams.png)   
    
    **To create tunnel for local web server**

    Use ngrok to create a tunnel to your locally running web server's publicly available HTTPS endpoints. Run the following command in ngrok:

     ```bash
     ngrok http -host-header=localhost 3978
     ```

    > [!TIP]
    > If you encounter **ERR_NGROK_4018**, follow the steps provided in the command prompt to sign-up and authenticate ngrok. Then run the `ngrok http -host-header=localhost 3978` command.

    **To add messaging endpoint**

    1. From ngrok, copy the HTTPS URL (https to io).

        ![ngrok HTTPS URL](~/assets/images/sbs-messagingextension-action/ngrok1.png)

        > [!NOTE]
        > The HTTPS URL in your ngrok is your fully qualified domain name.
        > The `WebAppDomain` is a fully qualified domain name that does not include `https://` in it.

    1. In **Settings** for the Azure bot that you created, select **Configuration**.

    1. In **Messaging endpoint**, use the HTTPS URL available from ngrok and at the end of the URL add **/api/messages**.

        ![Messaging Endpoint](~/assets/images/sbs-messagingextension-action/messaging-endpoint.png)

    1. Select **Apply**.

        You have successfully set up a bot in Azure Bot Service.


- title: Update the Azure AD app registration
  durationInMinutes: 1
  content: |
    **To register your app through the Azure AD portal**  

    1. Go to the [Azure portal](https://portal.azure.com/).

    1. Select **Azure Active Directory**.

    1. In the left navigation panel, select **App Registrations**.

    1. Select your bot.

       ![App registration](~/assets/images/sbs-messagingextension-action/appregister.png)

    1. Under **Manage**, select **Expose an API**.

    1. Select **Set**.

       ![Expose an API](~/assets/images/sbs-messagingextension-action/exposeanapi.png)

    1. Set the **Application ID URI** in the form of `api://{AppID}`.

       ![Set link](~/assets/images/sbs-messagingextension-action/setlink.png)

    1. Insert the `WebAppDomain` value between `api://` and `/{AppID}`.</br>
        `api://ae57****.ngrok.io/{AppID}`</br>
        
       The following image shows the domain name:
        
        ![App ID URI](~/assets/images/sbs-messagingextension-action/appIDuri.png)

        > [!NOTE]
        > If you're using a tunneling service such as ngrok, ensure you update the value whenever your ngrok subdomain changes.
        > `For example: api://f631****.ngrok.io/92c11075-c629-4a1e-ab58-02b4fd4204c2`, where `f631****.ngrok.io` is the new ngrok subdomain name.

    1. Select **Add a scope**. 

       ![Select scope](~/assets/images/sbs-messagingextension-action/selectscope.png)
    
    1. In the panel that appears, enter `access_as_user` as the **Scope name**.
  
    1. Set **Who can consent?** to `Admins and users`.
  
    1. To configure the admin and user consent prompts with appropriate values for `access_as_user` scope, provide the following information in the fields:</br>
    
         * Enter `Teams can access the user’s profile` as **Admin consent display name**.

         * Enter `Allows Teams to call the app’s web APIs as the current user` as **Admin consent description**.

         * Enter `Teams can access the user profile and make requests on the user’s behalf` as **User consent display name**.

         * Enter `Enable Teams to call this app’s APIs with the same rights as the user` as **User consent description**.
  
    1. Ensure that **State** is set to **Enabled**.
  
    1. Select **Add scope** to save.

        ![Add a scope](~/assets/images/sbs-messagingextension-action/addascope.png)

        > [!NOTE]
        > The **Scope name** should match with the **Application ID** URI with `/access_as_user` appended at the end.</br>
           `api://ae57****.ngrok.io/00000000-0000-0000-0000-000000000000/access_as_user`

        ![Scopes](~/assets/images/sbs-messagingextension-action/scopes.png) 
  
    1. In the **Authorized client applications** section, identify the applications that you want to authorize for your app’s web application. 
    
    1. Select **Add a client application**. 

        ![Select client application](~/assets/images/sbs-messagingextension-action/selectclientapp.png) 

    1. Enter **Client ID**: `1fec8e78-bce4-4aaf-ab1b-5451cc387264` for Teams mobile or desktop application. 

        ![Add client application 1](~/assets/images/sbs-messagingextension-action/addclientapplication1.png) 

       You can enter **Client ID**: `5e3ce6c0-2b1f-4285-8d4b-75ee78787346` for Teams web application.

        ![Add client application 2](~/assets/images/sbs-messagingextension-action/addclientapplication2.png) 

    1. Select **Authorized scopes**.

        ![Add client application 2](~/assets/images/sbs-messagingextension-action/authorizedscope.png) 

       The following image displays the client IDs:

        ![Client applications](~/assets/images/sbs-messagingextension-action/clientapps.png) 
  
    1. In the left panel, select **API Permissions**. 

       > [!NOTE]
       > Users need to consent to these permissions only if the Azure AD app is registered in a different tenant.

    1. Select **Add a permission**.

        ![Add permission](~/assets/images/sbs-messagingextension-action/addpermission.png)

    1. Select **Microsoft Graph**.

    1. Select **Delegated permissions**.

        By default, **User.Read** is selected.

         ![User](~/assets/images/sbs-messagingextension-action/userpermission.png)

    1. Add the following permissions:</br>
         * **email**
         * **offline_access**
         * **OpenId**
         * **profile**

    1. Select **Add permissions**.

         ![Other permissions](~/assets/images/sbs-messagingextension-action/other-permissions.png)

    1. From the left panel, select **Authentication** to set a redirect URI. 

       > [!NOTE]
       > If an app is not granted IT admin consent, users must provide consent the first time they use an app.
               
         1. Select **Add a platform**.
         1. Select **Web**.

            ![Web](~/assets/images/sbs-messagingextension-action/webauth.png)

         1. Enter the redirect URI for your app by appending `auth-end` to fully qualified domain name:</br> 
           `https://ae57****.ngrok.io/auth-end`. </br>

         1. Enable **Implicit grant and hybrid flows** by selecting the following checkboxes:
             * **ID tokens**
             * **Access tokens**
   
         1. Select **Configure**.

            ![Auth-end](~/assets/images/meetings-side-panel/authend.png)


- title: Set up app settings and manifest files
  durationInMinutes: 1
  content: |
    1. Navigate to **appsettings.json** in cloned repository.

        ![App settings location](~/assets/images/sbs-messagingextension-action/appsettingslocation.png)

    1. Open **appsettings.json** in **Visual Studio 2019** and update the following information:  

         * Set `"MicrosoftAppId"` to your bot's **Microsoft App ID**.
         * Set `"MicrosoftAppPassword"` to your bot's client secret ID value.
         * Set `"BaseUrl"` to the fully qualified domain name.

        ![App settings](~/assets/images/sbs-messagingextension-action/appsettings.png)

    1. Navigate to **manifest.json** in cloned repository.

        ![Manifest file location](~/assets/images/sbs-messagingextension-action/manifestlocation.png)
    
    1. Open **manifest.json** in **Visual Studio 2019** and make the following changes:

         * Replace all occurrences of `<<Your_Domain_URL>>` with your fully qualified domain name.
         * Replace all occurrences of `<<Microsoft-App-ID>>` with your bot's **Microsoft App ID**.

        ![Manifest image2](~/assets/images/sbs-messagingextension-action/manifest-2.png)

- title: Build and run the service
  durationInMinutes: 1
  content: |
    **To build and run the service using Visual Studio 2019 or Command line**

    # [Visual Studio 2019](#tab/vs2019)

       1. Launch **Visual Studio 2019**.
       1. Navigate to **File** > **Open** > **Project/Solution**.
    
          ![Open file](~/assets/images/meeting-token-generator/sbs-messagingextension-action-VSopenfile.png)

       1. Select **SidePanel.sln** file from **csharp** folder.

          ![Solution File](~/assets/images/sbs-messagingextension-action/Tokenfileready.png)

       1. Press **F5** to run the project.
    
       1. Select **Yes** if the following dialog appears:

          ![Trust Certificate](~/assets/images/sbs-messagingextension-action/meeting-token-generator-certificate.png)

          A webpage opens with a message **Your bot is ready!**.

          ![App ready](~/assets/images/meetings-side-panel/appisready.png) 

        
    # [Command line](#tab/cli)

    Navigate to **samples > meetings-sidepanel > csharp > Side Panel** in a Command Prompt window and enter the following command:

    ```bash
    dotnet run
    ```
   
    ![Dotnet](~/assets/images/meetings-side-panel/dotnetruncmd.png)
      
- title: Add Meetings SidePanel app to Teams
  durationInMinutes: 1
  content: |
    **To create a Teams meeting and add Meetings SidePanel App**

    1. In your cloned repository, navigate to **csharp > Side Panel > Manifest**.

    1. Create a .zip with the following files that are present in the **Manifest** folder: 
       * manifest.json
       * icon-outline.png
       * icon-color.png

       ![Zip file](~/assets/imagessbs-messagingextension-action/zipfile.png) 
    
    1. Create a meeting with a few presenters and attendees.
   
    1. After the meeting is created, go to the meeting details page and select **Add an app**.

       ![Add an app](~/assets/imagessbs-messagingextension-action/addanapp.png) 
   
    1. In the pop-up that opens, select **Manage apps**.

       ![Manage apps](~/assets/images/meeting-token-generator/meeting-token-generator-manageappsimage.png)
   
    1. Select **Upload a custom app**. 

       ![Upload custom app](~/assets/images/sbs-messagingextension-action/uploadcustomapp.png)

    1. Select **Open** to upload the .zip file that you created in the **Manifest** folder.

       ![Select zip file](~/assets/images/sbs-messagingextension-action/selectzip.png)

    1. Select **Add**.

       ![Add the app](~/assets/images/sbs-messagingextension-action/addtheapp.png)

       The **Manage apps** section displays the list of applications.

       ![App in Manage apps](~/assets/images/sbs-messagingextension-action/manageappsection.png)
   
    1. Go to Teams meeting.
    
    1. Select **Add an app**. 
    
       In the app selection page, the app displays as **Side Panel**.
  
       ![App icon in Teams](~/assets/images/sbs-messagingextension-action/appicon.png)

    1. Select the **Side Panel** app.
    
    1. Select **Save**.

       ![Welcome App](~/assets/images/sbs-messagingextension-action/welcomeapp.png)
   
       The app is visible in the meeting SidePanel.         

- title: Interact with the app in Teams
  durationInMinutes: 1
  content: |
    Let's interact with the app in Teams!

    1. Select **Create Card** command from the compose box command list.

       ![Token in Meet](~/assets/imagessbs-messagingextension-action/sidepanelinmeet.png)

    1. Enter your information in the modal popup.

       ![Token in Meet](~/assets/images/sbs-messagingextension-action/newagenda.png)

    1. Select **Submit**.

       ![Your Token](~/assets/images/sbs-messagingextension-action/youragenda.png)

    1. Select ... overflow menu from an existing message.

       ![Token in Meet](~/assets/images/meetings-side-panel/newagenda.png)

    1. Point to **More actions**.

       ![Your Token](~/assets/images/meetings-side-panel/youragenda.png)
    1. Select **Share Message**.


       ![Token in Meet](~/assets/images/meetings-side-panel/newagenda.png)

    1. Select the checkbox if you need to include image and submit.

       ![Your Token](~/assets/images/meetings-side-panel/youragenda.png)


- title: Complete challenge
  durationInMinutes: 1
  content: |
    Did you come up with something like this?

       ![Token in Meet](~/assets/images/meetings-side-panel/sidepanelondesktopmobile.png)


- content: |
    You've completed the tutorial to get started with a **Side Panel** app!

