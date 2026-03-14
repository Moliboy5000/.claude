# Global CLAUDE.md for all projects

## You typically have access to these additional command-line tools (otherwise you can ask me for them)
- **AWS CLI:** USE VERY CAREFULLY, you are free to read whatever you want but always double check any changes you want to make.
- **Github CLI** Feel free to use in addition to the MCP server if you wish.

## Code Style and Conventions
- **General:** Write clean, modular, and self-documenting code. Favor readability over cleverness. Follow MVC for frontend projects, separate business logic and UI.
- **Naming Conventions:** - Always follow naming conventions in the project. If the project is fresh use the standard for the language/framework. 
- **Error Handling:** Fail fast and gracefully. Do not swallow exceptions. Always log errors with adequate context.
- **Documentation:** Include concise docstrings for all public functions, classes, and complex logic blocks. 

## Infrastructure & DevOps Rules (If Applicable)
- **Security First:** Never hardcode secrets. Always use environment variables or a secrets manager (e.g., AWS Secrets Manager, Vault). Apply the Principle of Least Privilege for all IAM roles and policies.
- **Idempotency:** Ensure all scripts, CI/CD pipelines, and IaC are idempotent. Running them twice should not alter the state if no source changes occurred.
- **Tags & Labels:** All cloud resources and containers must include standard tags (e.g., `Environment`, `Project`, `Owner`).
- **Immutability:** Treat infrastructure and containers as ephemeral. Do not patch running instances; replace them through the deployment pipeline.

## AI Interaction Guidelines
- **Be Detailed and comprehensive:** Always outline exactly what you are doing and what you are thinking, always review arthicectural choices with me.
- **Think Before Executing:** When asked to write or modify code, outline the proposed changes before writing the full implementation.
- **Safety First:** If a requested command (especially a deletion or deployment command) seems destructive or unsafe for a production environment, pause and ask for explicit confirmation before proceeding.
- **Testing:** If you generate new logic, always verify (using relevant tools) that it works as intended. You do not necessarily have to write unit tests, but you should double check all new logic.
- **Work within scope:** Work within the scope of the prompt given. If you catch something outside of scope consult with me first. If you find a bug/enhancement spin up the github-issue-reporter agent that will post an issue to github.
- **Do not commit unless approved:** Do not stage or commit changes unless I allow you to. 
