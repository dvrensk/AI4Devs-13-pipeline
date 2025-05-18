# Development Guide

## Prerequisites

- Node.js (v18 or higher)
- npm (v9 or higher)
- PostgreSQL (v14 or higher)
- Git

## Setup Instructions

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd backend
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Set up environment variables:
   ```bash
   cp .env.example .env
   ```
   Edit `.env` with your configuration:
   ```
   DATABASE_URL="postgresql://user:password@localhost:5432/ai4devs"
   PORT=3000
   NODE_ENV=development
   JWT_SECRET=your-secret-key
   ```

4. Initialize the database:
   ```bash
   npm run prisma:init
   npm run prisma:generate
   npm run prisma:migrate
   ```

5. Start the development server:
   ```bash
   npm run dev
   ```

## Development Workflow

### Branch Strategy

- `main`: Production-ready code
- `develop`: Integration branch for features
- `feature/*`: New features
- `bugfix/*`: Bug fixes
- `hotfix/*`: Urgent production fixes

### Git Workflow

1. Create a new branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. Make your changes and commit:
   ```bash
   git add .
   git commit -m "feat: your feature description"
   ```

3. Push your branch:
   ```bash
   git push origin feature/your-feature-name
   ```

4. Create a pull request to `develop`

### Code Style

- Follow the TypeScript style guide
- Use ESLint and Prettier
- Run linting before committing:
  ```bash
  npm run lint
  ```

### Testing

1. Unit Tests:
   ```bash
   npm test
   ```

2. Coverage Report:
   ```bash
   npm run test:coverage
   ```

3. E2E Tests:
   ```bash
   npm run test:e2e
   ```

## Project Structure

```
src/
├── application/          # Application business logic
│   ├── services/        # Business logic services
│   └── use-cases/       # Use case implementations
├── domain/              # Domain models and interfaces
│   ├── entities/        # Domain entities
│   ├── repositories/    # Repository interfaces
│   └── value-objects/   # Value objects
├── presentation/        # Presentation layer
│   ├── controllers/     # Request handlers
│   └── middlewares/     # Express middlewares
├── routes/              # API route definitions
├── prompts/             # AI-related prompts
└── index.ts            # Application entry point
```

## Adding New Features

1. Create necessary domain entities
2. Implement repository interfaces
3. Create use cases
4. Implement controllers
5. Add routes
6. Write tests
7. Update documentation

## Database Changes

1. Create a new migration:
   ```bash
   npx prisma migrate dev --name your-migration-name
   ```

2. Update Prisma client:
   ```bash
   npx prisma generate
   ```

3. Apply migrations:
   ```bash
   npx prisma migrate deploy
   ```

## API Documentation

1. Update OpenAPI specification:
   ```bash
   npm run docs:generate
   ```

2. View API documentation:
   ```bash
   npm run docs:serve
   ```

## Deployment

### Development

1. Build the project:
   ```bash
   npm run build
   ```

2. Start the server:
   ```bash
   npm start
   ```

### Production

1. Set environment variables:
   ```
   NODE_ENV=production
   PORT=3000
   DATABASE_URL=your-production-db-url
   JWT_SECRET=your-production-secret
   ```

2. Build and start:
   ```bash
   npm run start:prod
   ```

## Monitoring and Logging

### Logging

- Use Winston for logging
- Log levels: error, warn, info, debug
- Log to file and console

### Monitoring

- Use Prometheus for metrics
- Grafana for visualization
- Health check endpoint: `/health`

## Troubleshooting

### Common Issues

1. Database Connection:
   - Check DATABASE_URL in .env
   - Verify PostgreSQL is running
   - Check network connectivity

2. Build Errors:
   - Clear node_modules: `rm -rf node_modules`
   - Reinstall: `npm install`
   - Rebuild: `npm run build`

3. Test Failures:
   - Check test database connection
   - Verify test data setup
   - Check for conflicting tests

### Debugging

1. Enable debug logs:
   ```bash
   DEBUG=* npm run dev
   ```

2. Use VS Code debugger:
   - Launch configuration in `.vscode/launch.json`
   - Set breakpoints
   - Start debugging session

## Performance Optimization

1. Database:
   - Use indexes appropriately
   - Optimize queries
   - Use connection pooling

2. API:
   - Implement caching
   - Use compression
   - Optimize response size

3. Code:
   - Profile performance
   - Optimize critical paths
   - Use async/await properly

## Security Best Practices

1. Authentication:
   - Use JWT with proper expiration
   - Implement refresh tokens
   - Secure password storage

2. Authorization:
   - Implement role-based access
   - Validate user permissions
   - Use middleware for protection

3. Data Protection:
   - Validate input
   - Sanitize output
   - Use HTTPS
   - Implement rate limiting

## Contributing

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a pull request

## Code Review Process

1. Automated checks:
   - Linting
   - Tests
   - Type checking

2. Manual review:
   - Code quality
   - Architecture
   - Security
   - Performance

3. Approval process:
   - At least one reviewer
   - All checks passing
   - Documentation updated 