# FastAPI Patient Management System

![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-green) ![Python](https://img.shields.io/badge/Python-3.8+-blue) ![License](https://img.shields.io/badge/License-MIT-blue)

A comprehensive Patient Management System API built with FastAPI, providing robust CRUD operations for patient records with automatic health metrics calculation and JSON-based data persistence.

## Table of Contents

- [Features](#features)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
- [Data Models](#data-models)
- [Examples](#examples)
- [Error Handling](#error-handling)
- [Technical Details](#technical-details)
- [Learning Resources](#learning-resources)

---

## Features

✨ **Core Capabilities:**

- **Complete CRUD Operations**: Create, Read, Update, and Delete patient records
- **Automatic Health Metrics**: Automatic BMI calculation with health verdict classification
- **Advanced Sorting**: Sort patients by height, weight, or BMI in ascending/descending order
- **Data Validation**: Comprehensive input validation using Pydantic models
- **Type Safety**: Full type hints for better code quality and IDE support
- **JSON Persistence**: File-based storage using JSON format
- **Automatic API Documentation**: Interactive Swagger UI and ReDoc documentation
- **Robust Error Handling**: Detailed HTTP exceptions with meaningful error messages
- **RESTful Design**: Standards-compliant REST API architecture

---

## Project Structure

```
fastapi/
├── main.py                 # Simple Hello World FastAPI example
├── requirements.txt        # Project dependencies
├── README.md              # Project documentation
├── LICENSE                # Project license
├── patients_project/      # Main Patient Management System
│   ├── main.py           # Patient Management System API
│   └── patients.json     # JSON database with patient records
└── myenv/                # Python virtual environment
    └── Scripts/          # Activation scripts
```

---

## Requirements

- **Python**: 3.8 or higher
- **FastAPI**: 0.137.0+
- **Uvicorn**: Web server for running the API
- **Pydantic**: 2.13.4+ (Data validation)

See `requirements.txt` for complete dependencies.

---

## Installation

### 1. Clone or Navigate to Project

```bash
cd fastapi
```

### 2. Create Virtual Environment

```bash
# Windows
python -m venv myenv
myenv\Scripts\activate

# Linux/macOS
python3 -m venv myenv
source myenv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the Application

```bash
# Main application
uvicorn main:app --reload

# Patient Management System (Recommended)
cd patients_project
uvicorn main:app --reload --port 8001
```

The API will be available at `http://localhost:8000` (or port 8001 for patients_project)

---

## Usage

### Access API Documentation

Once the server is running:

- **Interactive Swagger UI**: [http://localhost:8000/docs](http://localhost:8000/docs)
- **Alternative ReDoc**: [http://localhost:8000/redoc](http://localhost:8000/redoc)

### Basic Example with cURL

```bash
# Get all patients
curl -X GET "http://localhost:8000/view"

# Get specific patient
curl -X GET "http://localhost:8000/patient/P001"

# Create new patient
curl -X POST "http://localhost:8000/create" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "P005",
    "name": "John Doe",
    "city": "Delhi",
    "age": 30,
    "gender": "male",
    "height": 1.75,
    "weight": 75
  }'
```

---

## API Endpoints

### Information Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Welcome message |
| GET | `/about` | API information |

### Patient Management Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/view` | Retrieve all patients |
| GET | `/patient/{patient_id}` | Retrieve specific patient by ID |
| POST | `/create` | Create new patient record |
| PUT | `/edit/{patient_id}` | Update existing patient |
| DELETE | `/delete/{patient_id}` | Delete patient record |
| GET | `/sort` | Sort patients by field |

---

## Request/Response Examples

### 1. Create Patient (POST)

**Request:**
```bash
curl -X POST "http://localhost:8000/create" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "P005",
    "name": "Priya Singh",
    "city": "Bangalore",
    "age": 26,
    "gender": "female",
    "height": 1.62,
    "weight": 55
  }'
```

**Response (201):**
```json
{
  "message": "Patient created successfully"
}
```

### 2. Get Patient (GET)

**Request:**
```bash
curl -X GET "http://localhost:8000/patient/P001"
```

**Response (200):**
```json
{
  "name": "Ananya Verma",
  "city": "Guwahati",
  "age": 28,
  "gender": "female",
  "height": 1.65,
  "weight": 90,
  "bmi": 33.06,
  "verdict": "Obese"
}
```

### 3. Sort Patients (GET)

**Request:**
```bash
curl -X GET "http://localhost:8000/sort?sort_by=bmi&order=desc"
```

**Response (200):**
```json
[
  {
    "name": "Ananya Verma",
    "city": "Guwahati",
    "age": 28,
    "gender": "female",
    "height": 1.65,
    "weight": 90,
    "bmi": 33.06,
    "verdict": "Obese"
  },
  {
    "name": "Ravi Mehta",
    "city": "Mumbai",
    "age": 35,
    "gender": "male",
    "height": 1.75,
    "weight": 85,
    "bmi": 27.76,
    "verdict": "Overweight"
  }
]
```

### 4. Update Patient (PUT)

**Request:**
```bash
curl -X PUT "http://localhost:8000/edit/P001" \
  -H "Content-Type: application/json" \
  -d '{
    "weight": 85,
    "city": "New Delhi"
  }'
```

**Response (200):**
```json
{
  "message": "Patient Details Updated Successfully"
}
```

### 5. Delete Patient (DELETE)

**Request:**
```bash
curl -X DELETE "http://localhost:8000/delete/P001"
```

**Response (200):**
```json
{
  "message": "Patient record deleted successfully"
}
```

---

## Data Models

### Patient Model

The core data structure for patient records with automatic computation:

```python
class Patient(BaseModel):
    id: str                                    # Unique patient identifier
    name: str                                  # Patient full name
    city: str                                  # City of residence
    age: int                                   # Age (0-120 years)
    gender: Literal['male','female','others'] # Gender classification
    height: float                              # Height in meters (must be > 0)
    weight: float                              # Weight in kilograms (must be > 0)
    
    # Computed Fields (auto-calculated):
    bmi: float                                 # Body Mass Index
    verdict: str                               # Health classification
```

### Health Verdict Classification

The system automatically calculates BMI and provides health verdict based on WHO standards:

| BMI Range | Verdict |
|-----------|---------|
| < 18.5 | Underweight |
| 18.5 - 24.9 | Normal |
| 25.0 - 29.9 | Overweight |
| ≥ 30.0 | Obese |

**BMI Formula:** `BMI = weight (kg) / height² (m²)`

---

## Examples

### Complete Workflow Example

```bash
# 1. Create a new patient
curl -X POST "http://localhost:8000/create" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "P010",
    "name": "Rajesh Kumar",
    "city": "Chennai",
    "age": 45,
    "gender": "male",
    "height": 1.78,
    "weight": 95
  }'

# 2. Retrieve the patient
curl -X GET "http://localhost:8000/patient/P010"

# 3. Update the patient weight
curl -X PUT "http://localhost:8000/edit/P010" \
  -H "Content-Type: application/json" \
  -d '{"weight": 85}'

# 4. Sort all patients by BMI (descending)
curl -X GET "http://localhost:8000/sort?sort_by=bmi&order=desc"

# 5. Delete the patient
curl -X DELETE "http://localhost:8000/delete/P010"
```

### Sorting Examples

```bash
# Sort by height (ascending)
curl -X GET "http://localhost:8000/sort?sort_by=height&order=asc"

# Sort by weight (descending)
curl -X GET "http://localhost:8000/sort?sort_by=weight&order=desc"

# Sort by BMI (ascending)
curl -X GET "http://localhost:8000/sort?sort_by=bmi&order=asc"
```

---

## Error Handling

The API provides comprehensive error responses with meaningful messages.

### Error Response Format

```json
{
  "detail": "Error description"
}
```

### Common HTTP Status Codes

| Status Code | Meaning | Scenario |
|------------|---------|----------|
| **200 OK** | Success | Successful GET request |
| **201 Created** | Created | Successful POST request |
| **400 Bad Request** | Client Error | Invalid input or duplicate patient ID |
| **404 Not Found** | Client Error | Patient ID does not exist |
| **500 Internal Server Error** | Server Error | Server-side error |

### Error Examples

**Invalid Sort Field:**
```bash
curl -X GET "http://localhost:8000/sort?sort_by=invalid_field&order=asc"
```
**Response (400):**
```json
{
  "detail": "Invalid field select from ['height', 'weight', 'bmi']"
}
```

**Patient Not Found:**
```bash
curl -X GET "http://localhost:8000/patient/INVALID_ID"
```
**Response (404):**
```json
{
  "detail": "Patient not found"
}
```

**Duplicate Patient:**
```bash
curl -X POST "http://localhost:8000/create" \
  -d '{"id": "P001", ...}'
```
**Response (400):**
```json
{
  "detail": "Patient already exists"
}
```

---

## Technical Details

### Technology Stack

| Component | Technology | Version |
|-----------|-----------|---------|
| Framework | FastAPI | 0.137.0 |
| Web Server | Uvicorn | Latest |
| Data Validation | Pydantic | 2.13.4 |
| Async Support | Starlette | 1.3.1 |
| API Documentation | OpenAPI/Swagger | Auto-generated |

### Data Persistence

- **Storage Format**: JSON
- **Location**: `patients.json` in `patients_project` directory
- **Structure**: Key-value pairs with patient ID as key
- **Automatic Updates**: File is updated on every create, update, or delete operation

**Example JSON Structure:**
```json
{
  "P001": {
    "name": "Patient Name",
    "city": "City",
    "age": 30,
    "gender": "male",
    "height": 1.75,
    "weight": 75,
    "bmi": 24.49,
    "verdict": "Normal"
  }
}
```

### Features Implementation

- **Type Hints**: Full Python type annotations for code safety and IDE support
- **Validation**: Pydantic models with field constraints (age: 0-120, height/weight > 0)
- **Computed Fields**: Automatic BMI and verdict calculation using `@computed_field`
- **Path Parameters**: Patient IDs via URL path (`/patient/{patient_id}`)
- **Query Parameters**: Sorting options (`?sort_by=...&order=...`)
- **Status Codes**: Proper HTTP status codes for all responses
- **Request/Response Validation**: Automatic serialization/deserialization

---

## Development Notes

### Running Tests Manually

1. Start the server with `--reload` flag for hot reloading during development:
   ```bash
   uvicorn main:app --reload
   ```

2. Navigate to `/docs` for interactive testing interface

3. Test each endpoint using the Swagger UI

### Database File Management

The `patients.json` file serves as the database:

- **Location**: `patients_project/patients.json`
- **Permissions**: Ensure write permissions in the directory
- **Backup**: Consider backing up this file regularly
- **Format**: Standard JSON format (readable and version control friendly)

### Validation Rules

| Field | Rules |
|-------|-------|
| id | Required, must be unique |
| name | Required, string |
| city | Required, string |
| age | Required, integer, 0 < age < 120 |
| gender | Required, must be: male, female, or others |
| height | Required, float > 0 (in meters) |
| weight | Required, float > 0 (in kilograms) |

---

## Contributing

When contributing to this project:

1. Maintain code formatting consistency
2. Add proper type hints to all functions
3. Update documentation for new endpoints
4. Test thoroughly using the Swagger UI at `/docs`
5. Ensure error handling is comprehensive

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Support & Documentation

For additional information:

- **FastAPI Documentation**: https://fastapi.tiangolo.com/
- **Pydantic Documentation**: https://docs.pydantic.dev/
- **OpenAPI Specification**: https://www.openapis.org/

---

# Learning Resources

## What is an API?

- APIs are mechanisms that enable two software components such as the frontend and backend of an application to communicate with each other using a defined set of rules, protocols and data formats.

- Before APIs, we used to use Monolithic Architecture for projects. The Frontend and backend were tightly coupled. One change affects the entire Architecture components. (e.g. using Flask or PHP for creating websites.)

![API](image-1.png)

## What is FastAPI?

- FastAPI is a modern, high-performance web framework for building APIs with Python.
- It is built upon Python's two popular libraries: Starlette and Pydantic

- Starlette manages how your API receives requests and sends back responses.
- Pydantic ensures that data is in proper format or not.

### FastAPI Key Features:

1. **High Performance**: FastAPI is designed for speed, often outperforming other Python frameworks like Flask. It utilizes asynchronous programming and efficient libraries like Starlette and Pydantic for performance.

2. **Automatic Documentation**: FastAPI automatically generates interactive documentation using the OpenAPI standard. This allows developers to quickly see and test API endpoints.

3. **Type Hints**: FastAPI leverages Python's type-hinting system to enable type validation and automatic data serialisation, reducing errors and improving code readability.

4. **Easy to Learn and Use**: FastAPI is known for its intuitive design and ease of use, making it a good choice for beginners and experienced developers alike.

5. **Asynchronous Support**: FastAPI is built on top of Starlette, allowing for asynchronous programming, which is crucial for handling large numbers of concurrent requests.

## Web Server Architecture

![Web server](image-2.png)

- The interaction between the web server and the server gateway interface is managed by a protocol that outlines the guidelines for their communication. In Python, the protocol may be the Web Server Gateway Interface (WSGI) or the Asynchronous Server Gateway Interface (ASGI)

### 1. Web Server Gateway Interface (WSGI)

- WSGI (Web Server Gateway Interface) is a specification that outlines the communication protocol between the web server and a server gateway interface.
- It defines a straightforward interface for managing HTTP requests and responses, enabling developers to create web applications in Python without the need to delve into the intricacies of the underlying server.
- WSGI operates by exposing a Python function to the web server, typically named 'application' or 'app' by default. 
- Django and Flask both utilize WSGI.
- WSGI servers operate in a synchronous manner, they handle one request at a time, blocking the connection until the result is returned.

### 2. Asynchronous Server Gateway Interface (ASGI)

- ASGI is a more recent specification crafted to facilitate asynchronous programming in Python. Unlike WSGI, which is tailored for synchronous requests, ASGI empowers developers to create web applications that can handle multiple requests concurrently, without impeding the main thread.

- To implement ASGI, you can define a function named 'application' that accepts three parameters:
  - **scope**: A dictionary with information about the current request, akin to environ in WSGI, but with a slightly different naming convention for the details. For example, method, path, headers, query_string, etc.
  - **receive**: An async callable (function) that lets the application receive messages from the client.
  - **send**: An async callable that lets the application send messages back to the client.

## Path Parameters

- Path parameters are dynamic segments of a URL path used to identify a specific resource.

### Path() Function

- The `Path()` function in FastAPI is used to provide metadata, validation rules and documentation hints for path parameters in your API endpoints.
- Options include:
  - `title`: Parameter title
  - `description`: Parameter description
  - `ge` (greater than or equal to)
  - `gt` (greater than)
  - `le` (less than or equal to)
  - `lt` (less than)
  - `min_length`: Minimum string length
  - `max_length`: Maximum string length
  - `regex`: Regular expression pattern

## HTTP Status Codes

- HTTP status codes are 3 digit numbers returned by a web server (like FastAPI) to indicate the result of a client's request (like from a browser or API consumer).
- They help the client (browser, frontend, mobile app, etc) understand:
  1. Whether the request was successful
  2. Whether something went wrong
  3. What kind of issue occurred (if any)

### Status Code Categories

- **2XX - Success**: The request was successfully received and processed
- **3xx - Redirection**: Further action needs to be taken (e.g. Redirect)
- **4xx - Client Error**: Something is wrong with the request from the client
- **5xx - Server Error**: Something went wrong on the server side

### Common Status Codes

- **200 OK**: Standard success - A GET or POST succeeded
- **201 Created**: Resource created - After a POST that creates something
- **204 No Content**: Success but no data returned - After a DELETE request
- **400 Bad Request**: Malformed or invalid request - Missing field, wrong data type
- **401 Unauthorized**: No/invalid authentication - Login required
- **403 Forbidden**: Authenticated, but no permission - Logged in but not allowed
- **404 Not Found**: Resource doesn't exist - Patient ID not in DB
- **500 Internal Server Error**: Generic failure - Something broke on the server
- **502 Bad Gateway**: Gateway (like Nginx) failed to reach backend
- **503 Service Unavailable**: Server is down or overloaded

### HTTPException

- It is a special built-in exception in FastAPI used to return custom HTTP error responses when something goes wrong in your API.
- Instead of returning JSON or crashing the server, you can gracefully raise an error with:
  1. A proper HTTP status code (like 404, 400, 403, etc.)
  2. A custom error message
  3. Extra headers (optional)

### Query Parameters

- Query parameters are optional key-value pairs appended to the end of a URL used to pass additional data to the server in an HTTP request. They are typically employed for operations like filtering, sorting, searching, and pagination without altering the endpoint path itself.

Example: `/patients?city=Delhi&sort_by=age`

- The `?` marks the start of query parameters
- Each parameter is a key-value pair: `key=value`
- Multiple parameters are separated by `&`

In this case:
  1. `city=Delhi` is a query parameter for filtering
  2. `sort_by=age` is a query parameter for sorting

### Request Body

- Request body is the portion of an HTTP request that contains data sent by the client to the server. 
- It is typically used in HTTP methods such as POST or PUT to transmit structured data (e.g. JSON, XML, form-data) for the purpose of creating or updating resources on the server. 
- The server parses the request body to extract the necessary information and perform the intended operation.

### HTTP Methods

1. **POST**: Create new resources
2. **PUT**: Update existing resources
3. **DELETE**: Remove resources
4. **GET**: Retrieve resources
5. **PATCH**: Partially update resources

---

**Last Updated**: June 21, 2026  
**Version**: 1.0.0  
**Status**: Production Ready

- APIs are mechanisms that enable two software components such as the frontend and backend of an application to communicate with each other using a defined set of rules, protocols and data formats.

- Before APIs , we used to use Monolithic Architecture for projects.the Frontend and backend were tightly coupled. One change affects the entire Architecure components. (e.g. using flask or php for creating websites.)

![API](image-1.png)

# What is FastAPI ?

- FastAPI is a modern, high-performance web framework for building APIs with Python.
- It is built upon pythons' two popular libraries : Starlette and Pydantic

- Starlette manages how your API receives requests and sends back responses.
- Pydantic ensures that data is in proper format or not.

## Key Features:

1. High Performance:
FastAPI is designed for speed, often outperforming other Python frameworks like Flask. It utilizes asynchronous programming and efficient libraries like Starlette and Pydantic for performance.

2. Automatic Documentation:
FastAPI automatically generates interactive documentation using the OpenAPI standard. This allows developers to quickly see and test API endpoints.

3. Type Hints:
FastAPI leverages Python’s type-hinting system to enable type validation and automatic data serialisation, reducing errors and improving code readability.

4. Easy to Learn and Use:
FastAPI is known for its intuitive design and ease of use, making it a good choice for beginners and experienced developers alike.

5. Asynchronous Support:
FastAPI is built on top of Starlette, allowing for asynchronous programming, which is crucial for handling large numbers of concurrent requests.

# Web Server Architecture

![Web server](image-2.png)

- The interaction between the web server and the server gateway interface is managed by a protocol that outlines the guidelines for their communication. In Python, the protocol may be the Web Server Gateway Interface (WSGI) or the Asynchronous Server Gateway Interface (ASGI)

# 1. Web Server Gateway Interface (WSGI):

- WSGI (Web Server Gateway Interface) is a specification that outlines the communication protocol between the web server and a server gateway interface.
- It defines a straightforward interface for managing HTTP requests and responses, enabling developers to create web applications in Python without the need to delve into the intricacies of the underlying server.
- WSGI operates by exposing a Python function to the web server, typically named ‘application’ or ‘app’ by default. 
- Django and Flask both utilize WSGI.
- WSGI servers operate in a synchronous manner, they handle one request at a time, blocking the connection until the result is returned.

# 2. Asynchronous Server Gateway Interface (ASGI) :

- ASGI is a more recent specification crafted to facilitate asynchronous programming in Python. Unlike WSGI, which is tailored for synchronous requests, ASGI empowers developers to create web applications that can handle multiple requests concurrently, without impeding the main thread.

- To implement ASGI, you can define a function named ‘application’ that accepts three parameters.

i. scope: A dictionary with information about the current request, akin to environ in WSGI, but with a slightly different naming convention for the details.. For example, method, path, headers, query_string…etc.
ii. receive: An async callable (function) that lets the application send messages back to the client.
iii. send: An async callable that lets the application receive messages from the client..

# Path  Parameters

Path parameters are dynamic segments of a URL path used to identify a specific resource.

# Path()

- The path() function in FastAPI is used to provide metadata, validation rules and documentation hints for path parameters in your API endpoints.
  Title
  Description
- Example :
ge(greater than equal to),gt (greater than) ,le(less than equal),lt(less than)
Min_length
Max_length
regex   

# HTTP Status Codes

- HTTP status codes are 3 digit numbers returned by a web server (like FastAPI) to indicate the result of a client's request (like from a browser or API consumer).
- They help the client (browser,frontend,mobile app ,etc) understand:
 
 1. whether the request was successful
 2. whether something was went wrong.
 3. and what kind of issue occured (if any)

- 2XX - Success - The request was successfully received and processed
- 3xx - Redirection - Further action needs to be taken (e.g. Redirect)
- 4xx - Client Error - Something is wrong with the request from the client
- 5xx - Server Error - Something went wrong on the server side.

- 200 ok -->standard success --> A GET or POST succeeded
- 201 Created --> Resource created --> After a POST that creates something
- 204 No Content --> Success but no data returned --> After a DELETE request


- 400 Bad Request --> Malformed or invalid request --> Missing field, wrong data type
- 401 Unauthorized --> No/invalid authentication -->login required
- 403 forbidden --> Authenticated, but no permission --> logged in but not allowed
- 404 Not Found --> Resource doesn't exist --> Patient id not in DB

- 500 Internal server error --> Generic failure --> something broke on the server
- 502 Bad GW --> Gateway (like Nginx) failed to reach backend
- 503 Service Unavailable --> Server is down or overloaded

### HTTPException 

- It is a special built-in exception in FastAPI used to return custom HTTP error responses when somethin goes wrong in your API.
- Instead returning JSON or crashing the server,you can gracefully raise an error with :
1. A proper HTTP status code (like 404,400,403, etc.)
2. A custom error message
3. Extra headers (optional)

### Query Parameter

- Query parameters are optional key-value pairs appended to the end of a URL used to pass additional data to the server in an HTTP request. They are typically employed for operations like filtering , sorting, searching, and pagination without altering the endpoint path itself.

`/patients?city=Delhi&sort_by=age`

- The ? marks the start of query parameters.
- Each parameter is a key-value pair : key=value
- Multiple parameters are separated by &

- In this case :
1. city=Delhi is a query parameter for filtering.
2. sort_by=age is a query parameter for sorting.

### Request Body

- Request body is the portion of an HTTP request that contains data sent by the server. 
- It is typically used in HTTP methods such as POST or PUT to transmit structured data (e.g. JSON, XML,form-data) for the purpose of creating or updating resources on the server. 
- The server parses the request body to extract the necessary information and perform the intended operation.

### HTTP Methods

1. POST
2. PUT
3. DELETE
