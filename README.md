# FoodLens

**A SwiftUI food and recipe application powered by a Python backend and PostgreSQL database.**

FoodLens is a food-focused application designed to help users discover recipes and explore meal ideas. This repository contains the application code and backend integration work used to develop and test the FoodLens database architecture.

## Overview

FoodLens connects a native iOS application with a REST API and a relational database, providing a foundation for retrieving recipe data dynamically rather than relying exclusively on locally stored content.

### Key Components

- **iOS Application:** Built with SwiftUI.
- **REST API:** Built with Python and FastAPI.
- **Database:** PostgreSQL, hosted through Supabase.
- **Database Connectivity:** `psycopg2` for Python-to-PostgreSQL communication.
- **Asynchronous Networking:** Swift `URLSession` for API requests.
- **Environment Configuration:** Environment variables for database connection settings.

## Architecture

```text
FoodLens iOS App
    |
    | SwiftUI / URLSession
    v
Python Backend
    |
    | FastAPI REST API
    v
PostgreSQL Database
    |
    | Supabase
    v
Recipes and Ingredients
```

The iOS application requests recipe data from the backend. The API queries PostgreSQL and returns structured JSON responses that the application can decode and display.

## Features

- **Recipe retrieval:** Fetch recipes from the backend API.
- **Recipe details:** Retrieve an individual recipe using its ID.
- **Ingredient information:** Associate ingredients and quantities with their recipes.
- **Relational database:** Organize recipes, ingredients, and their relationships.
- **Backend integration:** Connect the SwiftUI application to a remotely accessible API.
- **API testing:** Inspect endpoints and responses during development.

## Technology Stack

| Component | Technology |
|---|---|
| iOS development | Swift, SwiftUI |
| Networking | URLSession, Codable |
| Backend | Python, FastAPI |
| API server | Uvicorn |
| Database | PostgreSQL |
| Database hosting | Supabase |
| Database driver | psycopg2 |
| Configuration | python-dotenv |
| Version control | Git, GitHub |

## Project Structure

```text
FoodLens-Testing/
├── FoodLens-main/
│   ├── BackendService.swift
│   ├── BackendTest.swift
│   ├── BackendTestView.swift
│   └── ...                    # SwiftUI application files
│
├── FoodLens Backend/
│   ├── api/
│   │   ├── main.py            # FastAPI application
│   │   ├── routes.py          # Recipe API endpoints
│   │   ├── database.py        # PostgreSQL connection
│   │   └── text_connection.py # Connection testing utilities
│   │
│   ├── database/
│   │   └── schema.sql         # Database schema
│   │
│   └── ...                    # Backend environment and files
│
└── README.md
```

## Getting Started

### Prerequisites

- Python 3.10 or later
- Git
- Xcode and a compatible macOS environment for building the iOS application
- A Supabase project with the required PostgreSQL tables
- A terminal or code editor, such as VS Code

### 1. Clone the Repository

```bash
git clone https://github.com/nandikapam123/FoodLens-Testing.git
cd FoodLens-Testing
```

### 2. Set Up the Backend

Navigate to the backend directory:

```bash
cd "FoodLens Backend"
```

Create and activate a Python virtual environment.

**Windows:**

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

Install the backend dependencies:

```bash
python -m pip install fastapi uvicorn psycopg2-binary python-dotenv
```

### 3. Configure Environment Variables

Create a `.env` file inside `FoodLens Backend/` and configure the database connection:

```dotenv
DATABASE_URL=your_postgresql_connection_string
```

Replace the placeholder with your actual Supabase PostgreSQL connection string.

**Security:** Never commit your `.env` file, database passwords, API keys, or other credentials to GitHub. Keep sensitive configuration in environment variables and exclude local secret files through `.gitignore`.

### 4. Configure the Database

Use the SQL schema in `FoodLens Backend/database/schema.sql` to understand and initialize the required database tables.

The backend expects a relational structure containing:

- `recipes` — recipe descriptions, instructions, cooking time, difficulty, and image URLs.
- `ingredients` — ingredient records.
- `recipe_ingredients` — relationships between recipes and ingredients, including quantities.

Ensure the tables and their foreign-key relationships are configured correctly in your Supabase project before testing the API.

### 5. Start the Backend Server

From the `FoodLens Backend` directory, run:

```bash
uvicorn api.main:app --reload
```

If your local package structure or working directory differs, adjust the module path accordingly.

By default, the development server runs at:

```text
http://127.0.0.1:8000
```

Open the interactive API documentation at:

```text
http://127.0.0.1:8000/docs
```

## API Reference

The backend currently exposes the following endpoints:

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/` | Check whether the API is running |
| `GET` | `/recipes` | Retrieve recipes and their ingredients |
| `GET` | `/recipes/{recipe_id}` | Retrieve one recipe by ID |

### Example Request

Retrieve all recipes:

```http
GET /recipes
```

Example response structure:

```json
[
  {
    "id": "recipe-uuid",
    "title": "Example Recipe",
    "description": "A sample recipe.",
    "instructions": "Follow the preparation steps.",
    "cook_time": 30,
    "difficulty": "Easy",
    "image_url": null,
    "ingredients": [
      {
        "name": "Example Ingredient",
        "quantity": "1 cup"
      }
    ]
  }
]
```

The values above illustrate the response structure; they are not actual records from the database.

### Interactive API Testing

With the backend running, visit `http://127.0.0.1:8000/docs` to explore and test the available endpoints through FastAPI's interactive documentation interface.

## Connecting the iOS Application

The SwiftUI application uses `BackendService.swift` to request recipe data from the API.

The service retrieves the `/recipes` endpoint using `URLSession` and decodes the JSON response into Swift models.

To connect the application to the backend:

1. Start the backend server.
2. Ensure the iOS device or simulator can reach the server.
3. Configure the API base URL in `BackendService.swift` to match the server's reachable address.
4. Verify that the API returns a successful response.
5. Test recipe decoding and display within the application.

**Networking note:** When testing on a physical iPhone, `127.0.0.1` refers to the phone itself, not your development computer. Use your computer's reachable local IP address or a deployed API URL. For production, configure HTTPS and an appropriate deployment environment.

## Development Goals

This repository supports the continued development and testing of FoodLens, including:

- Reliable database-backed recipe retrieval.
- Correct recipe and ingredient relationships.
- Consistent JSON responses between Python and Swift.
- Robust error handling for API and database failures.
- Secure database configuration.
- Preparation for eventual production deployment.

## Troubleshooting

**The API cannot connect to Supabase**
- Verify the `DATABASE_URL` environment variable.
- Check the database host, credentials, network access, and connection settings.
- Confirm that the required database tables exist.

**The recipes endpoint returns an empty list**
- Check whether the `recipes` table contains records.
- Verify that the application is connected to the intended Supabase project.

**The iOS application cannot reach the API**
- Confirm that the server is running.
- Verify the configured base URL and network connectivity.
- Check local firewall rules and iOS networking configuration.

**Recipe ingredients are missing**
- Verify the records in `ingredients` and `recipe_ingredients`.
- Confirm that the associated recipe and ingredient IDs match.

## Security

- Keep credentials and environment files out of version control.
- Enable and configure appropriate Row Level Security (RLS) policies where applicable.
- Never expose privileged database credentials in the iOS application.
- Use HTTPS and appropriate authentication and authorization controls for production deployments.

## Project Status

FoodLens-Testing is a development and backend-integration repository. Setup instructions, dependencies, and API behavior may evolve as the project progresses.

## Author

Developed as part of the FoodLens project.

**Repository:** [nandikapam123/FoodLens-Testing](https://github.com/nandikapam123/FoodLens-Testing)
