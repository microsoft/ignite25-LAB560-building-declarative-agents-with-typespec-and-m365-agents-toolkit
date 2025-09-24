# Exercise 1:  Build your first agent with TypeSpec using Microsoft 365 Agents Toolkit

It’s time to build your first Declarative Agent using Microsoft 365 Agents Toolkit. 
You will create an agent called **RepairServiceAgent**, which interacts with repairs data via an existing API service to help users manage car repair records.


## Step 1: Scaffold your base agent project using Microsoft 365 Agents Toolkit
-	Locate the Microsoft 365 Agents Toolkit icon <img width="24" alt="m365atk-icon" src="https://github.com/user-attachments/assets/b5a5a093-2344-4276-b7e7-82553ee73199" /> from the VS Code menu on the left and select it. An activity bar will be open. 
-	Select the "Create a New Agent/App" button in the activity bar which will open the palette with a list of app templates available on Microsoft 365 Agents Toolkit.
-	Choose "Declarative Agent" from the list of templates.
-	Next, select "Start with TypeSpec for Microsoft 365 Copilot" to define your agent using TypeSpec.
-	Next, select the folder where you want the agents toolkit to scaffold the agent project.
-	Next, give an application name like - "RepairServiceAgent" and select Enter to complete the process. You will get a new VSCode window with the agent project preloaded.



## Step 2: Sign into the Microsoft 365 Agents Toolkit 

You'll need to sign into the Microsoft 365 Agents Toolkit in order to upload and test your agent from within it.

-	Within the project window, select the Microsoft 365 Agents Toolkit icon <img width="24" alt="m365atk-icon" src="https://github.com/user-attachments/assets/b5a5a093-2344-4276-b7e7-82553ee73199" /> again from the left side menu. This will open the agent toolkit’s activity bar with sections like Accounts, Environment, Development etc. 
-	Under "Accounts" section select "Sign in to Microsoft 365". This will open a dialog from the editor to sign in or create a Microsoft 365 developer sandbox or Cancel. Select "Sign in". 
-	In the virtual machine, the credentials to log into the Microsoft 365 tenant will be given in the same "Resources" panel where this instruction is, under title "Azure portal". Use the username and password provided. Make sure you use the copy text instruction.
-	Once signed in, close the browser and go back to the project window.

## Step 3: Define your agent 

The Declarative Agent project scaffolded by the Agents Toolkit provides a template that includes code for connecting an agent to the GitHub API to display repository issues. In this lab, you’ll build your own agent that integrates with a car repair service, supporting multiple operations to manage repair data.

In the project folder, you will find two TypeSpec files `main.tsp` and `actions.tsp`.
The agent is defined with its metadata, instructions and capabilities in the `main.tsp` file.
Use the `actions.tsp` file to define your agent’s actions. If your agent includes any actions like connecting to an API service, then this is the file where it should be defined.

Open `main.tsp` and inspect what is there in the default template, which you will modify for our agent’s repair service scenario. 

### Update the Agent Metadata and Instructions

In the `main.tsp` file you will find the basic structure of the file. Review the content provided by the agents toolkit template which includes:
-	Agent name and description 1️⃣
-	Basic instructions 2️⃣
-	Placeholder code for actions and capabilities (commented out) 3️⃣

![agent template](https://github.com/user-attachments/assets/42da513c-d814-456f-b60f-a4d9201d1620)



Begin by defining your agent for the repair scenario. Replace the "@agent" and "@instructions" definitions with below code snippet

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

Next, add a conversation starter for the agent. Just below the instructions you will see a commented out code for a conversation starter. Uncomment it.
And replace title and text as below.

```typespec
// Uncomment this part to add a conversation starter to the agent.
// This will be shown to the user when the agent is first created.
@conversationStarter(#{
  title: "List repairs",
  text: "List all repairs"
})

```

### Update the action for the agent

Next, you will define the action for your agent by opening the actions.tsp file. You’ll return to the `main.tsp` file later to complete the agent metadata with the action reference, but first, the action itself must be defined. For that open the file `actions.tsp`.


The placeholder code in `actions.tsp` is designed to search for open issues in a GitHub repository. It serves as a starting point to help newcomers understand how to define an action for their agent like action’s metadata, API host url and operations or functions and their definitions. You will replace all this with repair service. 

After the module-level directives like import and using statements, replace the existing code up to the point where the "SERVER_URL" is defined with the snippet below. This update introduces the action metadata and sets the server URL. Also, note that the namespace has been changed from GitHubAPI to RepairsAPI.

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

Next, replace the operation in the template code from "searchIssues" to "listRepairs" which is a repair operation to get the list of repairs.
Replace the entire block of code starting just after the SERVER_URL definition and ending *before* the final closing braces with the snippet below. Be sure to leave the closing braces intact.

```typespec
  /**
   * List repairs from the API 
   * @param assignedTo The user assigned to a repair item.
   */

  @route("/repairs")
  @get  op listRepairs(@query assignedTo?: string): string;

````

Now go back to `main.tsp` file and add the action you just defined into the agent. After the conversation starters replace the entire block of code with below snippet.

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


## Step 4: (Optional) Understand the decorators

This is an optional step but if curious to know what we have defined in the TypeSpec file just read through this step, or if you wish to test the agent right away go to Step 5.
In the TypeSpec files `main.tsp` and `actions.tsp`, you'll find decorators (starting with @), namespaces, models, and other definitions for your agent.

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

Next step is to test the Repair Service Agent. 

- Select the agents toolkit extension icon, to open the activity bar from within your project.
- In the activity bar of the agents toolkit under "LifeCycle" select "Provision". This will build the app package consisting of the generated manifest files and icons and side load the app into the catalog only for you to test. 

> [!knowledge] 
> Here the agents toolkit also helps validate all the definitions provided in the TypeSpec file to ensure accuracy. It also identifies errors to streamline the developer experience.

- Next, open Microsoft Edge from lab machine from the taskbar and go to +++https://m365.cloud.microsoft/chat+++ in the browser to open Copilot app. Use the same credentils you used from the `Resources` tab in this instruction.

> [!help] 
> If for any reason you see a "Something went wrong" screen in Copilot app, just refresh the browser.  


- Select the **RepairServiceAgent** from the right side of the screen under **Agents**.
This will take a while and you will be able to see a toaster message showing the progress of the task to provision.

- Select the conversation starter - `List repairs` and send the prompt to the chat to initiate conversation with your agent and see check out the response.

> [!help] 
> When prompted to connect the agent to process a query, you’ll usually see this screen just once.  
> To streamline your experience in this lab, select **"Always allow"** when it appears.

![ex1-dem0-01](https://github.com/user-attachments/assets/02400c13-0766-4440-999b-93c88ca45dc7)

- Keep this browser session open for upcoming exercise. 


☑️ You've successfully completed the first exercise! Select **Next >** to go to the next exercise.






