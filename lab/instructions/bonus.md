# Bonus step - Enhance your agent's capability

## Step 1: Add code interpreter capability to your agent

Declarative Agents can be extended to have many capabilities like OneDriveAndSharePoint, WebSearch, CodeInterpreter etc
Next, you will enhance the agent by adding code interpreter capability to it.

- To do this, open the `main.tsp` file and locate the `RepairServiceAgent` namespace which is where you define the agent behaviour.

- Inside the namespace `RepairServiceAgent`, insert the following snippet above `@service` to define a new capability that enables the agent to interpret and execute code.

```typespec
op codeInterpreter is AgentCapabilities.CodeInterpreter;
```

>[!TIP]
> When you add above codeinterpreter operation, paste it inside the outer `RepairServiceAgent` namespace which defines the agent's behaviour including the capabilities and not the `RepairServiceActions` namespace which defines the agent's actions.

Since the agent now supports additional capability, update the instructions accordingly to reflect this enhancement.

- In the same `main.tsp` file, update instructions definition to have additional directives for the agent.

```typespec
@instructions("""
  ## Purpose
You will assist the user in finding car repair records based on the information provided by the user. When asked to display a report, you will use the code interpreter to generate a report based on the data you have.

  ## Guidelines
- You are a repair service agent.
- You can use the actions to create, update, and delete repairs.
- When creating a repair item, if the user did not provide a description or date , use title as description and put todays date in format YYYY-MM-DD
- Do not use any technical jargon or complex terms.
- You can use the code interpreter to generate reports based on the data you have.
- Do not show any code or technical details to the user.

""")

```

### Step 2: Test your agent's new capabiility

Next, you will test the new analytical capability of your agent. You will need to reprovision the agent. Follow below steps:

- Save all changes, select the Agents toolkit extension icon <img width="24" alt="m365atk-icon" src="https://github.com/user-attachments/assets/b5a5a093-2344-4276-b7e7-82553ee73199" />, to open the activity bar from within your project.
- In the activity bar of the agents toolkit under "LifeCycle" select "Provision". This will reprovision the agent.
- Open a new chat by selecting the **New chat** button on the top right corner of your agent.
- Next, copy the prompt below and paste it to the message box and hit enter to send it.

`Classify repair items based on title into three distinct categories: Routine Maintenance, Critical, and Low Priority. Then, generate a pie chart displaying the percentage representation of each category. Use unique colours for each group and incorporate tooltips to show the precise values for each segment.`

You should get some response similar to below screen. It may vary sometimes. 
![reposne with chart using code interpreter](https://github.com/user-attachments/assets/8ccc7758-28ec-42ff-96fd-2341cad6c9ed)


☑️ Great job completing all the exercises! Click **Next >** to finish up this lab.
