# What are Declarative Agents? 

**Declarative Agents** leverage the same scalable infrastructure and platform of Microsoft 365 Copilot, tailored specifically to meet focus on a special area of your needs. They function as subject matter experts in a specific area or business need, allowing you to use the same interface as a standard Microsoft 365 Copilot chat while ensuring they focus exclusively on the specific task at hand.

## Anatomy of a Declarative Agent

As you build more agents for Copilot, you’ll notice that the final output is a set of a few files bundled into a zip file what we call an app package, that you'll install and use. So it's important you have a basic understanding of what the app package consists of. The app package of a Declarative Agent is similar to a Teams app if you have built one before with additonal elements. See the table to see all the core elements. You will also see that the app deployment process is very similar to deploying a teams app.

| File Type                          | Description                                                                                                                                                     | Required |
|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Microsoft 365 App Manifest        | A JSON file (`manifest.json`) that defines the standard Teams app manifest.                                                                                     | Yes      |
| Declarative Agent Manifest        | A JSON file containing the agent's name, instructions, capabilities, conversation starters, and actions (if applicable).                                        | Yes      |
| Plugin Manifest       | A JSON file used to configure your action as an API plugin. Includes authentication, required fields, adaptive card responses, etc. Only needed if actions exist. | No       |
| OpenAPI Spec            | A JSON or YAML file defining your API. Required only if your agent includes actions.                                                                            | No       |

## Capabilities of a Declarative Agent

You can enhance the agent's focus on context and data by not only adding instructions but also specifying the knowledge base it should access. They are called capabilities. Below are the ones supported in a Declarative Agent today: 

- **Copilot Connectors** - let you centralize content on Microsoft 365. By importing external content to Microsoft 365, you not only make it easier to find relevant information, but you also let others in your organization discover new content.
- **OneDrive and SharePoint** - let you provide URLs of files/sites in OneDrive and SharePoint, which will part of the agent's knowledge base.
- **Web search** - let you enable or disable web content as part of the agent's knowledge base. You can also pass around 4 websites URLs as source. 
- **Code interpreter** - enables you to build an agent with capabilities to better solve math problems and, when needed, leverage Python code for complex data analysis or chart generation.
- **GraphicArt** - enables you to build an agent for image or video generation using DALL·E.
- **Email knowledge** - enables you to build an agent to acces a personal or shared mailbox, and optionally, a specific mailbox folder as knowledge.
- **People knowledge** - enables you to build an agent to answer questions about individuals in an organization.
- **Teams messages** - enables you to equip the agent to search through Teams channels, teams, meetings, 1:1 chats, and group chats.
- **Dataverse knowledge** - enables you to add a Dataverse instance as a knowledge source.



☑️  Well done, you've built a solid foundation in the theory of Declarative Agents. Select **Next >** to go to the next chapter.