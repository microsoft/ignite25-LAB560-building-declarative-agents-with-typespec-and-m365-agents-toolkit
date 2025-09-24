# Exercise 3: Diagnosing and Debugging Agent

You can enable developer mode in a chat to allow you as a developer to understand how well the agent understands the tasks, ensure it calls your services appropriately, identify areas that need fine-tuning, detect performance issues, and generally help you track and analyse its interactions.

##  Agent debugging in the chat

- Copy and paste the following line into the chat with your agent to enable debugging mode.

    ```
    -developer on
    ```
The agent will respond with a success message if everything went well - `Successfully enabled developer mode.`

- Next to test, send a prompt to interact with the agent like the one below.

   `Find out what Karin is working on.`

- You will get a response with information from the repair service but also get the **Agent debug info** card along with the response.
- Expand the **Agent debug info** card to view all the details.
- You will be able to see: 
    -	Agent information 1️⃣
    -	Capabilities of the agent 2️⃣
    -	Actions and what function were selected 3️⃣
    -	Executed action info with detailed information about the request, latency, response data etc. 4️⃣

![agent-info](https://github.com/user-attachments/assets/b135f3b0-50f1-47a1-b608-a5a1b27b806e)

- Try expanding the **Executed Actions** and you will see the request url, parameters passed, request header, response, latency etc. 


☑️ You've successfully completed the all exercises! Select **Next >** to go see the finish line 😎