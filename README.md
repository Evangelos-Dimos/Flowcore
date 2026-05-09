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
