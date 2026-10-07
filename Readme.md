# Enterprise Sales & CRM Management System

A multi-tier, Java-based enterprise application suite designed for complete Sales & Customer Relationship Management (CRM). The system provides end-to-end management of sales leads, deal lifecycles, customer domains, external ERP integrations, and multi-threaded analytical reporting.

---

## Table of Contents
- [Architecture & Design Patterns](#architecture--design-patterns)
- [Key Features](#key-features)
- [System Requirements](#system-requirements)
- [Dependencies](#dependencies)
- [Database Setup & Configuration](#database-setup--configuration)
- [Build & Run Instructions](#build--run-instructions)
- [Project Structure](#project-structure)
- [Usage & Module Workflow](#usage--module-workflow)

---

## Architecture & Design Patterns

The system is structured cleanly across modular packages (`leads`, `deals`, `customers`, `analytics`, `com.designx.erp`) using enterprise software design patterns:

### Architectural Patterns
* **Facade Pattern (`AnalyticsFacade`, `CustomerFacade`):** Unifies underlying domain logic and data access into simple, high-level interfaces for consumption.
* **DAO / Repository Pattern (`LeadDAO`, `DealDAO`, `CustomerDAO`, `AnalyticsDAO`):** Decouples business logic from direct JDBC operations and SQL mapping.
* **Gateway Pattern (`SalesQuoteGateway`, `SdkSalesQuoteGateway`):** Decouples external ERP quote services and APIs from core sales operations.

### Behavioral Patterns
* **Command Pattern:** Encapsulates actions (e.g., `CreateLeadCommand`, `AddCustomerCommand`, `GenerateReportCommand`, `AdvanceDealStageCommand`) into standalone objects to standardize parameter validation and execution pipelines.
* **Factory Pattern (`LeadCommandFactory`, `DealCommandFactory`, `AnalyticsCommandFactory`):** Centralizes the initialization and dependency injection for command instances.

### Creational & Structural Patterns
* **Builder Pattern (`CustomerBuilder`):** Provides a fluent interface for building complex domain entities like `Customer`.
* **Workflow Engine Pattern (`LeadWorkflowEngine`, `DealWorkflowEngine`):** Encapsulates business rules, state transitions, and validation routines across lead and deal lifecycles.

---

## Key Features

* **Lead Lifecycle Management:** Create, track, view, list, update status, and remove leads with workflow validations.
* **Deal Pipeline Engine:** Manage deal stages, advance pipeline statuses, and query active deals filtered by stage.
* **Customer Management Subsystem:** Robust CRUD operations built with the Builder pattern and exposed via a unified Facade interface.
* **Multi-Threaded Analytics & Reporting:** High-performance, concurrent report generation using `ReportingEngine` to aggregate real-time business insights.
* **ERP Integration:** Extensible gateway architecture for synchronizing quote details and sales data with external systems.
* **Connection Pooling:** Integrated connection management powered by **HikariCP** and MySQL JDBC drivers for fast, reliable concurrency.

---

## System Requirements

* **Java Development Kit (JDK):** Version 17 or higher
* **Database:** MySQL Server 8.0+
* **Scripting Environment:** PowerShell 5.1+ (for automated execution on Windows) or Bash/Terminal (Linux/macOS)

---

## Dependencies

The required JAR dependencies are bundled or referenced under the project library paths:
* `HikariCP-5.1.0.jar` (Database Connection Pooling)
* `mysql-connector-j-9.3.0.jar` (MySQL JDBC Driver)
* `slf4j-api-2.0.9.jar` / `slf4j-simple-2.0.9.jar` (Logging abstraction and provider)

---

## Database Setup & Configuration

1. **Database Properties:**
   Configure your database credentials in `database.properties` (or the application configuration file):

   ```properties
   db.url=jdbc:mysql://localhost:3306/sales_crm_db
   db.username=your_db_user
   db.password=your_db_password
   db.pool.maxSize=10
   ```

2. **Database Initialization:**
   You can initialize the schema using the included utility tools or manually run the SQL script:
   * **Automated Setup:** Execute `SetupDatabase.java` or `CreateTables.java`.
   * **Manual Script:** Run `create_tables.sql` in your MySQL client:
     ```bash
     mysql -u your_db_user -p sales_crm_db < create_tables.sql
     ```

---

## Build & Run Instructions

### Automated Script (PowerShell - Windows)
To automatically compile dependencies and run the core system application, run:

```powershell
.\build-and-run.ps1
```

### Manual Compilation & Execution

1. **Create Output Directory:**
   ```bash
   mkdir -p bin
   ```

2. **Compile Java Files:**
   ```bash
   javac -cp "lib/*" -d bin $(find src -name "*.java")
   ```
   *(For Windows Command Prompt, specify file paths or use PowerShell syntax)*

3. **Run the Main Application:**
   ```bash
   java -cp "bin:lib/*" SalesManagementSystem
   ```

4. **Run Integration Tests:**
   ```bash
   java -cp "bin:lib/*" TestOrderIntegration
   ```

---

## Project Structure

```
├── src/
│   ├── leads/
│   │   ├── Lead.java
│   │   ├── LeadDAO.java
│   │   ├── LeadWorkflowEngine.java
│   │   ├── LeadCommandFactory.java
│   │   ├── CreateLeadCommand.java
│   │   ├── ViewLeadCommand.java
│   │   ├── ListLeadsCommand.java
│   │   ├── UpdateLeadStatusCommand.java
│   │   ├── DeleteLeadCommand.java
│   │   └── exceptions/ (LeadNotFound, LeadCreationFailed, LeadUpdateConflict)
│   ├── deals/
│   │   ├── Deal.java
│   │   ├── DealDAO.java
│   │   ├── DealWorkflowEngine.java
│   │   ├── DealCommandFactory.java
│   │   ├── CreateDealCommand.java
│   │   ├── ViewDealCommand.java
│   │   ├── ListDealsByStageCommand.java
│   │   ├── AdvanceDealStageCommand.java
│   │   ├── DeleteDealCommand.java
│   │   └── exceptions/ (DealNotFound, DealCreationFailed, DealUpdateConflict)
│   ├── customers/
│   │   ├── Customer.java
│   │   ├── CustomerBuilder.java
│   │   ├── CustomerDAO.java
│   │   ├── CustomerFacade.java
│   │   ├── AddCustomerCommand.java
│   │   └── exceptions/ (CustomerNotFound, DuplicateCustomerEntry, InvalidCustomerData)
│   ├── analytics/
│   │   ├── AnalyticsDAO.java
│   │   ├── AnalyticsFacade.java
│   │   ├── AnalyticsCommandFactory.java
│   │   ├── GenerateReportCommand.java
│   │   ├── ReportingEngine.java
│   │   └── exceptions/ (ReportGenerationFailed, ThreadExecutionTimeout)
│   ├── com/designx/erp/
│   │   ├── QuoteDetails.java
│   │   ├── SalesQuoteGateway.java
│   │   └── SdkSalesQuoteGateway.java
│   ├── ui/
│   │   ├── LeadDealUI.java
│   │   ├── CustomerUI.java
│   │   └── SalesManagementSystem.java
│   └── database/
│       ├── SetupDatabase.java
│       ├── CreateTables.java
│       └── create_tables.sql
├── lib/
│   ├── HikariCP-5.1.0.jar
│   └── mysql-connector-j-9.3.0.jar
├── build-and-run.ps1
├── database.properties
└── README.md
```

---

## Usage & Module Workflow

* **Command Pipeline:** Each action instantiated through factories (e.g., `LeadCommandFactory`) encapsulates inputs, handles transactional logic via the respective DAO, and enforces lifecycle rules via engines (`LeadWorkflowEngine`, `DealWorkflowEngine`).
* **Customer Creation:** Use `CustomerBuilder` to fluently populate attributes prior to passing the instance to `CustomerFacade`.
* **Multi-Threaded Analytics:** Request reports through `AnalyticsFacade`, which delegates tasks to `ReportingEngine` to process aggregates concurrently across thread pools.