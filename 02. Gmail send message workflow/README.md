# 🤖 Automated Code Summarizer & Commenter — n8n Workflow

An automated **n8n workflow** that processes incoming source code via Gmail, uses **Google Gemini** models to generate high-level summaries and inline comments in parallel, formats the results, and emails the completed documentation back to you.

---

## 📌 Features

* 📧 **Automated Email Trigger**
  Monitors Gmail for incoming emails containing source code.

* 🧠 **Parallel AI Processing**

  * **Code Summarization:** Analyzes the code structure and generates a high-level summary using Google Gemini.
  * **Comment Generation:** Adds useful inline documentation and comments to the source code.

* 🔀 **Data Aggregation**
  Merges the outputs from the parallel AI processing branches into a single payload.

* ✨ **AI-Powered Formatting**
  Uses a secondary Gemini model to organize and format the generated documentation into a clean, readable response.

* 📤 **Automated Response**
  Sends the final summary and commented code back to the recipient via Gmail.

---

## ⚡ Workflow Architecture

```text
                         ┌──> [ Comments Addition Node (Gemini) ] ──┐
                         │                                          │
[ Gmail Trigger ] ───────┤                                          ├──> [ Merge ]
                         │                                          │
                         └──> [ Code Summary Node (Gemini) ] ───────┘
                                                                      │
                                                                      ▼
                                                               [ Aggregate ]
                                                                      │
                                                                      ▼
                                                             [ Edit Fields ]
                                                                      │
                                                                      ▼
                                                          [ Gemini Formatter ]
                                                                      │
                                                                      ▼
                                                             [ Send Email ]
```

---

## ⚙️ Node Configuration

| Step   | Node                   | Node Type   | Description                                                             |
| ------ | ---------------------- | ----------- | ----------------------------------------------------------------------- |
| **1**  | Gmail Trigger          | Trigger     | Listens for new emails containing source code.                          |
| **2a** | Comments Addition Node | Chain / LLM | Uses the Gemini Comment Model to generate inline code comments.         |
| **2b** | Code Summary           | Chain / LLM | Uses the Gemini Summary Model to generate a high-level code summary.    |
| **3**  | Merge                  | Transform   | Combines the outputs from both parallel branches using **Append** mode. |
| **4**  | Aggregate              | Transform   | Groups the summary and commented code into a single item.               |
| **5**  | Edit Fields            | Transform   | Prepares the aggregated data for the formatting model.                  |
| **6**  | Basic LLM Chain        | LLM Chain   | Uses the Gemini Formatter Model to structure the final email content.   |
| **7**  | Send a Message         | Gmail       | Sends the formatted documentation back to the recipient.                |

---

## 🔄 How It Works

The workflow follows these steps:

1. **Receive Code**

   * A new email containing source code arrives in the connected Gmail inbox.

2. **Trigger Workflow**

   * The Gmail Trigger detects the incoming message and starts the n8n workflow.

3. **Process Code in Parallel**

   * The code is sent to two Gemini-powered branches:

     * One generates a **high-level summary**.
     * The other generates **inline comments**.

4. **Merge Results**

   * The outputs from both AI branches are merged together.

5. **Aggregate Data**

   * The generated summary and commented code are grouped into a single payload.

6. **Format Documentation**

   * A separate Gemini model formats the combined output into a clean and readable structure.

7. **Send Documentation**

   * The final result is sent back via Gmail.

---

## 🚀 Setup & Installation

### Prerequisites

Before importing the workflow, make sure you have:

* An active **n8n** instance

  * n8n Cloud, or
  * Self-hosted n8n
* A **Google Gemini API Key**
* A **Gmail account** connected to n8n using OAuth2
* The required n8n workflow JSON file

---

### 1. Import the Workflow

Import the workflow JSON into your n8n workspace.

In n8n:

```text
Workflows → Import from File
```

Select the provided workflow JSON file.

---

### 2. Configure Gmail Credentials

Connect your Gmail OAuth2 credentials to:

* **Gmail Trigger**
* **Send a Message**

Make sure the connected Gmail account has the required permissions to read incoming emails and send outgoing messages.

---

### 3. Configure Gemini Models

Connect your Google Gemini credentials/API key to the three Gemini model nodes:

```text
Gemini Comment Model
Google Gemini Summary Model
Gemini Formatter Model
```

Each model is responsible for a different stage of the workflow:

| Model                           | Purpose                                        |
| ------------------------------- | ---------------------------------------------- |
| **Gemini Comment Model**        | Generates inline comments for the source code  |
| **Google Gemini Summary Model** | Generates a high-level explanation of the code |
| **Gemini Formatter Model**      | Formats the final response for email delivery  |

---

### 4. Activate the Workflow

After configuring the credentials:

1. Save the workflow.
2. Test the workflow with a sample email.
3. Verify that the summary and commented code are generated correctly.
4. Set the workflow to **Active**.

---

## 📩 Example Input

Send an email containing source code to the Gmail account connected to the workflow.

For example:

```python
def calculate_total(items):
    total = 0

    for item in items:
        total += item["price"]

    return total
```

The workflow automatically processes the code and generates both a summary and commented version.

---

## 📤 Example Output

The resulting email can contain:

### Code Summary

> This function calculates the total price of a collection of items by iterating through each item and adding its `price` value to an accumulator.

### Commented Code

```python
def calculate_total(items):
    # Initialize the total price.
    total = 0

    # Iterate through each item and add its price.
    for item in items:
        total += item["price"]

    # Return the calculated total.
    return total
```

The final formatting is handled automatically by the Gemini Formatter Model.

---

## 🧩 Workflow Components

```text
Gmail
  │
  ▼
Gmail Trigger
  │
  ├──────────────────────┐
  ▼                      ▼
Gemini Comments      Gemini Summary
  │                      │
  └──────────┬───────────┘
             ▼
           Merge
             │
             ▼
         Aggregate
             │
             ▼
        Edit Fields
             │
             ▼
     Gemini Formatter
             │
             ▼
       Send Email
```

---

## 🔐 Credentials & Security

The workflow requires access to Gmail and Google Gemini.

For security:

* Do **not** commit API keys to GitHub.
* Use n8n's built-in credential management.
* Do not store OAuth tokens directly inside workflow JSON files.
* Add sensitive configuration files to `.gitignore`.
* Use environment variables where appropriate for self-hosted deployments.

Example `.gitignore`:

```gitignore
.env
*.secret
credentials.json
```

---

## 🛠️ Customization

You can customize the workflow to suit your requirements.

### Change the AI Models

Replace the Gemini models with other supported LLMs if desired.

### Customize the Summary

Modify the summary prompt to request specific information, such as:

* Overall purpose
* Functions and classes
* Dependencies
* Inputs and outputs
* Potential issues
* Complexity
* Architecture
* Suggested improvements

### Customize Comments

The comment-generation prompt can be adjusted to control:

* Comment style
* Comment verbosity
* Documentation format
* Languag
