---
{"dg-metatags":{"description":"AI Prompt to generate Readme file for Java Script App","title":"AI Prompt to generate Readme file for Java Script App","og:title":"AI Prompt to generate Readme file for Java Script App","og:type":"article","og:article:author":"Hemant Bothra","og:article:tag":["AI-Prompts","chatGPT","Gemini","Claude","Cusror"],"og:article:section":"Technology"},"dg-publish":true,"comments":true,"tags":["AI","Prompt","PromptEngineering","JavaScript","Node","README","TypeScript"],"permalink":"/ai-prompts/java-script-readme-generator/","metatags":{"description":"AI Prompt to generate Readme file for Java Script App","title":"AI Prompt to generate Readme file for Java Script App","og:title":"AI Prompt to generate Readme file for Java Script App","og:type":"article","og:article:author":"Hemant Bothra","og:article:tag":["AI-Prompts","chatGPT","Gemini","Claude","Cusror"],"og:article:section":"Technology"},"dgPassFrontmatter":true}
---

Act as an expert at reading code and generating documentation for TypeScript Node.js services.

Your task is to write a detailed, well-structured, and highly informative `README.md` file for this internal/private TypeScript Node.js service repository.

Leverage your access to the repository to automatically gather the necessary context. This includes:

- Inspecting the `package.json` for dependencies and Node.js versions.
- Analysing relevant configuration files (e.g., `.env.example`, config files, `tsconfig.json`).
- Reading the `CODEOWNERS` file to identify project owners/maintainers.
- Scanning for common Node.js/TypeScript patterns, API endpoints, and potential KEDA configurations.
- Considering any existing `README.md` file in the repository. Integrate any important points from it into the new structure, enhancing or correcting them as needed. Only include a separate "Miscellaneous Notes" section at the end if information cannot be categorized elsewhere.

---

## README Structure

### 1. Service Overview

- **Project Name:** Automatically determine the project name.
- **Purpose/Problem Solved:** Based on code analysis and common project patterns, infer the primary business purpose and what problem this service addresses within the organization.
- **Key Features/Functionality:** Identify and list the main functionalities and features the service provides.

### 2. Table of Contents

Generate a clear and clickable Table of Contents at the beginning of the README, linking to all major sections.

### 3. Technologies & Service Dependencies

- **Core Technologies:** List the main programming languages (specifically Node.js version), frameworks (Express, Fastify, NestJS, etc.), TypeScript version, and key libraries identified from the `package.json` (e.g., npm/yarn/pnpm, common middleware, ORMs like Prisma/TypeORM).
- **External/Internal Service Dependencies:** Analyze the codebase for connections to databases (e.g., PostgreSQL, MySQL, MongoDB), message queues (e.g., Kafka, RabbitMQ), other internal microservices, or external APIs. Clearly list these dependencies.
- **Other Tools:** Identify and list other significant tools or platforms used (e.g., Docker, Kubernetes).

### 4. Setup & Local Development

Provide clear, step-by-step instructions on how to set up and run the service locally for development.

- **Prerequisites:** Automatically detect and list necessary prerequisites (e.g., specific Node.js version, npm/yarn/pnpm).
- **Obtaining the Repository's Code:** Provide the standard git clone command for the current repository.
- **Initial Setup/Build:** Provide the exact commands to install dependencies and build the project (e.g., `npm install`, `yarn install`, `npm run build`, `yarn build`).
- **Configuration:** Explain how to manage environment variables or configuration files for local development. Suggest a `.env.local` or `.env.development` approach if common.
- **Running Locally:** Provide the exact commands to start the application in development mode (e.g., `npm run dev`, `yarn dev`, `npm start`).
- **Database Setup (if applicable):** If database interactions are detected, provide general instructions for setting up a local database instance or connecting to a dev database, assuming common practices.

### 5. KEDA Scaling Logic

Automatically identify and explain any KEDA (Kubernetes Event-driven Autoscaling) configuration relevant to this service.

If found, detail:

- **Scaler Type(s):** (e.g., Kafka, SQS, Prometheus, HTTP).
- **Trigger Details:** Describe the specific trigger configuration (e.g., scales based on Kafka topic lag, HTTP requests).
- **Min/Max Replicas:** The minimum and maximum replica counts.
- **Thresholds:** Any specific scaling thresholds (e.g., target average lag).
- **Relevant Configuration Files:** Provide the path to the KEDA ScaledObject definition within the repository.

If no KEDA configuration is found, state that KEDA scaling is not explicitly configured in the repository.

### 6. Monitoring

**a. ArgoCD:**

- Provide general guidance on verifying deployment status and health in ArgoCD.
- Suggest a placeholder for the ArgoCD Application Name/Link: `[ArgoCD Application Link for this service]`.

**b. Grafana:**

- Suggest common categories of Grafana Dashboards relevant for a Node.js/TypeScript service (e.g., "Service Health," "Node.js Metrics," "API Performance," "Database Performance").
- Provide placeholders for specific Grafana Dashboard Links: `[Service Health Dashboard Link]`, `[Node.js Metrics Dashboard Link]`.
- List important metrics to watch based on common Node.js monitoring practices and detected dependencies (e.g., HTTP request latency, error rate, memory usage, event loop lag, database connection pool, Kafka consumer lag).
- Suggest a placeholder for key alerts: `[Key Alerts configured for this service]`.  

### 7. Service Documentation

**API Endpoints:**

- Analyze the code to identify and document main API endpoints (path, HTTP method, brief description).
- Provide generic examples of request bodies and sample responses based on common Express/Fastify/NestJS REST API patterns.
- Mention any detected authentication/authorization mechanisms.

**Swagger/OpenAPI Documentation:**

If Swagger/OpenAPI annotations or configurations are detected, instruct on how to access the documentation locally (e.g., `http://localhost:3000/api-docs` or `http://localhost:3000/swagger`). Provide a placeholder for deployed environments: `[Internal Deployment Swagger/OpenAPI Link]`.

**Other Internal Documentation:**

Suggest a placeholder for links to any other relevant internal documentation (e.g., `[Confluence Design Document Link]`).

### 8. Project Structure & Key Files

- Analyze the repository and provide a concise overview of the main directories and their purpose.
- Highlight key configuration files, important source code directories, and entry points relevant to a TypeScript Node.js application.

### 9. Testing

- Explain how to run tests for the project locally. Automatically detect the relevant commands (e.g., `npm test`, `yarn test`, `npm run test:unit`, `npm run test:integration`).
- Mention any testing frameworks detected (e.g., Jest, Mocha, Vitest, Supertest).

### 10. Owners & Support

- **Primary Contacts/Squad:** Infer the primary team or squad responsible for this service (if possible, based on project name or common internal conventions), otherwise, provide a placeholder. Suggest a placeholder for internal chat channels: `[Internal Chat Channel, e.g., Slack channel #project-name]`.
- **Codeowners/Maintainers:** Read the `CODEOWNERS` file in the repository. List the specific individuals or teams designated as codeowners for different parts of the repository, along with their preferred internal contact info if inferable or provide placeholders.
- **Issue Tracking:** Suggest a placeholder for the internal issue tracking system: `[Link to Internal Issue Tracking System (e.g., Jira)]`.

### 11. Known Issues / Caveats (Optional but Recommended)

Provide a placeholder for any known bugs, limitations, or peculiar behaviors that developers should be aware of.

### 12. Diagrams / Architecture (Optional)

Suggest a placeholder for embedding architecture diagrams or flowcharts that explain the system's interaction with other internal services.

---
**Formatting Notes:**

- Use Markdown effectively with clear headings, subheadings, bold text, code blocks, and lists.
- Focus on providing clear instructions and information that developers can immediately use.
- Avoid overly verbose explanations.
- Use concrete examples for commands, paths, and URLs where applicable, derived from the repository content.
---