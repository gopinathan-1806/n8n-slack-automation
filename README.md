# 🍳 Slack Recipe AI Assistant

An n8n-based AI automation that works directly inside Slack.

Users type a recipe name in a Slack channel, and the workflow uses an
LLM to generate the required ingredients, approximate quantities, and
step-by-step cooking instructions. The response is posted back to the
same Slack channel.

## Architecture

``` text
User → Slack → Slack Trigger → Edit Fields → Basic LLM Chain
                                      │
                                      └── OpenAI Chat Model
                                             ↓
                                            Code
                                             ↓
                                  Slack — Send Message
                                             ↓
                                      Same Slack Channel
```

## Tech Stack

-   **n8n** --- Workflow automation
-   **Slack** --- User interface and messaging
-   **OpenAI / LLM** --- Recipe understanding and response generation
-   **JavaScript** --- Response formatting
-   **IBM Cloud VM** --- Self-hosted n8n environment

## Workflow

### 1. Slack Trigger

Starts the workflow when a new message is posted in the configured Slack
channel.

Example:

``` text
Chicken Biryani
```

The trigger provides the message text and channel information to n8n.

### 2. Edit Fields

Extracts the recipe name into a clean field:

``` json
{
  "recipe": "Chicken Biryani"
}
```

### 3. Basic LLM Chain

The recipe is sent to the LLM with instructions to:

-   List the main ingredients and spices.
-   Provide approximate quantities.
-   Keep the response practical and concise.
-   Provide clear, numbered cooking instructions.
-   Include relevant cooking times or temperatures where appropriate.
-   Ask the user to rephrase if the recipe name is unclear.

### 4. Code Node

The Code node prepares the LLM response for Slack.

It:

-   Extracts the generated response.
-   Converts escaped `\n` sequences into proper line breaks.
-   Cleans escaped quotation marks.
-   Preserves the original Slack channel ID.

### 5. Slack Send Message

The final Slack node uses the incoming channel ID and posts the
generated answer back to the same channel.

This avoids hardcoding a response channel.

## Example

**User**

``` text
Paneer Butter Masala
```

**AI Assistant**

``` text
Ingredients:
- Paneer — 250 g
- Butter — 2 tbsp
- Tomatoes — 3 medium
- Onion — 1 medium
- Cream — ¼ cup
- Ginger-garlic paste — 1 tbsp
- Kashmiri chili powder — 1 tsp
- Garam masala — ½ tsp
- Salt — as needed

Cooking Instructions:
1. Blend the tomatoes and onion into a smooth paste.
2. Heat butter and cook the ginger-garlic paste.
3. Add the tomato mixture and spices and cook until the oil separates.
4. Add paneer and a little water and simmer for 5–7 minutes.
5. Stir in the cream and garam masala.
6. Simmer briefly and serve hot.
```

## n8n Workflow

``` text
Slack Trigger
      ↓
Edit Fields
      ↓
Basic LLM Chain
      └── OpenAI Chat Model
      ↓
Code
      ↓
Slack — Send Message
```

  Node                Responsibility
  ------------------- -----------------------------------------------
  Slack Trigger       Receives recipe requests
  Edit Fields         Extracts the recipe name
  Basic LLM Chain     Generates ingredients and instructions
  OpenAI Chat Model   Provides the LLM
  Code                Formats the response and preserves channel ID
  Slack               Sends the response back to Slack

## Slack App Configuration

The Slack app uses Event Subscriptions to deliver channel messages to
the n8n Slack Trigger.

Required configuration includes:

-   Event Subscriptions enabled
-   n8n webhook URL configured as the Slack Request URL
-   Channel message bot event enabled
-   Bot added to the target Slack channel
-   Required bot OAuth scopes configured
-   Slack Signing Secret configured in n8n

## Security

Never commit secrets to Git.

Do not commit:

``` text
.env
OpenAI API keys
Slack bot tokens
Slack signing secrets
```

Use n8n credentials for secrets instead of hardcoding them in workflow
nodes.

## Testing

Send a recipe name to the configured Slack channel:

``` text
Masala Dosa
```

Expected flow:

``` text
Slack message
     ↓
n8n receives recipe
     ↓
LLM generates response
     ↓
Code formats response
     ↓
Slack receives answer
```

Also test an unclear input such as:

``` text
xyz food
```

The assistant should ask the user to rephrase instead of confidently
inventing a recipe.

## Key Learning

This project demonstrates a practical event-driven AI automation
pattern:

``` text
Event → Data Extraction → LLM → Response Formatting → Action
```

It provides hands-on experience with:

-   n8n workflow automation
-   Slack integrations
-   Slack Event Subscriptions
-   Webhooks
-   LLM integration
-   Dynamic data mapping
-   JavaScript transformation
-   API credentials
-   End-to-end AI automation

## Future Improvements

-   Conversation memory
-   Dietary preference support
-   Vegetarian / vegan filtering
-   Serving-size adjustment
-   Ingredient substitutions
-   Shopping-list generation
-   Nutritional information
-   Multi-language responses
-   Recipe verification using web sources
-   Interactive Slack buttons
-   Recipe saving and retrieval
-   Error handling and retries
-   Workflow observability

## Project Outcome

Users can interact with the AI recipe assistant entirely through Slack.

``` text
Type recipe in Slack
        ↓
AI processes request
        ↓
Ingredients + quantities
        ↓
Cooking instructions
        ↓
Answer appears in Slack
```

**Built with n8n + Slack + OpenAI.**

<img width="1536" height="1024" alt="slack-flowchart-diagram" src="https://github.com/user-attachments/assets/f375511e-7dc8-403f-aa57-ba7dc5420a9f" />

## Use the JSON below to create the workflow and import it into n8n.

https://github.com/gopinathan-1806/n8n-slack-automation/blob/main/Slack%20Automation.json

## Realtime demo

<img width="1449" height="669" alt="Screenshot 2026-09-10 at 9 06 13 PM" src="https://github.com/user-attachments/assets/fdedc83f-e82c-4970-9997-28fd48c54948" />

