# Inventory Management Requirements Specification

## 1. Introduction
This document outlines the comprehensive requirements for the Inventory Management System (IMS) designed to manage products, categories, and stock movements effectively. The system will enable efficient inventory control across different departments and user roles within an organization.

## 2. Functional Requirements

### 2.1 Product Management
**Requirement ID: FR-2.1**
- **Add Product**: Ability to add a new product with the following attributes:
  - Product Name (required, string, max 255 characters)
  - Category (required, reference to Category)
  - Storage Location (required, reference to StorageLocation)
  - Quantity (required, integer, >= 0)
  - Minimum Quantity (required, integer, >= 0)
  - Status (required, enum: Available, Unavailable)
  - Description (optional, text)
  - SKU/Item Code (optional, string)
  - Date Added (auto-generated timestamp)
  
- **Edit Product**: Ability to modify existing products (Admin only).
- **Delete Product**: Removal of products from the system (Admin only).
- **View Products**: Display product list with filtering and search capabilities.
- **Product Details**: View detailed information for each product including history.

### 2.2 Category Management
**Requirement ID: FR-2.2**
- **Create Category**: Admin can create new categories with the following attributes:
  - Category Name (required, string, max 100 characters, unique)
  - Description (optional, text)
  - Date Created (auto-generated timestamp)
  
- Predefined categories: Büro, Elektronik, Werkzeuge
- **Edit Category**: Modify existing categories (Admin only).
- **Delete Category**: Remove categories (Admin only, only if no products are assigned).
- **View Categories**: Display all available categories.

### 2.3 Storage Location Management
**Requirement ID: FR-2.3**
- **Add Storage Location**: Admin can add new storage locations with the following attributes:
  - Location Name (required, string, max 100 characters)
  - Location Type (required, enum: Room, Shelf, Cabinet)
  - Room (optional, reference to another location)
  - Shelf (optional, reference to another location)
  - Description (optional, text)
  - Date Created (auto-generated timestamp)
  
- **Edit Storage Location**: Modify existing storage locations (Admin only).
- **Delete Storage Location**: Remove locations (Admin only, only if no products are stored there).
- **View Locations**: Display all storage locations in a hierarchical structure.

### 2.4 Stock Movement Tracking
**Requirement ID: FR-2.4**
- **Track Incoming Stock**: Record incoming inventory with the following details:
  - Product (required, reference to Product)
  - Quantity (required, integer, > 0)
  - Date (required, timestamp)
  - Notes (optional, text)
  - Movement Type = "Incoming"
  
- **Track Outgoing Stock**: Record outgoing inventory with details:
  - Product (required, reference to Product)
  - Quantity (required, integer, > 0)
  - Date (required, timestamp)
  - Reason (optional, enum: Sale, Usage, Disposal)
  - Notes (optional, text)
  - Movement Type = "Outgoing"
  
- **Borrowing Items**: Employees can borrow products with the following details:
  - Product (required, reference to Product)
  - Quantity (required, integer, > 0)
  - Borrower (required, reference to User)
  - Borrow Date (auto-generated timestamp)
  - Expected Return Date (required, date)
  - Movement Type = "Borrow"
  
- **Returning Items**: Track returned borrowed items:
  - Reference to original Borrow movement
  - Quantity (required, integer, > 0)
  - Return Date (auto-generated timestamp)
  - Condition (optional, enum: Good, Damaged, Lost)
  - Notes (optional, text)
  - Movement Type = "Return"
  
- **Movement History**: Maintain complete audit trail of all stock movements.

### 2.5 Low-Stock Warnings
**Requirement ID: FR-2.5**
- **Automatic Warnings**: System automatically generates warnings when quantity <= min_quantity.
- **Warning Dashboard**: Display products with low stock on a dedicated dashboard.
- **Email Notifications**: Send email notifications to Admin when stock falls below minimum.
- **Threshold Management**: Admin can set/modify minimum quantity thresholds for each product.

### 2.6 Data Export
**Requirement ID: FR-2.6**
- **CSV Export**: Admin can export the following data in CSV format:
  - Product inventory list with quantities and storage locations
  - Stock movement history with filtering by date range
  - Category and location inventory summary
- **Export Filtering**: Ability to filter export data by:
  - Category
  - Storage Location
  - Date range
  - Status
- **Export Headers**: Include descriptive column headers in exported files.

## 3. Non-Functional Requirements

### 3.1 Performance
**Requirement ID: NFR-3.1**
- System should handle up to 1000 products without performance degradation.
- Page load time should not exceed 2 seconds.
- Search and filter operations should complete within 1 second.
- CSV export should complete within 5 seconds for up to 10,000 records.

### 3.2 Scalability
**Requirement ID: NFR-3.2**
- System architecture should support adding more categories and storage locations.
- Database should be capable of handling millions of stock movement records.
- Support for future multi-location/multi-site expansion.

### 3.3 Security
**Requirement ID: NFR-3.3**
- User authentication via secure login mechanism (e.g., password hashing with bcrypt).
- Role-based access control (RBAC) to restrict actions based on user roles.
- All sensitive data should be encrypted in transit (HTTPS).
- Audit logging of all administrative actions and data modifications.
- Session management with automatic timeout after 30 minutes of inactivity.

### 3.4 Usability
**Requirement ID: NFR-3.4**
- User-friendly interface with clear navigation.
- Responsive design supporting desktop and mobile devices.
- Intuitive forms with validation error messages.
- Help documentation available within the application.
- Consistent terminology and design patterns throughout.

### 3.5 Reliability
**Requirement ID: NFR-3.5**
- System availability: 99% uptime (excluding maintenance windows).
- Data backup: Daily automated backups with disaster recovery plan.
- Error handling and graceful degradation.

### 3.6 Maintainability
**Requirement ID: NFR-3.6**
- Code should follow established coding standards and conventions.
- Comprehensive logging for debugging and monitoring.
- Clear documentation of system architecture and API endpoints.

## 4. User Roles and Permissions

### 4.1 Admin Role
**Permissions:**
- Create, read, update, delete products
- Create, read, update, delete categories
- Create, read, update, delete storage locations
- View all stock movements
- Record incoming stock
- Record outgoing stock
- Manage user accounts
- Export data to CSV
- Set low-stock thresholds
- View audit logs
- System configuration

### 4.2 Mitarbeiter (Employee) Role
**Permissions:**
- View product list and details
- View categories and storage locations
- View own borrowed items and return history
- Borrow products (if quantity >= 1 and quantity <= available quantity)
- Return borrowed products
- View low-stock warnings (read-only)

**Restrictions:**
- Cannot create, edit, or delete products
- Cannot manage categories or storage locations
- Cannot record incoming/outgoing stock
- Cannot export data
- Cannot access admin functions
- Cannot view other users' borrowing history

## 5. Use Cases

### 5.1 Use Case: Add a New Product
**Use Case ID: UC-1**
- **Actor**: Admin
- **Precondition**: Admin is logged into the system with valid credentials.
- **Postcondition**: New product is added to inventory and visible in product list.
- **Main Flow**:
  1. Admin navigates to Product Management section.
  2. Admin clicks "Add Product" button.
  3. System displays product creation form.
  4. Admin enters product details (name, category, location, quantity, min_quantity, status).
  5. Admin submits the form.
  6. System validates input data.
  7. System stores product in database.
  8. System displays success confirmation message.
  9. System redirects to product details page.
- **Alternative Flows**:
  - If required fields are missing, system displays validation errors and prompts for correction.
  - If product name already exists, system warns user and prompts for unique name.

### 5.2 Use Case: Edit Product Details
**Use Case ID: UC-2**
- **Actor**: Admin
- **Precondition**: Admin is logged in; product exists in the system.
- **Postcondition**: Product information is updated and changes are reflected in the system.
- **Main Flow**:
  1. Admin navigates to product list.
  2. Admin selects a product to edit.
  3. System displays product details form with current data.
  4. Admin modifies one or more fields.
  5. Admin submits the form.
  6. System validates changes.
  7. System updates product in database.
  8. System displays success message.
  9. System logs this administrative action.
- **Alternative Flows**:
  - If validation fails, system displays error messages.

### 5.3 Use Case: Create New Category
**Use Case ID: UC-3**
- **Actor**: Admin
- **Precondition**: Admin is logged in.
- **Postcondition**: New category is created and available for product assignment.
- **Main Flow**:
  1. Admin navigates to Category Management.
  2. Admin clicks "Create Category" button.
  3. System displays category creation form.
  4. Admin enters category name and description.
  5. Admin submits the form.
  6. System validates input (unique name).
  7. System creates category in database.
  8. System displays success confirmation.
- **Alternative Flows**:
  - If category name already exists, system prompts user to choose a different name.

### 5.4 Use Case: Add Storage Location
**Use Case ID: UC-4**
- **Actor**: Admin
- **Precondition**: Admin is logged in.
- **Postcondition**: New storage location is available for product assignment.
- **Main Flow**:
  1. Admin navigates to Storage Location Management.
  2. Admin clicks "Add Location" button.
  3. System displays location creation form.
  4. Admin selects location type (Room, Shelf, Cabinet).
  5. Admin enters location name and description.
  6. Admin submits the form.
  7. System validates input.
  8. System creates location in database.
  9. System displays success confirmation.

### 5.5 Use Case: Record Incoming Stock
**Use Case ID: UC-5**
- **Actor**: Admin
- **Precondition**: Admin is logged in; product exists in system.
- **Postcondition**: Stock quantity is increased; movement record is created.
- **Main Flow**:
  1. Admin navigates to Stock Management.
  2. Admin clicks "Record Incoming Stock".
  3. System displays incoming stock form.
  4. Admin selects product and enters quantity.
  5. Admin enters optional notes.
  6. Admin submits the form.
  7. System validates quantity (> 0).
  8. System increases product quantity in database.
  9. System creates stock movement record.
  10. System logs this transaction.
  11. System displays success confirmation.

### 5.6 Use Case: Record Outgoing Stock
**Use Case ID: UC-6**
- **Actor**: Admin
- **Precondition**: Admin is logged in; product has sufficient quantity.
- **Postcondition**: Stock quantity is decreased; movement record is created.
- **Main Flow**:
  1. Admin navigates to Stock Management.
  2. Admin clicks "Record Outgoing Stock".
  3. System displays outgoing stock form.
  4. Admin selects product and enters quantity.
  5. Admin enters reason (Sale, Usage, Disposal).
  6. Admin enters optional notes.
  7. Admin submits the form.
  8. System validates quantity (> 0 and <= available quantity).
  9. System decreases product quantity in database.
  10. System creates stock movement record.
  11. System checks if quantity is now below min_quantity threshold.
  12. If below threshold, system triggers low-stock warning.
  13. System logs this transaction.
  14. System displays success confirmation.

### 5.7 Use Case: Borrow Product
**Use Case ID: UC-7**
- **Actor**: Mitarbeiter (Employee)
- **Precondition**: Employee is logged in; product has available quantity; expected return date is in the future.
- **Postcondition**: Product is marked as borrowed; stock quantity is reduced; borrow record is created.
- **Main Flow**:
  1. Employee navigates to product list.
  2. Employee selects a product with status "Available".
  3. System displays product details with "Borrow" button.
  4. Employee clicks "Borrow".
  5. System displays borrowing form with:
     - Product name (pre-filled)
     - Quantity field (default 1)
     - Expected return date field (required)
  6. Employee enters quantity and expected return date.
  7. Employee submits the form.
  8. System validates:
     - Quantity > 0
     - Quantity <= available quantity
     - Expected return date is in the future
  9. System reduces product quantity.
  10. System creates "Borrow" movement record.
  11. System logs this action.
  12. System displays success confirmation.
  13. System notifies Admin of the borrowing action.

### 5.8 Use Case: Return Borrowed Product
**Use Case ID: UC-8**
- **Actor**: Mitarbeiter (Employee)
- **Precondition**: Employee is logged in; employee has borrowed items.
- **Postcondition**: Product is marked as returned; stock quantity is restored; return record is created.
- **Main Flow**:
  1. Employee navigates to "My Borrowed Items".
  2. System displays list of items borrowed by employee.
  3. Employee selects an item to return.
  4. System displays return form with:
     - Product name (pre-filled)
     - Quantity to return field (pre-filled with borrowed quantity)
     - Condition field (dropdown: Good, Damaged, Lost)
     - Optional notes field
  5. Employee enters condition and optional notes.
  6. Employee submits the form.
  7. System validates quantity (> 0 and <= borrowed quantity).
  8. System increases product quantity.
  9. System updates borrow record with return information.
  10. System creates "Return" movement record.
  11. System logs this action.
  12. System displays success confirmation.
  13. If condition is "Damaged" or "Lost", system notifies Admin.

### 5.9 Use Case: View Low-Stock Warning Dashboard
**Use Case ID: UC-9**
- **Actor**: Admin
- **Precondition**: Admin is logged in; at least one product has quantity <= min_quantity.
- **Postcondition**: Dashboard displays all low-stock items.
- **Main Flow**:
  1. Admin navigates to Dashboard or Low-Stock Warnings section.
  2. System queries all products where quantity <= min_quantity.
  3. System displays list of low-stock products with:
     - Product name
     - Current quantity
     - Minimum quantity
     - Category
     - Storage location
     - Difference (how many units below minimum)
  4. Admin can click on a product to view details or record incoming stock.
  5. Admin can export the low-stock list to CSV.

### 5.10 Use Case: Export Inventory to CSV
**Use Case ID: UC-10**
- **Actor**: Admin
- **Precondition**: Admin is logged in.
- **Postcondition**: CSV file with inventory data is generated and downloaded.
- **Main Flow**:
  1. Admin navigates to "Export Data" section.
  2. System displays export options form with:
     - Export type dropdown (Products, Stock Movements, etc.)
     - Category filter (optional)
     - Location filter (optional)
     - Date range filter (optional, for stock movements)
  3. Admin selects export options and filters.
  4. Admin clicks "Generate Export".
  5. System generates CSV file with selected data.
  6. System includes descriptive headers.
  7. System initiates file download.
  8. System logs this export action.

### 5.11 Use Case: View Stock Movement History
**Use Case ID: UC-11**
- **Actor**: Admin, Mitarbeiter (for own movements only)
- **Precondition**: User is logged in.
- **Postcondition**: Stock movement history is displayed.
- **Main Flow**:
  1. User navigates to Stock Movement History.
  2. System displays list of all movements (or user's movements for Mitarbeiter).
  3. System shows:
     - Movement type (Incoming, Outgoing, Borrow, Return)
     - Product name
     - Quantity
     - Date/timestamp
     - User who performed action (for Admin view)
     - Notes
  4. User can filter by:
     - Product
     - Movement type
     - Date range
     - User (Admin only)
  5. User can search for specific movements.
  6. System displays results with pagination.

## 6. Data Entity Definitions

### 6.1 Product Entity
**Entity ID: E-1**
**Description**: Represents a single item in inventory.

**Attributes**:
| Attribute | Type | Required | Unique | Max Length | Notes |
|-----------|------|----------|--------|-----------|-------|
| Product ID | UUID | Yes | Yes | - | Auto-generated primary key |
| Name | String | Yes | No | 255 | Product name |
| Category ID | FK | Yes | No | - | Reference to Category |
| Storage Location ID | FK | Yes | No | - | Reference to StorageLocation |
| Quantity | Integer | Yes | No | - | Current stock quantity, >= 0 |
| Min Quantity | Integer | Yes | No | - | Minimum acceptable quantity |
| Status | Enum | Yes | No | - | Values: Available, Unavailable |
| Description | Text | No | No | 1000 | Optional product description |
| SKU | String | No | Yes | 50 | Stock Keeping Unit |
| Date Created | Timestamp | Yes | No | - | Auto-generated creation date |
| Date Modified | Timestamp | Yes | No | - | Auto-updated on modification |
| Created By | FK | Yes | No | - | Reference to User who created |
| Modified By | FK | No | No | - | Reference to User who last modified |

---

### 6.2 Category Entity
**Entity ID: E-2**
**Description**: Represents a type/classification of products.

**Attributes**:
| Attribute | Type | Required | Unique | Max Length | Notes |
|-----------|------|----------|--------|-----------|-------|
| Category ID | UUID | Yes | Yes | - | Auto-generated primary key |
| Name | String | Yes | Yes | 100 | Category name (e.g., Büro, Elektronik, Werkzeuge) |
| Description | Text | No | No | 500 | Optional category description |
| Date Created | Timestamp | Yes | No | - | Auto-generated creation date |
| Created By | FK | Yes | No | - | Reference to User who created |

---

### 6.3 StorageLocation Entity
**Entity ID: E-3**
**Description**: Represents a physical location where products are stored.

**Attributes**:
| Attribute | Type | Required | Unique | Max Length | Notes |
|-----------|------|----------|--------|-----------|-------|
| Location ID | UUID | Yes | Yes | - | Auto-generated primary key |
| Name | String | Yes | No | 100 | Location name |
| Type | Enum | Yes | No | - | Values: Room, Shelf, Cabinet |
| Parent Location ID | FK | No | No | - | Reference to parent location (for hierarchical structure) |
| Description | Text | No | No | 500 | Optional description |
| Date Created | Timestamp | Yes | No | - | Auto-generated creation date |
| Created By | FK | Yes | No | - | Reference to User who created |

---

### 6.4 StockMovement Entity
**Entity ID: E-4**
**Description**: Represents a stock movement transaction (incoming, outgoing, borrow, return).

**Attributes**:
| Attribute | Type | Required | Unique | Max Length | Notes |
|-----------|------|----------|--------|-----------|-------|
| Movement ID | UUID | Yes | Yes | - | Auto-generated primary key |
| Product ID | FK | Yes | No | - | Reference to Product |
| Movement Type | Enum | Yes | No | - | Values: Incoming, Outgoing, Borrow, Return |
| Quantity | Integer | Yes | No | - | Quantity moved, > 0 |
| Date | Timestamp | Yes | No | - | Date/time of movement |
| Notes | Text | No | No | 1000 | Optional notes about movement |
| User ID | FK | Yes | No | - | Reference to User performing action |
| Reason | Enum | No | No | - | For Outgoing: Sale, Usage, Disposal |
| Condition | Enum | No | No | - | For Return: Good, Damaged, Lost |
| Related Movement ID | FK | No | No | - | For Return: Reference to original Borrow movement |
| Borrow Date | Timestamp | No | No | - | For Borrow: Date item was borrowed |
| Expected Return Date | Date | No | No | - | For Borrow: Expected return date |
| Actual Return Date | Timestamp | No | No | - | For Return: Actual return timestamp |
| Date Created | Timestamp | Yes | No | - | Auto-generated creation date |

---

### 6.5 User Entity
**Entity ID: E-5**
**Description**: Represents a system user with role-based access.

**Attributes**:
| Attribute | Type | Required | Unique | Max Length | Notes |
|-----------|------|----------|--------|-----------|-------|
| User ID | UUID | Yes | Yes | - | Auto-generated primary key |
| Username | String | Yes | Yes | 50 | Unique username for login |
| Email | String | Yes | Yes | 255 | User email address |
| Password Hash | String | Yes | No | 255 | Bcrypt hashed password |
| First Name | String | No | No | 100 | User's first name |
| Last Name | String | No | No | 100 | User's last name |
| Role | Enum | Yes | No | - | Values: Admin, Mitarbeiter |
| Status | Enum | Yes | No | - | Values: Active, Inactive, Suspended |
| Last Login | Timestamp | No | No | - | Timestamp of last login |
| Date Created | Timestamp | Yes | No | - | Auto-generated creation date |
| Date Modified | Timestamp | Yes | No | - | Auto-updated on modification |
| Created By | FK | Yes | No | - | Reference to User who created |
| Modified By | FK | No | No | - | Reference to User who last modified |

---

## 7. Constraints and Business Rules

### 7.1 Product Constraints
- A product must have a valid category assigned.
- A product must have a valid storage location assigned.
- A product's quantity cannot be negative.
- A product's minimum quantity cannot be greater than its current quantity (business logic constraint).
- Product names must be unique within the system.
- A product cannot be deleted if stock movements are associated with it (soft delete recommended).

### 7.2 Category Constraints
- Category names must be unique.
- A category cannot be deleted if products are assigned to it.
- Predefined categories (Büro, Elektronik, Werkzeuge) should be created during system initialization.

### 7.3 Storage Location Constraints
- Storage location names must be unique.
- A location cannot be deleted if products are stored there.
- Hierarchical structure: A shelf must have a parent room; a cabinet may have a parent room or shelf.

### 7.4 Stock Movement Constraints
- All movements must increase or decrease product quantity accordingly.
- Outgoing movements cannot exceed available quantity.
- Borrow movements create a pending return obligation.
- Return movements must match or be less than borrowed quantity.
- All movements must be immutable (cannot be edited, only logged).

### 7.5 User Constraints
- Usernames must be unique and case-insensitive.
- Email addresses must be unique and valid format.
- Passwords must meet complexity requirements (minimum 8 characters, mix of uppercase, lowercase, numbers, special characters).
- At least one Admin user must exist in the system at all times.

## 8. System Architecture Considerations

### 8.1 Technology Stack (Recommended)
- **Backend**: Python (Flask/FastAPI), Node.js (Express), or similar
- **Database**: PostgreSQL or MySQL for relational data storage
- **Frontend**: React, Vue.js, or Angular for web interface
- **Authentication**: JWT tokens with secure session management
- **File Storage**: Local filesystem or cloud storage (S3) for CSV exports

### 8.2 API Endpoints (Sample)
- `GET /api/products` - List all products
- `POST /api/products` - Create new product
- `GET /api/products/{id}` - Get product details
- `PUT /api/products/{id}` - Update product
- `DELETE /api/products/{id}` - Delete product
- `POST /api/stock-movements` - Record stock movement
- `GET /api/stock-movements` - List stock movements
- `POST /api/export/csv` - Generate CSV export
- `GET /api/warnings/low-stock` - Get low-stock warnings

## 9. Future Enhancements (Out of Scope)

- Multi-location/multi-site support with central inventory management
- Barcode/QR code scanning for faster product operations
- Integration with external suppliers for automated ordering
- Analytics dashboard with trend analysis and forecasting
- Mobile application for borrowing on the go
- Automated email reminders for pending returns
- Integration with accounting system for cost tracking
- Role-based customization (create custom roles)
- Webhook support for third-party integrations

## 10. Glossary

- **SKU**: Stock Keeping Unit - A unique identifier for a product
- **RBAC**: Role-Based Access Control - Security model restricting system access based on user roles
- **CSV**: Comma-Separated Values - File format for data export
- **Audit Log**: Record of all system actions and changes for security and compliance
- **Threshold**: Minimum quantity level that triggers a low-stock warning

## 11. Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-03-12 | Nataliia Zvarych | Initial complete requirements specification |

---

**Document Status**: APPROVED FOR DEVELOPMENT  
**Last Updated**: 2026-03-12  
**Next Review Date**: Upon completion of initial implementation