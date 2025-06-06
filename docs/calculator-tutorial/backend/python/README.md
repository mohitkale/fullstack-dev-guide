# Calculator REST API with FastAPI

This guide will walk you through creating a RESTful API for a calculator application using Python and FastAPI. The API will support basic arithmetic operations: addition, subtraction, multiplication, division, and modulo.

## Table of Contents
1. [Prerequisites](#1-prerequisites)
2. [Project Setup](#2-project-setup)
3. [Project Structure](#3-project-structure)
4. [Implementing the API](#4-implementing-the-api)
   - [4.1 Project Dependencies](#41-project-dependencies)
   - [4.2 Create the FastAPI Application](#42-create-the-fastapi-application)
   - [4.3 Implement Calculator Logic](#43-implement-calculator-logic)
   - [4.4 Add Exception Handling](#44-add-exception-handling)
   - [4.5 Add Request/Response Models](#45-add-requestresponse-models)
5. [Testing the API](#5-testing-the-api)
6. [Running the Application](#6-running-the-application)
7. [API Documentation](#7-api-documentation)
8. [Next Steps](#8-next-steps)

## 1. Prerequisites

Before you begin, ensure you have the following installed:

- Python 3.8 or later
- pip (Python package installer)
- Virtual environment (recommended)
- Your favorite code editor (VS Code, PyCharm, etc.)
- Postman or any API testing tool (for testing endpoints)

## 2. Project Setup

### 2.1 Create a Virtual Environment

```bash
# Create a new directory for the project
mkdir calculator-api
cd calculator-api

# Create a virtual environment
python -m venv venv

# Activate the virtual environment
# On Windows:
.\venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate
```

### 2.2 Install Required Packages

```bash
pip install fastapi uvicorn[standard] python-dotenv pydantic
```

## 3. Project Structure

Here's the project structure we'll be creating:

```
calculator-api/
├── app/
│   ├── __init__.py
│   ├── main.py              # Main FastAPI application
│   ├── calculator.py        # Calculator logic
│   ├── models.py           # Pydantic models
│   └── exceptions.py       # Custom exceptions
├── .env                    # Environment variables
├── requirements.txt        # Project dependencies
└── README.md
```

## 4. Implementing the API

### 4.1 Project Dependencies

Create a `requirements.txt` file with the following content:

```text
fastapi>=0.95.0
uvicorn[standard]>=0.21.0
python-dotenv>=1.0.0
pydantic>=1.10.7
```

### 4.2 Create the FastAPI Application

Create `app/main.py`:

```python
from fastapi import FastAPI, HTTPException, status
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
from typing import Optional
import os
from dotenv import load_dotenv

# Import calculator functions
from .calculator import (
    add, subtract, multiply, divide, modulo,
    DivisionByZeroError, ModuloByZeroError, CalculatorError
)

# Load environment variables
load_dotenv()

# Create FastAPI app
app = FastAPI(
    title="Calculator API",
    description="A RESTful API for performing basic arithmetic operations",
    version="1.0.0",
    docs_url="/docs",
    redoc_url="/redoc"
)

# Configure CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # In production, replace with your frontend URL
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Request/Response Models
class OperationRequest(BaseModel):
    a: float
    b: float

class OperationResponse(BaseModel):
    result: float
    operation: str
    a: float
    b: float

class ErrorResponse(BaseModel):
    detail: str
    error_code: str

# Health check endpoint
@app.get("/api/health", status_code=status.HTTP_200_OK)
async def health_check():
    return {"status": "ok", "message": "Calculator API is running"}

# Calculator endpoints
@app.post("/api/calculate/add", response_model=OperationResponse, status_code=status.HTTP_200_OK)
async def add_numbers(request: OperationRequest):
    result = add(request.a, request.b)
    return {
        "result": result,
        "operation": "add",
        "a": request.a,
        "b": request.b
    }

@app.post("/api/calculate/subtract", response_model=OperationResponse, status_code=status.HTTP_200_OK)
async def subtract_numbers(request: OperationRequest):
    result = subtract(request.a, request.b)
    return {
        "result": result,
        "operation": "subtract",
        "a": request.a,
        "b": request.b
    }

@app.post("/api/calculate/multiply", response_model=OperationResponse, status_code=status.HTTP_200_OK)
async def multiply_numbers(request: OperationRequest):
    result = multiply(request.a, request.b)
    return {
        "result": result,
        "operation": "multiply",
        "a": request.a,
        "b": request.b
    }

@app.post("/api/calculate/divide", response_model=OperationResponse, status_code=status.HTTP_200_OK)
async def divide_numbers(request: OperationRequest):
    try:
        result = divide(request.a, request.b)
        return {
            "result": result,
            "operation": "divide",
            "a": request.a,
            "b": request.b
        }
    except DivisionByZeroError as e:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )

@app.post("/api/calculate/modulo", response_model=OperationResponse, status_code=status.HTTP_200_OK)
async def modulo_numbers(request: OperationRequest):
    try:
        result = modulo(request.a, request.b)
        return {
            "result": result,
            "operation": "modulo",
            "a": request.a,
            "b": request.b
        }
    except ModuloByZeroError as e:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )

# Global exception handler
@app.exception_handler(CalculatorError)
async def calculator_exception_handler(request, exc):
    return JSONResponse(
        status_code=status.HTTP_400_BAD_REQUEST,
        content={"detail": str(exc), "error_code": "CALCULATOR_ERROR"}
    )

@app.exception_handler(Exception)
async def global_exception_handler(request, exc):
    return JSONResponse(
        status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
        content={"detail": "An unexpected error occurred", "error_code": "INTERNAL_SERVER_ERROR"}
    )

# Run the application
if __name__ == "__main__":
    import uvicorn
    port = int(os.getenv("PORT", 8000))
    uvicorn.run("app.main:app", host="0.0.0.0", port=port, reload=True)
```

### 4.3 Implement Calculator Logic

Create `app/calculator.py`:

```python
class CalculatorError(Exception):
    """Base exception for calculator errors"""
    pass

class DivisionByZeroError(CalculatorError):
    """Raised when attempting to divide by zero"""
    def __init__(self, message="Division by zero is not allowed"):
        self.message = message
        super().__init__(self.message)

class ModuloByZeroError(CalculatorError):
    """Raised when attempting to calculate modulo by zero"""
    def __init__(self, message="Modulo by zero is not allowed"):
        self.message = message
        super().__init__(self.message)

def add(a: float, b: float) -> float:
    """Add two numbers"""
    return a + b

def subtract(a: float, b: float) -> float:
    """Subtract b from a"""
    return a - b

def multiply(a: float, b: float) -> float:
    """Multiply two numbers"""
    return a * b

def divide(a: float, b: float) -> float:
    """Divide a by b"""
    if b == 0:
        raise DivisionByZeroError()
    return a / b

def modulo(a: float, b: float) -> float:
    """Calculate a modulo b"""
    if b == 0:
        raise ModuloByZeroError()
    return a % b
```

### 4.4 Add Exception Handling

Create `app/exceptions.py`:

```python
from fastapi import HTTPException, status
from fastapi.responses import JSONResponse
from typing import Any, Dict, Optional

class CalculatorError(HTTPException):
    def __init__(
        self,
        status_code: int = status.HTTP_400_BAD_REQUEST,
        detail: Any = None,
        headers: Optional[Dict[str, str]] = None,
    ) -> None:
        super().__init__(status_code=status_code, detail=detail, headers=headers)

class DivisionByZeroError(CalculatorError):
    def __init__(self, detail: str = "Division by zero is not allowed"):
        super().__init__(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=detail
        )

class ModuloByZeroError(CalculatorError):
    def __init__(self, detail: str = "Modulo by zero is not allowed"):
        super().__init__(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=detail
        )
```

### 4.5 Add Request/Response Models

Create `app/models.py`:

```python
from pydantic import BaseModel, Field
from typing import Optional

class OperationRequest(BaseModel):
    """Request model for calculator operations"""
    a: float = Field(..., description="First number")
    b: float = Field(..., description="Second number")

    class Config:
        schema_extra = {
            "example": {
                "a": 10,
                "b": 5
            }
        }

class OperationResponse(BaseModel):
    """Response model for calculator operations"""
    result: float = Field(..., description="The result of the operation")
    operation: str = Field(..., description="The operation that was performed")
    a: float = Field(..., description="First number")
    b: float = Field(..., description="Second number")

    class Config:
        schema_extra = {
            "example": {
                "result": 15,
                "operation": "add",
                "a": 10,
                "b": 5
            }
        }

class ErrorResponse(BaseModel):
    """Error response model"""
    detail: str = Field(..., description="Error message")
    error_code: str = Field(..., description="Error code")

    class Config:
        schema_extra = {
            "example": {
                "detail": "Division by zero is not allowed",
                "error_code": "DIVISION_BY_ZERO"
            }
        }
```

## 5. Testing the API

### 5.1 Start the Development Server

```bash
# From the project root directory
uvicorn app.main:app --reload
```

### 5.2 Test Endpoints

You can test the API using curl or Postman:

#### Health Check
```bash
curl "http://localhost:8000/api/health"
```

#### Addition
```bash
curl -X POST "http://localhost:8000/api/calculate/add" \
  -H "Content-Type: application/json" \
  -d '{"a": 10, "b": 5}'
```

#### Subtraction
```bash
curl -X POST "http://localhost:8000/api/calculate/subtract" \
  -H "Content-Type: application/json" \
  -d '{"a": 10, "b": 5}'
```

#### Multiplication
```bash
curl -X POST "http://localhost:8000/api/calculate/multiply" \
  -H "Content-Type: application/json" \
  -d '{"a": 10, "b": 5}'
```

#### Division
```bash
curl -X POST "http://localhost:8000/api/calculate/divide" \
  -H "Content-Type: application/json" \
  -d '{"a": 10, "b": 5}'
```

#### Modulo
```bash
curl -X POST "http://localhost:8000/api/calculate/modulo" \
  -H "Content-Type: application/json" \
  -d '{"a": 10, "b": 3}'
```

#### Error Case (Division by Zero)
```bash
curl -X POST "http://localhost:8000/api/calculate/divide" \
  -H "Content-Type: application/json" \
  -d '{"a": 10, "b": 0}'
```

## 6. Running the Application

### 6.1 Development Mode

```bash
# Activate virtual environment (if not already activated)
source venv/bin/activate  # On Windows: .\venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run the application with hot reload
uvicorn app.main:app --reload
```

### 6.2 Production Mode

For production, you should use a production ASGI server like Uvicorn with Gunicorn:

```bash
# Install Gunicorn
pip install gunicorn

# Run with Gunicorn (4 worker processes)
gunicorn -w 4 -k uvicorn.workers.UvicornWorker app.main:app
```

## 7. API Documentation

FastAPI automatically generates interactive API documentation using Swagger UI and ReDoc:

- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

The documentation is interactive, allowing you to test the API endpoints directly from the browser.

## 8. Environment Variables

Create a `.env` file in the project root to set environment variables:

```env
# Server configuration
HOST=0.0.0.0
PORT=8000
DEBUG=True

# CORS (comma-separated list of allowed origins)
CORS_ORIGINS=*
```

## 9. Next Steps

1. **Add Unit Tests**: Use `pytest` to write unit tests for the calculator functions and API endpoints.
2. **Add Integration Tests**: Test the API using `TestClient` from FastAPI.
3. **Add Logging**: Configure logging for better debugging and monitoring.
4. **Add Authentication**: Secure the API with JWT or OAuth2.
5. **Containerization**: Create a Dockerfile and docker-compose.yml for containerization.
6. **CI/CD**: Set up a CI/CD pipeline using GitHub Actions or GitLab CI.
7. **Monitoring**: Add monitoring and metrics using Prometheus and Grafana.
8. **Rate Limiting**: Implement rate limiting to prevent abuse.
9. **Input Validation**: Add more robust input validation using Pydantic validators.
10. **API Versioning**: Implement API versioning for future updates.

## 10. Conclusion

You've successfully created a RESTful calculator API using FastAPI. The API supports basic arithmetic operations, includes proper error handling, and provides interactive documentation. FastAPI's automatic data validation and OpenAPI documentation make it a great choice for building robust and maintainable APIs in Python.
