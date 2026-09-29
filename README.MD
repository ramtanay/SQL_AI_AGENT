# 🤖 AI Employee Database Agent

An AI-powered employee database assistant that allows users to interact with a SQLite employee database using **natural language**.

Instead of writing SQL queries manually, users can simply ask questions such as:

> "Show all employees from the IT department."

> "Who has the highest salary?"

> "Add Rahul as an IT employee with a salary of 55000."

> "Find employees earning between 40000 and 60000."

The AI agent interprets the user's request and uses **OpenAI Function Calling** to select and execute the appropriate Python database operation.

---

## 🚀 Features

### 🤖 Natural Language Database Interaction

Interact with the employee database using normal conversational language instead of manually writing SQL.

Examples:

* Show all employees
* Find an employee by ID
* Search employees by name
* Search employees by department
* Search employees by city
* Find employees within a salary range
* Count total employees
* Find the highest-paid employee
* Find employees in a department above a particular salary
* Create a new employee
* Update an employee
* Delete an employee

The available operations are exposed to the OpenAI model as structured function tools.

---

### 🧠 OpenAI Function Calling

The project uses OpenAI's function/tool calling mechanism to connect the language model with Python functions.

The AI does **not directly execute SQL**.

Instead, it follows this architecture:

```text
User
  ↓
Natural Language Request
  ↓
OpenAI AI Agent
  ↓
Function Calling
  ↓
Python Database Function
  ↓
SQLite Database
  ↓
Function Result
  ↓
OpenAI AI Agent
  ↓
Natural Language Response
  ↓
User
```

This separation allows the AI model to understand the user's intent while Python handles the actual database operations.

---

## 🗄️ SQLite Employee Database

The application uses SQLite as its database.

The database contains an `employees` table with the following fields:

| Column       | Type    | Description         |
| ------------ | ------- | ------------------- |
| `id`         | INTEGER | Unique employee ID  |
| `name`       | TEXT    | Employee name       |
| `email`      | TEXT    | Employee email      |
| `department` | TEXT    | Employee department |
| `salary`     | REAL    | Employee salary     |
| `city`       | TEXT    | Employee city       |

The employee email is required to be unique.

---

## 🛠️ Available AI Tools

The agent currently has access to the following database tools:

| Tool                       | Purpose                                                 |
| -------------------------- | ------------------------------------------------------- |
| `create_employee`          | Create a new employee                                   |
| `get_employee`             | Retrieve an employee using ID                           |
| `get_all_employees`        | Retrieve all employees                                  |
| `update_employee`          | Update employee information                             |
| `delete_employee`          | Delete an employee                                      |
| `search_by_name`           | Search employees using partial name                     |
| `search_by_department`     | Search by department                                    |
| `search_by_city`           | Search by city                                          |
| `search_by_salary`         | Search using a salary range                             |
| `count_employees`          | Count total employees                                   |
| `highest_salary`           | Find the highest-paid employee                          |
| `search_department_salary` | Find employees in a department above a salary threshold |

These tools are defined as structured OpenAI function tools and mapped to their corresponding Python functions.

---

## 🖥️ Streamlit Interface

The project provides a Streamlit web interface with three sections:

### 🤖 AI Agent

The main interface allows users to enter natural-language requests and execute them through the AI agent.

Example requests include:

```text
Show all employees.

How many employees are there?

Find employees from IT.

Find employees from Kolkata.

Who has the highest salary?

Find employees earning between 40000 and 60000.

Add Rahul as an IT employee with salary 55000.

Update employee 1 salary to 65000.

Delete employee 3.
```

The application displays the AI-generated response after the request is processed.

### 📊 Employees

Displays the current employee database in a Streamlit dataframe and provides a refresh option.

### ℹ️ About

Provides information about the application and its technology stack.

---

## 📁 Project Structure

```text
sql-ai-agent/
│
├── .devcontainer/
│   └── ...
│
├── .streamlit/
│   └── ...
│
├── .env
├── .gitignore
├── LICENSE
├── README.md
├── requirements.txt
│
├── agent.py
├── app.py
├── database.py
├── employees.db
│
├── test_agent.py
├── test_database.py
│
└── __pycache__/
```

### File Responsibilities

#### `agent.py`

Contains the AI agent logic.

Responsibilities include:

* Loading the OpenAI API key
* Initializing the OpenAI client
* Defining function-calling tools
* Mapping tool names to Python functions
* Sending user requests to the OpenAI model
* Processing function calls
* Executing database operations
* Returning the final natural-language response

The agent uses a loop that continues processing tool calls until the model produces a final response.

---

#### `database.py`

Contains the SQLite database layer.

Responsibilities include:

* Creating the database connection
* Creating the employee table
* CRUD operations
* Employee searches
* Salary-based queries
* Employee counting
* Finding the highest salary

The database connection uses SQLite's `sqlite3` module and returns rows using `sqlite3.Row`.

---

#### `app.py`

Contains the Streamlit frontend.

Responsibilities include:

* Initializing the database
* Creating the Streamlit interface
* Receiving user requests
* Calling the AI agent
* Displaying AI responses
* Displaying employee records

---

#### `employees.db`

SQLite database file containing employee records.

The database is automatically initialized through:

```python
create_table()
```

when the application starts.

---

#### `test_agent.py`

Provides a simple command-line interface for testing the AI agent.

Run it and enter natural-language requests:

```text
You: Show all employees

AI: ...
```

Type:

```text
exit
```

to stop the program.

---

#### `test_database.py`

Contains basic database testing examples, including:

* Creating employees
* Retrieving employees
* Searching by department
* Searching by city
* Counting employees
* Finding the highest salary

---

# ⚙️ Tech Stack

| Technology        | Purpose                         |
| ----------------- | ------------------------------- |
| 🐍 Python         | Application development         |
| 🧠 OpenAI API     | AI agent and function calling   |
| 🗃️ SQLite        | Employee database               |
| 🎨 Streamlit      | Web interface                   |
| 🔐 python-dotenv  | Environment variable management |
| 🧪 Python testing | Database and agent testing      |

---

# 🔄 How the Agent Works

Suppose the user asks:

```text
Who has the highest salary?
```

### Step 1 — User Request

The request is entered into the Streamlit interface.

```text
Who has the highest salary?
```

### Step 2 — OpenAI Agent

The request is sent to the OpenAI model.

The agent has access to a collection of predefined database tools.

### Step 3 — Tool Selection

The model determines that the appropriate tool is:

```text
highest_salary()
```

### Step 4 — Python Execution

The Python application executes the database function.

Internally, the function retrieves the employee with the maximum salary from SQLite.

```sql
SELECT *
FROM employees
ORDER BY salary DESC
LIMIT 1
```

### Step 5 — Result Returned to Agent

The database result is passed back to the OpenAI model as a function result.

### Step 6 — Natural Language Response

The model converts the result into a human-readable response.

```text
The employee with the highest salary is ...
```

This tool-call → result → final-response loop is implemented in `ask_agent()`.

---

# 🔐 Environment Variables

Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your_openai_api_key_here
```

The application reads the API key from Streamlit secrets or the environment.

### ⚠️ Important

Never commit your real API key to GitHub.

Make sure `.env` is included in `.gitignore`.

Example:

```gitignore
.env
venv/
__pycache__/
*.pyc
```

---

# 📦 Installation

## 1. Clone the Repository

```bash
git clone <your-repository-url>
cd sql-ai-agent
```

## 2. Create a Virtual Environment

### Windows

```bash
python -m venv venv
```

Activate it:

```bash
venv\Scripts\activate
```

### Linux / macOS

```bash
python3 -m venv venv
source venv/bin/activate
```

---

## 3. Install Dependencies

```bash
pip install -r requirements.txt
```

---

## 4. Configure API Key

Create:

```text
.env
```

and add:

```env
OPENAI_API_KEY=your_openai_api_key
```

---

# ▶️ Running the Application

Start the Streamlit application:

```bash
streamlit run app.py
```

After starting, Streamlit will provide a local URL similar to:

```text
http://localhost:8501
```

Open the URL in your browser.

---

# 💬 Example Queries

Once the application is running, try:

### Read Operations

```text
Show all employees.
```

```text
Find employee with ID 3.
```

```text
Find employees from IT.
```

```text
Find employees from Kolkata.
```

```text
How many employees are there?
```

```text
Who has the highest salary?
```

---

### Search Operations

```text
Find employees named Rahul.
```

```text
Find employees earning between 40000 and 60000.
```

```text
Find IT employees earning at least 50000.
```

---

### Create Operation

```text
Add Rahul as an IT employee with salary 55000.
```

The agent can collect the required employee fields and call the `create_employee` function.

---

### Update Operation

```text
Update employee 1 salary to 65000.
```

---

### Delete Operation

```text
Delete employee 3.
```

The agent's instructions require clear employee identification before destructive operations such as deletion.

---

# 🧪 Testing

## Test the Database Layer

Run:

```bash
python test_database.py
```

The test script demonstrates employee creation and several database operations including department search, city search, employee counting, and highest-salary lookup.

---

## Test the AI Agent

Run:

```bash
python test_agent.py
```

Then enter requests interactively:

```text
You: Show all employees

AI: ...
```

To exit:

```text
exit
```

---

# 🧩 Database Operations

The database layer implements standard CRUD operations.

### Create

```python
create_employee(...)
```

### Read

```python
get_employee(...)
get_all_employees(...)
```

### Update

```python
update_employee(...)
```

### Delete

```python
delete_employee(...)
```

### Search

```python
search_by_name(...)
search_by_department(...)
search_by_city(...)
search_by_salary(...)
search_department_salary(...)
```

### Analytics

```python
count_employees(...)
highest_salary(...)
```

The underlying implementation uses parameterized SQLite queries, helping keep user-provided values separate from SQL statements.

---

# 🏗️ Architecture

```text
                  ┌─────────────────────┐
                  │       User          │
                  └──────────┬──────────┘
                             │
                             ▼
                  ┌─────────────────────┐
                  │     Streamlit UI    │
                  │       app.py        │
                  └──────────┬──────────┘
                             │
                             ▼
                  ┌─────────────────────┐
                  │    AI Agent         │
                  │      agent.py      │
                  └──────────┬──────────┘
                             │
                  OpenAI Function Calling
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
        create_employee  search...    highest_salary
              │              │              │
              └──────────────┼──────────────┘
                             ▼
                  ┌─────────────────────┐
                  │   database.py       │
                  │   Python DB Layer   │
                  └──────────┬──────────┘
                             │
                             ▼
                  ┌─────────────────────┐
                  │     SQLite          │
                  │   employees.db      │
                  └─────────────────────┘
```

---

# 🎯 Project Goals

This project demonstrates how an AI model can be connected to a real application through **tool/function calling**.

Instead of allowing the LLM to directly manipulate the database, the application exposes controlled Python functions such as:

```python
create_employee()
get_employee()
update_employee()
delete_employee()
search_by_department()
highest_salary()
```

The model decides **which operation is required**, while the application controls **how that operation is executed**.

This is a useful pattern for building AI agents that interact with external systems.

---

# 🔮 Future Improvements

Potential improvements include:

* [ ] Add authentication and user authorization
* [ ] Add confirmation before destructive operations
* [ ] Add conversation history
* [ ] Add employee editing through the UI
* [ ] Add pagination for large employee datasets
* [ ] Add more advanced analytics
* [ ] Add charts and dashboards
* [ ] Add logging and monitoring
* [ ] Add comprehensive automated tests
* [ ] Add database migrations
* [ ] Add PostgreSQL/MySQL support
* [ ] Add Docker deployment
* [ ] Add deployment configuration for Streamlit Cloud
* [ ] Add role-based database permissions
* [ ] Add more complex natural-language queries

---

# 🔒 Security Considerations

For production use, additional security controls should be implemented.

Recommended improvements:

1. Keep API keys outside the source code.
2. Never commit `.env` files.
3. Validate tool arguments.
4. Add authorization for database modifications.
5. Require explicit confirmation before deletion.
6. Add audit logs for database changes.
7. Restrict the operations available to the AI agent.
8. Use a production-grade database for larger applications.

---

# 📚 What This Project Demonstrates

This project provides hands-on experience with:

* Python backend development
* SQLite database management
* CRUD operations
* SQL queries
* OpenAI API integration
* Function calling / tool calling
* AI agent architecture
* LLM-to-database interaction
* Streamlit application development
* Environment variable management
* Basic testing
* Separation of AI logic and database logic

---

# 👨‍💻 Author

**Ramtanay Chakraborty**

---

# 📄 License

This project is licensed under the terms specified in the repository's `LICENSE` file.
