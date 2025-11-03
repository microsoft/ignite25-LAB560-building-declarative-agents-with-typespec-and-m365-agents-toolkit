# Exercise 1: Build your first agent with TypeSpec using Microsoft 365 Agents Toolkit

It’s time to build your first Declarative Agent using Microsoft 365 Agents Toolkit. 
You will create an agent called **RepairServiceAgent**, which interacts with repairs data via an existing Repairs API service to help users manage car repair records.

Once signed into the machine, you will be able to access VS Code from the desktop. Open VS Code.

>[!TIP]
> When you open the folder in VS Code, you may get a prompt window asking if you trust the authors of the files in the folder. This is expected and you can safely select **Yes, I trust the authors**. The dialog is a security safeguard that helps you decide whether to run all features or limit execution based on the trustworthiness of the code authors. If you're opening your own code or from a reliable source, it's safe to trust.


## Step 1: Scaffold your base agent project using Microsoft 365 Agents Toolkit

- In VS Code, locate the Microsoft 365 Agents Toolkit icon <img width="24" alt="m365atk-icon" src="https://github.com/user-attachments/assets/b5a5a093-2344-4276-b7e7-82553ee73199" /> from the VS Code menu on the left and select it. An activity bar will be open. 
-	Select the "Create a New Agent/App" button in the activity bar which will open the palette with a list of app templates available on Microsoft 365 Agents Toolkit.
-	Choose "Declarative Agent" from the list of templates.
-	Next, select "Start with TypeSpec for Microsoft 365 Copilot" to define your agent using TypeSpec.
-	Next, select the **Default folder** where you want the agents toolkit to scaffold the agent project.
-	Next, give an application name like - +++RepairServiceAgent+++ and select Enter to complete the process. You will get a new VSCode window with the agent project preloaded.

> [!NOTE] 
> Ignore any alerts in VS Code such as "No TypeSpec compiler found…" prompting you to install the compiler. These can be safely disregarded.

## Step 2: Sign into the Microsoft 365 Agents Toolkit 

You'll need to sign into the Microsoft 365 Agents Toolkit in order to upload and test your agent from within it.

-	Within the project window, select the Microsoft 365 Agents Toolkit icon <img width="24" alt="m365atk-icon" src="https://github.com/user-attachments/assets/b5a5a093-2344-4276-b7e7-82553ee73199" /> again, from the left side menu. This will open the agent toolkit’s activity bar with sections like Accounts, Environment, Development etc. 
-	Under "Accounts" section select "Sign in to Microsoft 365". This will open a dialog from the editor to sign in or create a Microsoft 365 developer sandbox or Cancel. Select "Sign in". 
-	In the virtual machine, log into the Microsoft 365 tenant using below credentials:

**Username: +++@lab.CloudPortalCredential(User1).Username+++**

**TAP Token:+++@lab.CloudPortalCredential(User1).AccessToken+++**

-	Once signed in, close the browser and go back to the project window.

## Step 3: Define your agent 

The Declarative Agent project scaffolded by the Agents Toolkit provides a template that includes code for connecting an agent to the GitHub API to display repository issues. In this lab, you'll build your own agent that integrates with a Repairs API service, supporting multiple operations to manage repair data.
Before proceeding with the agent definition, take a moment to examine the Repairs API service to gain a clearer understanding of its functionality.

### Get to know the repair API service

You'll need to explore endpoints and payloads of the API service interactively. Using a **.http** file in Visual Studio Code with the REST Client extension, which is already installed for you,  allows you to define and send HTTP requests directly from your editor. It's a lightweight, code-friendly way to test APIs, inspect responses, and iterate quickly without switching to external tools.

Inside the root folder of the project you just created, create a folder called **http**. 
Create a new file named +++repairs-api.http+++ inside the http folder.

>[!TIP]
> **Creating folders and files in VS Code:**
> - To create a new folder: Right-click in the Explorer panel (file tree) on the left side of VS Code, select "New Folder", and type the folder name.
> - To create a new file: Right-click on the folder where you want to add the file, select "New File", and type the filename with its extension.
> - Alternatively, you can use the icons in the Explorer panel: the folder icon (📁) creates a new folder, and the file icon (📄) creates a new file in the currently selected location.

Copy paste below content into the file.

```
@base_url = https://repairshub.azurewebsites.net

### Get all repair requests
{{base_url}}/repairs

### Get a specific repair request by ID
{{base_url}}/repairs/1

### Create a new repair request
POST {{base_url}}/repairs
Content-Type: application/json

{
  "description": "Repair broken screen",
  "date": "2023-10-01T12:00:00Z",
  "image": "https://example.com/image.png"
}

### Update an existing repair request
PATCH {{base_url}}/repairs
Content-Type: application/json  

{
  "id": 1,
  "description": "Repair broken screen - updated",
  "date": "2023-10-01T12:00:00Z",
  "image": "https://example.com/image-updated.png"
}


### Delete a repair request by ID
DELETE {{base_url}}/repairs/10
Content-Type: application/json

{
  "id": 10
}
```

> Note there is a small delay to process the request from the editor, but the response should come back in a few seconds.

To run each request, hover over each request line (e.g., GET {{base_url}}/repairs) and click **Send Request** to see the response.
Observe the structure of requests and responses and use the response data to understand how your agent will interact with the API.

![http request](https://github.com/user-attachments/assets/050ca976-4523-463d-920f-4f0f2da46249)




#### Repairs API Overview

**Base URL**: *https://repairshub.azurewebsites.net*

| Operation | Method | Endpoint | Payload required | Purpose |
|-----------|--------|----------|------------------|---------|
| Get all repair requests | GET | /repairs | No | Retrieve all repair jobs |
| Get repair by ID | GET | /repairs/{id} | No | Fetch a specific repair job |
| Create a repair request | POST | /repairs | Yes | Submit a new repair job |
| Update a repair request | PATCH | /repairs/{id} | Yes | Modify an existing repair job |
| Delete a repair request | DELETE | /repairs/{id} | No | Remove a repair job by ID |

Now that you’re familiar with the API service, let’s move on to integrating it with your agent.

In the project folder, you will find two **TypeSpec** files **main.tsp** and **actions.tsp**.
The agent is defined with its metadata, instructions and capabilities in the **main.tsp** file.
You'll use the **actions.tsp** file to define your agent’s actions like connecting with the Repairs API service.

Open **main.tsp** and inspect what is there in the default template, which you will modify for our agent's repair service scenario. 

### Update the Agent Metadata and Instructions

In the **main.tsp** file, you will find the basic structure of the agent. Review the content provided by the agents toolkit template which includes:
-	Agent name and description 1️⃣
-	Basic instructions 2️⃣
-	Placeholder code for actions and capabilities (commented out) 3️⃣

![agent template](https://github.com/user-attachments/assets/42da513c-d814-456f-b60f-a4d9201d1620)


Begin by defining your agent for the repair scenario. Replace the "@agent" and "@instructions" definitions with below code snippet.

```typespec
@agent(
  "RepairServiceAgent",
   "An agent for managing repair information"
)

@instructions("""
  ## Purpose
You will assist the user in finding car repair records based on the information provided by the user. 
""")

```

Next, configure a conversation starter, the initial prompt that begins user-agent interaction. Uncomment the default template section and update the title and text fields to match the agent scenario.

```typespec
// Uncomment this part to add a conversation starter to the agent.
// This will be shown to the user when the agent is first created.
@conversationStarter(#{
  title: "List repairs",
  text: "List all repairs"
})

```
This prompt triggers a GET operation to retrieve all repairs from the service. To enable this behaviour in the agent, you' ll need to define the corresponding action. Proceed to the next step to do so.

### Define the action for the agent

Next, you will define the action for your agent by opening the **actions.tsp** file. You'll return to the **main.tsp** file later to complete the agent metadata with the action reference, but first, the action itself must be defined. For that open the file **actions.tsp**.

The default **actions.tsp** template demonstrates how to define an agent action, including metadata, service URL, and operation structure. Replace the sample GitHub logic entirely with definitions relevant to the Repairs API service.

After the module-level directives like import and using statements, replace the existing code up to the point where the "SERVER_URL" is defined with the snippet below. 

```typespec
@service
@server(RepairsAPI.SERVER_URL)
@actions(RepairsAPI.ACTIONS_METADATA)
namespace RepairsAPI{
  /**
   * Metadata for the API actions.
   */
  const ACTIONS_METADATA = #{
    nameForHuman: "Repair Service Agent",
    descriptionForHuman: "Manage your repairs and maintenance tasks.",
    descriptionForModel: "Plugin to add, update, remove, and view repair objects.",
    legalInfoUrl: "https://docs.github.com/en/site-policy/github-terms/github-terms-of-service",
    privacyPolicyUrl: "https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement"
  };
  
  /**
   * The base URL for the  API.
   */
  const SERVER_URL = "https://repairshub.azurewebsites.net";

```

Next, replace the operation in the template code from "searchIssues" to "listRepairs" to get the list of repairs.
Replace the entire block of code starting just after the SERVER_URL definition and ending just *before* the final closing braces with the snippet below. Be sure to leave the closing braces intact.

```typespec
  /**
   * List repairs from the API 
   * @param assignedTo The user assigned to a repair item.
   */

  @route("/repairs")
  @get  op listRepairs(@query assignedTo?: string): string;

```

Now go back to **main.tsp** file and add the action you just defined into the agent. After the conversation starters replace the entire "RepairServiceAgent" namespace with below snippet, 

```typespec
namespace RepairServiceAgent{  
  // Uncomment this part to add actions to the agent.
  @service
  @server(global.RepairsAPI.SERVER_URL)
  @actions(global.RepairsAPI.ACTIONS_METADATA)
  namespace RepairServiceActions {
    op listRepairs is global.RepairsAPI.listRepairs;   
  }
}

```
For now, you'll test only the GET operation. Additional operations will be explored in the next exercise.

## Step 4: (Optional) Understand the decorators

This is an optional step but if curious to know what we have defined in the TypeSpec file just read through this step, or if you wish to test the agent right away go to Step 5.
In the TypeSpec files **main.tsp** and **actions.tsp**, you'll find decorators (starting with @), namespaces, models, and other definitions for your agent.

Check this table to understand some of the decorators used in these files 


| Annotation             | Description                                                                                                                                                     |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| @agent             | Defines the namespace (name) and description of the agent                                                                                                       |
| @instructions       | Defines the instructions that prescribe the behaviour of the agent. 8000 characters or less                                                                     |
| @conversationStarter | Defines conversation starters for the agent                                                                                                                     |
| @op            | Defines any operation. Either it can be an operation to define agent’s capabilities like *op GraphicArt*, *op CodeInterpreter* etc., or define API operations like **op listRepairs**. For a post operation, define it like: *op createRepair(@body repair: Repair): Repair;*                                                                                                               |
| @server           | Defines the server endpoint of the API and its name                                                                                                              |
| @capabilities      | When used inside a function, it defines simple adaptive cards with small definitions like a confirmation card for the operation                                  |

## Step 5: Test your agent

Next step is to test the Repair Service Agent. For this first you need to provision the agent to your tenant. Follow below steps:

- Select the Agents toolkit extension icon <img width="24" alt="m365atk-icon" src="https://github.com/user-attachments/assets/b5a5a093-2344-4276-b7e7-82553ee73199" />. This will open the activity bar for agents toolkit from within your project.
- In the activity bar of the agents toolkit under "LifeCycle" select "Provision". This will build the app package consisting of the generated manifest files and icons and side load the app into the catalog only for you to test. 
This will take a while and you will be able to see a toaster message in VS Code, showing the progress of the task to provision.


If you run into a time out issue as shown below, just quit and reopen your VS Code editor and try again.

![time out error](https://github.com/user-attachments/assets/b5a9f569-bb3b-44e8-a217-502d0fd31ce4)

> [!NOTE] 
> Here the agents toolkit also helps validate all the definitions provided in the TypeSpec file to ensure accuracy. It also identifies errors to streamline the developer experience.

- Next, open Microsoft Edge from lab machine from the taskbar and go to +++https://m365.cloud.microsoft/chat+++ in the browser to open Copilot app. Use the same credentils you used before:
  
  **Username: +++@lab.CloudPortalCredential(User1).Username+++**

  **TAP Token:+++@lab.CloudPortalCredential(User1).AccessToken+++**

- Select the **RepairServiceAgent** from the left side of the screen under **Agents**.

- Select the conversation starter - **List repairs** and send the prompt to the chat to initiate conversation with your agent and check out the response. When prompted to connect the agent to process a query, you’ll usually get a message with buttons to Allow accessing your service through agent. 

- To streamline your experience in this lab, select **"Always allow"** when it appears.

  Once accepted you will see the response from the agent as below: 

![ex1-dem0-01](https://github.com/user-attachments/assets/02400c13-0766-4440-999b-93c88ca45dc7)

- Keep this browser session open for upcoming exercise. 


☑️ You've successfully completed the first exercise! Select **Next >** to go to the next exercise.






