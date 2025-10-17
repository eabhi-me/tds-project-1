# LLM Code Deployment & Evaluation Project

This project is an automated system designed to build, deploy, and revise web applications based on programmatic requests. It leverages a Large Language Model (LLM) to generate code, interacts with the GitHub API to manage repositories and deployments, and communicates with an external evaluation service.

## Overview

The core of this project is a FastAPI application that listens for incoming tasks. When a task is received, the application performs the following steps:

1.  **Request Verification**: It validates an incoming JSON request containing an application brief and a shared secret.
2.  **Code Generation**: It uses an LLM to generate the necessary HTML, CSS, and JavaScript code based on the provided brief.
3.  **GitHub Automation**: It creates a new public GitHub repository, commits the generated files, adds a LICENSE, and writes this README.
4.  **Deployment**: It enables and deploys the repository to GitHub Pages.
5.  **Notification**: It notifies an external evaluation service with the repository and deployment details.
6.  **Revisions**: It can accept subsequent requests to modify and redeploy the application for a second round of evaluation.

A separate evaluation infrastructure is included in the `evaluations_infra/` directory to simulate the instructor's side of the project, which is responsible for sending tasks and evaluating the deployed applications.

## Project Structure

```
.
├── app/                  # Main FastAPI application source code
│   ├── main.py           # API endpoint and core processing logic
│   ├── llm_generator.py  # LLM interaction and code generation
│   ├── github_utils.py   # GitHub API helper functions
│   └── notify.py         # Notifies the evaluation server
├── evaluations_infra/    # Separate evaluation system for sending tasks
├── .env.example          # Example environment variables file
├── requirements.txt      # Python dependencies
└── vercel.json           # Vercel deployment configuration
```

## Getting Started

Follow these steps to set up and run the application locally.

### 1. Prerequisites

-   Python 3.9+
-   A GitHub account and a [Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) with `repo` and `workflow` scopes.
-   An OpenAI or other LLM provider API key.

### 2. Installation

1.  **Clone the repository:**
    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```

2.  **Create and activate a virtual environment:**
    ```bash
    # For Windows
    python -m venv venv
    .\venv\Scripts\activate

    # For macOS/Linux
    python3 -m venv venv
    source venv/bin/activate
    ```

3.  **Install the required dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Configure environment variables:**
    Copy the example `.env.example` file to a new `.env` file and fill in your credentials.
    ```bash
    cp .env.example .env
    ```
    You will need to set your `GITHUB_USERNAME`, `GITHUB_TOKEN`, `USER_SECRET`, and your LLM API key.

### 3. Running the Application

To start the FastAPI server, run the following command in your terminal:

```bash
uvicorn app.main:app --reload
```

The application will be available at `http://127.0.0.1:8000`.

## Usage

You can send a task to the application by making a POST request to the `/api-endpoint`.

**Example `curl` command:**

```bash
curl -X POST http://127.0.0.1:8000/api-endpoint \
-H "Content-Type: application/json" \
-d '{
  "email": "student@example.com",
  "secret": "your_shared_secret",
  "task": "captcha-solver-abcde",
  "round": 1,
  "nonce": "ab12-...",
  "brief": "Create a captcha solver.",
  "checks": ["Repo has MIT license"],
  "evaluation_url": "http://localhost:8002/notify"
}'
```

## Deployment

This application is configured for easy deployment to [Vercel](https://vercel.com/). Simply connect your GitHub repository to a new Vercel project and configure the same environment variables you set in your `.env` file.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.