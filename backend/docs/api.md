# API Documentation

## Overview
The AI4Devs Recruitment System API follows RESTful principles and provides endpoints for managing candidates, interviews, positions, and companies. All endpoints return JSON responses and use standard HTTP status codes.

## Base URL
```
http://localhost:3000/api/v1
```

## Authentication
All API endpoints require authentication using JWT tokens. Include the token in the Authorization header:
```
Authorization: Bearer <your-token>
```

## API Endpoints

### Candidates

#### Create Candidate
```http
POST /candidates
```

Request Body:
```json
{
  "firstName": "string",
  "lastName": "string",
  "email": "string",
  "phone": "string",
  "address": "string",
  "educations": [
    {
      "institution": "string",
      "title": "string",
      "startDate": "YYYY-MM-DD",
      "endDate": "YYYY-MM-DD"
    }
  ],
  "workExperiences": [
    {
      "company": "string",
      "position": "string",
      "description": "string",
      "startDate": "YYYY-MM-DD",
      "endDate": "YYYY-MM-DD"
    }
  ],
  "cv": {
    "filePath": "string",
    "fileType": "string"
  }
}
```

#### Get Candidate
```http
GET /candidates/{id}
```

#### Update Candidate Stage
```http
PUT /candidates/{id}
```

Request Body:
```json
{
  "applicationId": "integer",
  "currentInterviewStep": "integer"
}
```

### Positions

#### Create Position
```http
POST /positions
```

Request Body:
```json
{
  "companyId": "integer",
  "interviewFlowId": "integer",
  "title": "string",
  "description": "string",
  "location": "string",
  "jobDescription": "string",
  "requirements": "string",
  "responsibilities": "string",
  "salaryMin": "number",
  "salaryMax": "number",
  "employmentType": "string",
  "benefits": "string",
  "companyDescription": "string",
  "applicationDeadline": "YYYY-MM-DD",
  "contactInfo": "string"
}
```

#### Get Position
```http
GET /positions/{id}
```

#### Update Position
```http
PUT /positions/{id}
```

### Interviews

#### Schedule Interview
```http
POST /interviews
```

Request Body:
```json
{
  "applicationId": "integer",
  "interviewStepId": "integer",
  "employeeId": "integer",
  "interviewDate": "YYYY-MM-DD HH:mm:ss",
  "notes": "string"
}
```

#### Update Interview Result
```http
PUT /interviews/{id}
```

Request Body:
```json
{
  "result": "string",
  "score": "number",
  "notes": "string"
}
```

### Companies

#### Create Company
```http
POST /companies
```

Request Body:
```json
{
  "name": "string"
}
```

#### Get Company
```http
GET /companies/{id}
```

## Response Formats

### Success Response
```json
{
  "success": true,
  "data": {
    // Response data
  }
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "string",
    "message": "string",
    "details": {}
  }
}
```

## Status Codes

- `200 OK`: Request successful
- `201 Created`: Resource created successfully
- `400 Bad Request`: Invalid input data
- `401 Unauthorized`: Authentication required
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `500 Internal Server Error`: Server error

## Rate Limiting
API requests are limited to 100 requests per minute per IP address.

## File Upload
File uploads are handled through multipart/form-data requests. Maximum file size is 10MB.

## Pagination
List endpoints support pagination using query parameters:
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 10)

## Filtering
List endpoints support filtering using query parameters:
- `search`: Search term
- `sort`: Sort field
- `order`: Sort order (asc/desc)

## Error Handling
All errors follow a consistent format:
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable message",
    "details": {
      // Additional error details
    }
  }
}
```

## Webhooks
The API supports webhooks for real-time notifications. Configure webhooks in the company settings.

## API Versioning
API versioning is handled through the URL path. Current version is v1.

## SDK Support
Official SDKs are available for:
- JavaScript/TypeScript
- Python
- Java
- .NET

## API Changelog
See [CHANGELOG.md](./CHANGELOG.md) for API version history and changes. 