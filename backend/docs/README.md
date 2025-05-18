# AI4Devs Recruitment System - Backend Documentation

## Overview
The AI4Devs Recruitment System is a comprehensive platform for managing the recruitment process, from candidate applications to interview management and position tracking. This documentation provides detailed information about the backend implementation, architecture, and usage.

## Documentation Structure

1. [Architecture](./architecture.md)
   - System Architecture
   - Design Patterns
   - Directory Structure
   - Technology Stack

2. [API Documentation](./api.md)
   - API Endpoints
   - Request/Response Formats
   - Authentication
   - Error Handling

3. [Database](./database.md)
   - Schema Design
   - Entity Relationships
   - Data Models
   - Migrations

4. [Development Guide](./development.md)
   - Setup Instructions
   - Development Workflow
   - Testing
   - Deployment

5. [Business Logic](./business-logic.md)
   - Core Features
   - Use Cases
   - Business Rules
   - Workflows

## Quick Start

1. Install dependencies:
   ```bash
   npm install
   ```

2. Set up environment variables:
   ```bash
   cp .env.example .env
   ```

3. Initialize the database:
   ```bash
   npm run prisma:init
   npm run prisma:generate
   ```

4. Start the development server:
   ```bash
   npm run dev
   ```

## Contributing
Please refer to the [Development Guide](./development.md) for detailed information about contributing to this project.

## License
This project is proprietary and confidential. 