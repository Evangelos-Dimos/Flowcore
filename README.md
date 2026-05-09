## D01: Requirements Interpretation Scope and Assumptions

This document defines the high-level scope, objectives, assumptions, and limitations of the FlowCore Warehouse Simulation System.

### 📁 File

| File | Purpose |
| :--- | :--- |
| `D01_Requirements_Interpretation_Scope_and_Assumptions.pdf` | Defines the project’s scope, objectives, system boundaries, and key assumptions. |

### 🎯 Objectives

- Simulate real-world warehouse operations
- Ensure data consistency and correctness
- Validate system behavior under different scenarios
- Implement automation for warehouse processes

### 📦 Scope (Included)

| Functionality | Description |
| :--- | :--- |
| Product receiving and storage | Add new products to the warehouse |
| Inventory management and tracking | Monitor stock levels per location |
| Customer order creation | Create new orders based on product availability |
| Order processing and status updates | Process orders and update statuses (PENDING, SHIPPED, FAILED) |
| Configuration-based system behavior | Control logic via external configuration files |

### ❌ Out of Scope

- Graphical user interface (GUI)
- Concurrency handling
- Multi-user support
- Complex warehouse models (e.g., dynamic routing)

### 🔧 Assumptions

- Each product type is stored in a predefined location
- The system operates in a single-user environment
- Inventory is managed per physical location (rack)

### ⚠️ Limitations

- No concurrent operation handling
- No graphical interface
- Simplified warehouse model (no nested locations, pick paths, etc.)

> **Note:** This document serves as the foundation for all subsequent design and implementation decisions.

---

## D02: Warehouse Process Flow and Business Rules

This document defines the functional and non-functional requirements, business rules, and system constraints.

### 📁 File

| File | Purpose |
| :--- | :--- |
| `D02_Warehouse_Process_Flow_and_Business_Rules.pdf` | Specifies system behavior, business logic, and quality attributes. |

### ✅ Functional Requirements

| Area | Key Requirements |
| :--- | :--- |
| **Product Management** | Receive products, assign to valid locations, update quantities automatically |
| **Inventory Management** | Track inventory per location, enforce capacity limits |
| **Order Management** | Create orders, validate availability, store order details |
| **Order Processing** | Process orders via DB procedure, update inventory and status |
| **Configuration Management** | Read external config files, validate, sync with DB |
| **System Validation** | Check DB connectivity and system readiness |

### ⚡ Non-Functional Requirements

| Category | Description |
| :--- | :--- |
| Performance | Efficient execution, quick script response |
| Reliability | Graceful handling of invalid inputs, data consistency |
| Usability | Easy command-line usage, clear messages |
| Maintainability | Modular design, configuration changes without code changes |
| Portability | Docker-based deployment, minimal setup across systems |

### 🔒 Constraints

- No graphical interface
- Each product type → predefined location
- Single-user operation only

### 📌 Assumptions

- Input data is provided in valid format
- Database is available and running
- Configuration files are correctly defined

> **Note:** This document is the authoritative source for system behavior validation.

---

## D03: Solution Architecture and Component Design

This document describes the modular architecture, database design, data flow, and system workflows.

### 📁 File

| File | Purpose |
| :--- | :--- |
| `D03_Solution_Architecture_and_Component_Design.pdf` | Defines system components, ER diagram, data flow, and workflow. |

### 🧱 System Components

| Layer | Technology | Responsibilities |
| :--- | :--- | :--- |
| **Database Layer** | Oracle | Stores data, enforces business rules via constraints, triggers, and procedures |
| **Script Layer** | Bash | Automates operations, user interaction, SQL execution |
| **Validation Layer** | C++ | Validates configuration files and system parameters |
| **Execution Environment** | Docker | Ensures consistency, isolation, and easy deployment |

### 🗄️ Database Key Elements

- **Tables:** `products`, `inventory`, `locations`, `orders`, `order_items`
- **Procedures:** Order processing logic
- **Triggers:** Automatic updates and validation

### 📜 Bash Scripts

| Script | Purpose |
| :--- | :--- |
| `receive_product.sh` | Product receiving |
| `check_inventory.sh` | Inventory checking |
| `create_order.sh` | Order creation |
| `process_order.sh` | Order processing |
| `sync_config_to_db.sh` | Configuration synchronization |

### 🔄 System Workflow

1. System validation (network & configuration)
2. Configuration synchronization to database
3. Product receiving and storage
4. Inventory tracking
5. Order creation
6. Order processing and shipping

### 📈 Data Flow Description

| Step | Description |
| :--- | :--- |
| 1 | Configuration files (`warehouse.conf`, `db.conf`) contain business rules and credentials |
| 2 | C++ module validates configuration parameters |
| 3 | Bash scripts execute SQL commands and orchestrate operations |
| 4 | Oracle database stores data and enforces PL/SQL logic |

### ✨ Design Advantages

- Modular architecture
- Clear separation of concerns
- Strong validation mechanisms
- Easy extensibility

### ⚠️ Limitations (Architectural)

- Single-location mapping per product type
- No concurrent user handling
- Simplified order processing logic

> **Note:** This document is essential for understanding how components interact and how to extend the system.


## D04: Configuration and Environment Setup Pack

This document defines the configuration files that control the behavior of the FlowCore Warehouse Simulation System.

### 📁 Files

| File | Purpose |
| :--- | :--- |
| `warehouse.conf` | Business rules and operational parameters (e.g., permitted product types, location mappings, capacity limits) |
| `db.conf` | Database connection credentials for the Oracle backend |

### ⚙️ Configuration Parameters (warehouse.conf)

| Parameter | Description | Example |
| :--- | :--- | :--- |
| `VALID_TYPES` | List of allowed product types (space-separated) | `laptop tablet smartphone` |
| `MAX_ITEMS_PER_LOCATION` | Maximum capacity per physical rack | `10` |
| `ALLOWED_LOCATIONS_<type>` | Maps a product type to a specific rack automatically | `ALLOWED_LOCATIONS_laptop="A1"` |
| `DEFAULT_ORDER_STATUS` | Default order status for new orders | `PENDING` |
| `COMPLETED_STATUS` | Order status when successfully shipped | `COMPLETED` |
| `FAILED_STATUS` | Order status when processing fails | `FAILED` |
| `ID_PREFIX` | Product ID prefix (every product ID starts with this letter) | `P` |
| `LOG_FILE` | Path to the system log file | `../logs/system.log` |
| `LOG_LEVEL` | Determines verbosity of logging (`INFO`, `DEBUG`, `ERROR`) | `INFO` |
| `SCRIPT_DIR` | Linux bash scripts path | `../scripts` |

### 🔐 Database Connection (db.conf)

| Parameter | Description |
| :--- | :--- |
| `DB_CONTAINER` | Name of the Docker container (e.g., `oracle23ai`) |
| `DB_USER` | Database username (e.g., `system`) |
| `DB_PASSWORD` | User password |
| `DB_SERVICE` | Oracle service name (e.g., `FREEPDB1`) |

### 🔄 Usage Workflow

| Step | Description |
| :--- | :--- |
| 1 | Edit business rules in `warehouse.conf` as needed |
| 2 | Run C++ validation tool (`../D09_CPP_Component/validate_config`) |
| 3 | Execute `../D05_Bash_Scripts/sync_config_to_db.sh` to sync configuration with database |
| 4 | Run operational scripts (`receive_product.sh`, `dispatch_order.sh`, etc.) |

### ⚠️ Important Notes

- Any change to `warehouse.conf` requires running `sync_config_to_db.sh` before execution
- `db.conf` is excluded from version control (via `.gitignore`) to protect credentials
- `warehouse.conf` should be committed as it contains business logic for documentation

> **Note:** This document is essential for understanding how configuration drives system behavior and ensures consistent commissioning.

## D05: Linux and Bash Operations Pack

This directory contains the Bash automation scripts that orchestrate warehouse operations, system validation, and database interactions.

### 📁 Files

| File | Purpose |
| :--- | :--- |
| `assign_location.sh` | Assigns products to valid storage locations based on configuration |
| `check_inventory.sh` | Checks current inventory levels per location |
| `check_network.sh` | Validates database connectivity and network readiness |
| `check_system_configuration.sh` | Verifies system configuration before execution |
| `create_order.sh` | Creates new customer orders with availability validation |
| `docker.sh` | Manages Docker container lifecycle and environment setup |
| `export_from_db.sh` | Exports database contents for backup or analysis |
| `my_push.sh` | Utility script for repository synchronization |
| `process_order.sh` | Processes existing orders and updates inventory |
| `receive_product.sh` | Handles product receiving and storage workflow |
| `run_demo.sh` | Executes the demonstration sequence |
| `sync_config_to_db.sh` | Synchronizes configuration files with database tables |
| `warehouse_reader.sh` | Reads and displays warehouse system status |

### 🔄 Core Workflows

| Script | Function |
| :--- | :--- |
| **System Validation** | `check_network.sh` → `check_system_configuration.sh` |
| **Configuration Sync** | `sync_config_to_db.sh` |
| **Product Operations** | `receive_product.sh` → `assign_location.sh` → `check_inventory.sh` |
| **Order Operations** | `create_order.sh` → `process_order.sh` |
| **Demo Execution** | `run_demo.sh` |

### ⚙️ Usage Notes

- All scripts source configuration from `../D04_Configuration/db.conf`
- Scripts log operations to the system log file defined in `warehouse.conf`
- Execute `docker.sh` first to ensure the database environment is running
- `my_push.sh` is used for development workflow only

> **Note:** This pack is the primary user interaction layer of the simulation system.

---

## D06: Oracle Schema and Sample Data Pack

This directory contains the SQL schema definition and sample data for the Oracle database backend.

### 📁 Files

| File | Purpose |
| :--- | :--- |
| `01_drop_tables.sql` | Drops existing tables for clean re-initialization |
| `02_create_tables.sql` | Creates all database tables (`products`, `inventory`, `locations`, `orders`, `order_items`) |
| `03_sample_data.sql` | Populates tables with sample products, locations, and inventory |
| `07_verification.sql` | Verifies schema integrity and data correctness after setup |
| `database.sql` | Consolidated schema file (all DDL + sample data) |
| `README.md` | Documentation for the database schema |

### 🗄️ Database Objects

| Object Type | Description |
| :--- | :--- |
| **Tables** | `products`, `inventory`, `locations`, `orders`, `order_items` |
| **Constraints** | Primary keys, foreign keys, check constraints, unique constraints |
| **Indexes** | Performance optimization on foreign keys and lookup columns |

### 📊 Sample Data Includes

- Predefined location racks (e.g., A1, B2, C3)
- Product types matching `warehouse.conf` configuration
- Initial inventory quantities
- Reference orders for testing

### 🔄 Execution Order

| Step | File | Action |
| :--- | :--- | :--- |
| 1 | `01_drop_tables.sql` | Clean existing schema |
| 2 | `02_create_tables.sql` | Create fresh schema |
| 3 | `03_sample_data.sql` | Load sample data |
| 4 | `07_verification.sql` | Validate installation |

> **Note:** Always execute scripts in numeric order for correct schema initialization.

---

## D07: SQL Operations Pack

This directory contains SQL query files demonstrating various database operations for warehouse management.

### 📁 Files

| File | Purpose |
| :--- | :--- |
| `01_select_queries.sql` | Basic SELECT queries for retrieving warehouse data |
| `02_filter_queries.sql` | Filtered queries using WHERE clauses and conditions |
| `03_join_queries.sql` | Multi-table JOIN operations (products, inventory, orders) |
| `04_aggregation.sql` | Aggregate functions (SUM, COUNT, AVG, GROUP BY) |
| `05_modification.sql` | INSERT, UPDATE, DELETE operations with validation |
| `06_report_queries.sql` | Business reports (stock levels, order summaries, etc.) |
| `README.md` | Documentation for the SQL operations |

### 📋 Query Categories

| Category | Operations Covered |
| :--- | :--- |
| **Selection** | Retrieve all products, orders, inventory records |
| **Filtering** | Location-based filtering, status filtering, quantity thresholds |
| **Joins** | Product-inventory joins, order-item joins |
| **Aggregation** | Total stock per location, order value calculations |
| **Modification** | Update inventory, change order status, insert new products |
| **Reporting** | Low stock alerts, order processing reports |

### 🔍 Example Query Types

- List all products in a specific location
- Show pending orders with customer details
- Calculate total inventory value per product type
- Find locations below minimum stock threshold

> **Note:** These queries assume the schema from D06 is properly initialized.

---

## D08: PLSQL Logic Pack

This directory contains PL/SQL stored procedures, functions, and triggers that enforce business logic at the database level.

### 📁 Files

| File | Purpose |
| :--- | :--- |
| `01_functions.sql` | PL/SQL functions for reusable business calculations |
| `02_procedures.sql` | Stored procedures for order processing and inventory management |
| `03_triggers.sql` | Database triggers for automatic validation and auditing |
| `README.md` | Documentation for the PL/SQL logic components |

### 🔧 PL/SQL Components

| Component Type | Description |
| :--- | :--- |
| **Functions** | Availability checks, capacity validation, stock calculations |
| **Procedures** | `process_order`, `update_inventory`, `receive_product` |
| **Triggers** | Before-insert validation, after-update logging, capacity enforcement |

### ⚙️ Business Rules Enforced

| Rule | Implementation |
| :--- | :--- |
| Location capacity limits | Trigger checks `MAX_ITEMS_PER_LOCATION` before insert |
| Product-type location mapping | Function validates allowed locations from config |
| Order availability validation | Procedure checks stock before processing |
| Inventory consistency | Trigger updates after each modification |

### 📐 Key Procedures

| Procedure | Description |
| :--- | :--- |
| `process_order(order_id)` | Processes order, updates inventory, sets status (COMPLETED/FAILED) |
| `receive_product(product_id, qty)` | Adds products to inventory with location validation |
| `sync_config()` | Synchronizes configuration parameters to database |

> **Note:** Functions and procedures are stored in the Oracle database and are called by the Bash scripts in D05.

## D09: C++ Utility or Logic Module

This directory contains the C++ utility programs that validate system configuration, analyze warehouse data, and provide operational insights.

### 📁 Files

| File | Purpose |
| :--- | :--- |
| `check_configuration.cpp` | Validates configuration file correctness and parameter values |
| `count_id_products.cpp` | Counts and reports product ID statistics from the database |
| `low_stock_alert.cpp` | Analyzes inventory and generates alerts for low stock items |
| `main.cpp` | Entry point for the C++ validation module |
| `product_summary.cpp` | Generates summary reports of product inventory |
| `warehouse_reader.cpp` | Reads and displays detailed warehouse system status |

### 🔧 Module Responsibilities

| Component | Function |
| :--- | :--- |
| **Configuration Validation** | Verifies `warehouse.conf` parameters (types, capacity limits, location mappings) |
| **Data Analysis** | Processes inventory data and generates business reports |
| **Alert Generation** | Identifies stock levels below configured thresholds |
| **Pre-execution Checks** | Ensures system readiness before Bash script execution |

### 🔄 Integration Points

| Integration | Description |
| :--- | :--- |
| **Input** | Reads `warehouse.conf` and `db.conf` configuration files |
| **Output** | Returns validation result (valid/invalid) and analysis data |
| **Caller** | Invoked by `check_system_configuration.sh` before operations |
| **Database** | Optionally connects to Oracle for real-time data analysis |

### ⚙️ Key Validation Rules

| Rule | Check Performed |
| :--- | :--- |
| Valid product types | Confirms types match `VALID_TYPES` list |
| Location capacity | Verifies `MAX_ITEMS_PER_LOCATION` is numeric and positive |
| Location mapping | Ensures each product type has an allowed location |
| ID prefix | Validates `ID_PREFIX` is a single character |
| Log configuration | Checks `LOG_FILE` path and `LOG_LEVEL` value |

> **Note:** The C++ module is executed during system startup and commissioning to prevent configuration errors before database operations begin.

---

## D10: Testing & Validation

This document presents the testing and validation activities performed to verify system correctness under normal and error conditions.

### 📁 File

| File | Purpose |
| :--- | :--- |
| `D10_Testing_Validation.pdf` | Documents test cases, execution results, and validation summary. |

### 🧪 Test Cases Summary

| Test Case | Description | Result |
| :--- | :--- | :--- |
| **TC1 - System Readiness Check** | Verify network connectivity and database accessibility | ✅ PASS |
| **TC2 - Configuration Validation (C++)** | Validate configuration file correctness | ✅ PASS |
| **TC3 - Configuration Sync** | Verify config synchronization to database | ✅ PASS |
| **TC4 - Product Receiving (Valid)** | Store product with sufficient capacity | ✅ PASS |
| **TC5 - Over Capacity Handling** | Reject storage when capacity exceeded | ✅ PASS |
| **TC6 - Inventory Check** | Verify inventory reflects correct quantities | ✅ PASS |
| **TC7 - Order Creation (Valid)** | Create order with available stock | ✅ PASS |
| **TC8 - Order Creation (Insufficient Stock)** | Reject order when stock insufficient | ✅ PASS |
| **TC9 - Order Processing** | Process order and update inventory | ✅ PASS |

### 📋 Detailed Test Cases

#### TC1 - System Readiness Check
| Command | Output |
| :--- | :--- |
| `./check_network.sh` | Database connection successful |

#### TC2 - Configuration Validation
| Command | Output |
| :--- | :--- |
| `./check_system_configuration.sh` | Configuration is valid! |

#### TC3 - Configuration Sync
| Command | Output |
| :--- | :--- |
| `./sync_config_to_db.sh` | Configuration synchronized successfully |

#### TC4 - Product Receiving (Valid)
| Command | Output |
| :--- | :--- |
| `./receive_product.sh "Test Laptop" laptop 2` | Stored 2 items in RACK_A (2/10). Product stored with ID: P5696 |

#### TC5 - Over Capacity Handling
| Command | Output |
| :--- | :--- |
| `./receive_product.sh "Big Product" laptop 50` | Not enough space in RACK_A (Available: 8, Requested: 50). Product NOT stored. |

#### TC6 - Inventory Check
| Command | Output |
| :--- | :--- |
| `./check_inventory.sh` | Inventory report showing correct quantities per location |

#### TC7 - Order Creation (Valid)
| Command | Output |
| :--- | :--- |
| `./create_order.sh 1 P5696 1` | Order created: 1 (PENDING). Customer: 1, Product: P5696, Quantity: 1 |

#### TC8 - Order Creation (Insufficient Stock)
| Command | Output |
| :--- | :--- |
| `./create_order.sh 1 P5696 999` | Not enough stock |

#### TC9 - Order Processing
| Command | Output |
| :--- | :--- |
| `./process_order.sh 1` | Order 1 → SHIPPED |

### 📊 Validation Summary

| Category | Status |
| :--- | :--- |
| System Readiness | ✅ PASS |
| Configuration | ✅ PASS |
| Database Operations | ✅ PASS |
| Inventory Handling | ✅ PASS |
| Order Processing | ✅ PASS |
| Error Handling | ✅ PASS |

### ⚠️ Known Limitations

- Simplified warehouse model
- Each product type assigned to single location
- No concurrent user handling
- No advanced routing logic for order processing

> **Note:** All core functionalities behave as expected, and validation mechanisms ensure incorrect configurations and operations are properly handled.

---

## D11: Executive Summary

This document provides a high-level overview of the FlowCore Warehouse Simulation System, including system architecture, key functionalities, and testing outcomes.

### 📁 File

| File | Purpose |
| :--- | :--- |
| `D11_Executive_Summary.pdf` | Summarizes the project scope, architecture, functionality, and test results. |

### 🏗️ System Overview

The FlowCore Warehouse Simulation System simulates a configurable warehouse environment, enabling validation of real-world operations such as product storage, inventory tracking, and order processing.

### 🔧 Technology Stack

| Component | Technology | Role |
| :--- | :--- | :--- |
| **Database Layer** | Oracle Database | Data management & business logic |
| **Script Layer** | Bash scripts | Automation and orchestration |
| **Validation Module** | C++ | Configuration validation |
| **Execution Environment** | Docker | Consistent deployment |

> System behavior is controlled through external configuration, allowing flexibility without code changes.

### ✅ Key Functionalities

| Functionality | Description |
| :--- | :--- |
| Product receiving | With capacity validation |
| Inventory tracking | Automatic updates |
| Order creation | With stock checking |
| Order processing | Automatic inventory reduction |

### 🧪 Testing Results

| Test Category | Outcome |
| :--- | :--- |
| Product receiving (valid) | ✅ PASS |
| Product receiving (over-capacity) | ✅ PASS |
| Inventory management | ✅ PASS |
| Order creation (valid) | ✅ PASS |
| Order creation (insufficient stock) | ✅ PASS |
| Order processing | ✅ PASS |

### 📌 Conclusion

The system has been successfully tested under multiple scenarios, including both valid and invalid operations. All core functionalities behave as expected, and validation mechanisms ensure that incorrect configurations and operations are properly handled. The solution demonstrates a realistic and technically sound warehouse commissioning simulation.

> **Note:** This document serves as the final project summary for stakeholders and evaluators.

## D12: Final Team Presentation

This presentation provides a comprehensive overview of the FlowCore Warehouse Simulation System project.

### 📁 File

| File | Purpose |
| :--- | :--- |
| `D12-Final_Team_Presentation.pptx` | Final project presentation covering system design, implementation, and results. |

### 📑 Presentation Contents

| Section | Topics Covered |
| :--- | :--- |
| **Introduction** | Project objectives, scope, and assumptions |
| **System Architecture** | Component design, technology stack, data flow |
| **Configuration & Environment** | Business rules, database setup, validation |
| **Implementation** | Bash scripts, SQL operations, PL/SQL logic, C++ utilities |
| **Testing & Validation** | Test cases, results, and validation summary |
| **Demonstration** | Live system walkthrough and scenario execution |
| **Conclusion** | Achievements, limitations, and future work |

> **Note:** This presentation accompanies the final project delivery and demonstrates system functionality to stakeholders.
