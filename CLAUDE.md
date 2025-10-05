# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an e-commerce microservices application built with .NET Aspire and GenAI, demonstrating AI-powered distributed architectures. The application includes semantic search capabilities using Ollama and Semantic Kernel for intelligent product discovery.

## Solution Structure

The solution uses .NET Aspire's orchestration pattern with three main solution folders:

- **aspire**: Contains AppHost (orchestration) and ServiceDefaults (shared infrastructure)
- **services**: Contains Catalog and Basket microservices
- **frontend**: Contains WebApp (Blazor Server UI)

All project files are located in `src/` directory. The main solution file is `eshop-distributed.sln` in the root.

## Build and Run Commands

### Running the Application
```bash
cd src/AppHost
dotnet run
```
This starts the .NET Aspire orchestrator which launches all services and backing resources.

### Building the Solution
```bash
dotnet build
```

### Database Migrations (Catalog service)
```bash
cd src/Catalog
dotnet ef migrations add <MigrationName>
dotnet ef database update
```

## Architecture

### AppHost Orchestration (src/AppHost/Program.cs)
The AppHost defines the complete distributed application topology:

**Backing Services:**
- PostgreSQL with PgAdmin (Catalog database)
- Redis with RedisInsight (Caching and output caching)
- RabbitMQ with Management UI (Message broker)
- Keycloak (Authentication/Authorization)
- Ollama with OpenWebUI (LLM hosting with llama3.2 and all-minilm models)

**Service Dependencies:**
- **Catalog** depends on: catalogdb, rabbitmq, llama, embedding
- **Basket** depends on: cache, catalog, rabbitmq, keycloak
- **WebApp** depends on: cache, catalog, basket

All services use `.WaitFor()` to ensure dependencies are ready before starting.

### ServiceDefaults Project
Shared infrastructure code used by all services:
- `Extensions.cs`: Configures telemetry, health checks, service discovery
- `Messaging/MassTransitExtentions.cs`: Configures MassTransit with RabbitMQ for event-driven messaging
- `Messaging/Events/`: Integration event definitions (e.g., ProductPriceChangedIntegrationEvent)

### Catalog Service (src/Catalog)
Product catalog microservice with AI capabilities:
- **Database**: PostgreSQL with Entity Framework Core
- **AI Features**:
  - Chat support via `ProductAIService.SupportAsync()` using Ollama LLM
  - Semantic product search via `ProductAIService.SearchProductsAsync()` using vector embeddings
  - In-memory vector store for product embeddings
- **Data**: `ProductDbContext`, migrations in `Data/Migrations/`
- **Models**: `Product.cs` (shared with Basket/WebApp), `ProductVector.cs` (for embeddings)
- **Services**: `ProductService.cs`, `ProductAIService.cs`
- **Endpoints**: `ProductEndpoints.cs` maps HTTP endpoints
- **Messaging**: Publishes integration events via MassTransit/RabbitMQ

### Basket Service (src/Basket)
Shopping basket microservice with authentication:
- **Cache**: Redis distributed cache for basket data
- **Authentication**: Keycloak JWT bearer authentication (realm: "eshop", audience: "account")
- **API Integration**: HTTP client to Catalog service for product data
- **Models**: Links to `../Catalog/Models/Product.cs`
- **Services**: `BasketService.cs`
- **Endpoints**: `BasketEndpoints.cs`
- **Event Handlers**: `ProductPriceChangedIntegrationEventHandler.cs` (MassTransit consumer)
- **Messaging**: Consumes integration events via MassTransit/RabbitMQ

### WebApp (src/WebApp)
Blazor Server frontend application:
- **UI Framework**: Blazor Server with interactive components
- **Caching**: Redis output caching
- **API Clients**: HTTP clients to Catalog service
- **Models**: Links to `../Catalog/Models/Product.cs`
- **Components**: Razor components in `Components/` directory

## Key Patterns

### Service-to-Service Communication
- HTTP-based communication using typed HttpClients with service discovery
- Example: `client.BaseAddress = new("https+http://catalog")` uses Aspire service discovery
- The `https+http://` scheme allows both secure and insecure communication

### Event-Driven Messaging
- MassTransit with RabbitMQ for asynchronous integration events
- Integration events defined in ServiceDefaults project
- Consumers automatically registered via `AddMassTransitWithAssemblies(Assembly.GetExecutingAssembly())`
- Events follow naming convention: `*IntegrationEvent` with handlers: `*IntegrationEventHandler`

### AI/GenAI Integration
- Ollama provides local LLM hosting (llama3.2 for chat, all-minilm for embeddings)
- Semantic Kernel's `IChatClient` and `IEmbeddingGenerator<string, Embedding<float>>` abstractions
- Vector search using `IVectorStoreRecordCollection<int, ProductVector>` with in-memory vector store
- Embeddings initialized lazily on first search query

### .NET Aspire Patterns
- All services call `builder.AddServiceDefaults()` to configure telemetry, health checks, and service discovery
- Connection strings managed by Aspire (named: "catalogdb", "cache", "rabbitmq", "keycloak")
- Container lifetime set to `ContainerLifetime.Persistent` for backing services
- Data volumes conditionally added only when `IsRunMode` (not for Azure Container Apps deployment)

## Azure Deployment

The project includes Azure Developer CLI (azd) configuration:
- `src/azure.yaml`: Defines the azd project
- Deploy with: `azd up` (or `azd provision` then `azd deploy`)
- Infrastructure is generated from the AppHost project
- To customize infrastructure: `azd infra synth` (requires `azd config set alpha.infraSynth on`)

## .NET Version
Targets .NET 9.0 with .NET Aspire 9.1.0 and preview packages for Keycloak integration.