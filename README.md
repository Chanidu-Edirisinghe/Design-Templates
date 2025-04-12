# OOP Template

A concise, practical template for converting a real-world scenario into OOP classes and methods, focusing on separating concerns and assigning actions to appropriate classes. A **Library Management System** is used as an example scenario to illustrate the process, but the template is generalizable.

---

### Template: Converting a Real-World Scenario to OOP

#### Step 1: Identify Key Entities

- Look for nouns in the scenario—these are potential classes representing real-world objects.
- Example: In a library, entities might be **User**, **Book**, **Loan**, and **Library**.

#### Step 2: Define Attributes and Basic Behaviors

- For each entity, list its properties (attributes) and what it can _do_ (methods).
- Keep methods focused on the entity’s core responsibilities.

#### Step 3: Separate Concerns

- Split responsibilities so each class handles one primary concern (e.g., data storage vs. business logic).
- Avoid overloading a single class with unrelated tasks.

#### Step 4: Create Manager/Service Classes

- For actions that involve multiple entities or complex logic, create separate “manager” or “service” classes.
- These coordinate interactions between entities.

#### Step 5: Assign Actions to Classes

- Match actions to the class that naturally “owns” them based on the entity’s role or the concern they address.
- If an action doesn’t fit, it likely belongs to a new manager class.

#### Step 6: Refine and Validate

- Ensure classes are cohesive (focused) and loosely coupled (minimal dependencies).
- Check if the design scales or adapts to new features.

---

### Example: Library Management System

#### Scenario

- Users borrow and return books from a library. Books have titles and availability status. Users can have overdue fines. The library tracks loans and notifies users about due dates.

#### Step 1: Identify Key Entities

- **User**: A person who borrows books.
- **Book**: An item that can be borrowed.
- **Loan**: A record of a book borrowed by a user.
- **Library**: The system managing everything.

#### Step 2: Define Attributes and Basic Behaviors

1. **User**
   - Attributes: `user_id`, `name`, `email`, `fines`
   - Methods: `pay_fine(amount)` (handles user-specific fine payment)
2. **Book**
   - Attributes: `book_id`, `title`, `is_available`
   - Methods: `set_availability(status)` (controls its own state)
3. **Loan**
   - Attributes: `loan_id`, `user_id`, `book_id`, `borrow_date`, `due_date`, `return_date`
   - Methods: `is_overdue()` (checks if loan is past due)

#### Step 3: Separate Concerns

- **User** shouldn’t manage loans—it’s about identity and fines.
- **Book** shouldn’t handle borrowing logic—just its own state.
- **Loan** tracks a single transaction, not the whole borrowing process.
- **Library** as a monolithic class would mix too many concerns (e.g., book inventory, user management, loan tracking).

#### Step 4: Create Manager/Service Classes

- **LibraryManager**: Coordinates borrowing, returning, and notifications.
  - Concern: High-level library operations.
- **FineManager**: Calculates and manages fines.
  - Concern: Fine-related logic, separate from user identity.

#### Step 5: Assign Actions to Classes

- **User Actions**:
  - “Pay a fine” → `User.pay_fine(amount)`
- **Book Actions**:
  - “Mark as available/unavailable” → `Book.set_availability(status)`
- **Loan Actions**:
  - “Check if overdue” → `Loan.is_overdue()`
- **LibraryManager Actions**:
  - “Borrow a book” → `LibraryManager.borrow_book(user_id, book_id)`
  - “Return a book” → `LibraryManager.return_book(loan_id)`
  - “Notify user of due date” → `LibraryManager.notify_user(loan_id)`
- **FineManager Actions**:
  - “Calculate overdue fine” → `FineManager.calculate_fine(loan_id)`
  - “Add fine to user” → `FineManager.add_fine_to_user(user_id, amount)`

#### Step 6: Refine and Validate

- **Cohesion**: `User` focuses on user data, `Book` on book state, `Loan` on loan details, `LibraryManager` on operations, `FineManager` on fines.
- **Coupling**: Classes interact via IDs or minimal data, not direct manipulation.
- **Scalability**: Add `Reservation` class later without breaking existing logic.

---

### Resulting Class Template (Pseudo-Code)

```python
class User:
    def __init__(self, user_id, name, email):
        self.user_id = user_id
        self.name = name
        self.email = email
        self.fines = 0
    def pay_fine(self, amount):
        self.fines -= amount

class Book:
    def __init__(self, book_id, title):
        self.book_id = book_id
        self.title = title
        self.is_available = True
    def set_availability(self, status):
        self.is_available = status

class Loan:
    def __init__(self, loan_id, user_id, book_id, borrow_date, due_date):
        self.loan_id = loan_id
        self.user_id = user_id
        self.book_id = book_id
        self.borrow_date = borrow_date
        self.due_date = due_date
        self.return_date = None
    def is_overdue(self):
        return self.return_date is None and current_date() > self.due_date

class LibraryManager:
    def borrow_book(self, user_id, book_id):
        # [To DB]: Check book availability, create Loan instance, update book status
        pass
    def return_book(self, loan_id):
        # [To DB]: Update Loan return_date, set book availability to True
        pass
    def notify_user(self, loan_id):
        # [Logic]: Send email reminder about due date
        pass

class FineManager:
    def calculate_fine(self, loan_id):
        # [Logic]: Calculate fine based on overdue days
        return amount
    def add_fine_to_user(self, user_id, amount):
        # [To DB]: Update user's fines
        pass
```

---

### General Tips

- **Entity Classes**: Handle data and simple state changes (e.g., `User`, `Book`).
- **Manager Classes**: Handle multi-entity logic or workflows (e.g., `LibraryManager`).
- **Specialized Classes**: Offload specific concerns (e.g., `FineManager`).
- **Actions**: Assign to the class owning the data or process; if unclear, it’s a sign a new class is needed.

This template keeps your OOP design clean and adaptable. Try applying it to a scenario you’re interested in—I can help refine it!

# DB Template

This repository provides a structured **template** for designing a conceptual database model for any system, along with three example applications: a zoo, a restaurant recipe system, and a library. The template helps identify a system's needs and define entities, attributes, and relationships to create an effective database design. Each example demonstrates how to apply the template to real-world scenarios, focusing on why each element is chosen and its practical impact.

The approach is based on a data engineering lecture (inspired by concepts like those in CM2606: Data Engineering) and ensures databases are logical, efficient, and tailored to operational goals.

---

## Database Design Template

The template guides you through designing a conceptual database by identifying what a system needs and building a model to support it. Each step has a clear **reason** (why it’s needed) and **effect** (how it helps), ensuring the design is practical and complete.

### 1. System Overview

- **Purpose**: Describe the system’s primary goal in 1–2 sentences.
- **Reason**: Clarifies what the system aims to achieve.
- **Effect**: Focuses the database on supporting core operations.
- **Prompt**: What does the system do? Who uses it, and for what?

### 2. Identify System Needs

- **Operational Needs**: List 3–5 key functions or processes (e.g., tracking, managing, reporting).
- **Data Requirements**: Specify what data each function needs (e.g., items, people, transactions).
- **Reason**: Ensures the database captures all necessary data for the system’s tasks.
- **Effect**: Links system activities to data, avoiding gaps or irrelevant elements.
- **Prompt**:
  - What tasks does the system perform daily?
  - What information is required for those tasks?
  - Are there specific outputs (e.g., reports, displays)?

### 3. Define Entities

- **Entities**: List 3–5 main objects or concepts to track (e.g., Customer, Product).
- **Description**: Explain what each entity represents.
- **Reason**: Entities are the system’s core components, covering all key elements.
- **Effect**: Organizes data logically for easy tracking and retrieval.
- **Prompt**:
  - What are the main objects or people involved?
  - What needs to be uniquely identified?

### 4. Assign Attributes

- **Attributes per Entity**: List 4–6 details for each entity (e.g., Name, ID, Quantity).
- **Purpose of Attribute**: Explain why each is needed and how it supports functions.
- **Reason**: Attributes provide specific data for tasks, ensuring functionality.
- **Effect**: Enables actions like tracking or reporting with accurate data.
- **Prompt**:
  - What details are necessary for this entity?
  - How will each detail be used (e.g., identification, calculations)?
  - Are there constraints (e.g., unique IDs)?

### 5. Establish Relationships

- **Relationships**: Identify 2–4 connections between entities (e.g., one-to-many).
- **Nature of Relationship**: Describe how entities interact and the direction.
- **Purpose**: Explain why the relationship supports operations.
- **Reason**: Relationships reflect real-world interactions, enabling data linkage.
- **Effect**: Supports queries and processes by connecting data efficiently.
- **Prompt**:
  - How do entities depend on or interact with each other?
  - What processes require these connections?
  - Are there limits (e.g., one entity linked to one other)?

### 6. Validate the Model

- **Check Against Needs**: Confirm entities, attributes, and relationships meet all needs.
- **Gaps or Redundancies**: Identify missing or unnecessary elements.
- **Reason**: Ensures the model is complete and efficient.
- **Effect**: Produces a practical database that fully supports the system.
- **Prompt**:
  - Does every need have corresponding data?
  - Can relationships support processes?
  - Is anything included that’s not needed?

---

## Example 1: Zoo Database

### 1. System Overview

- **Purpose**: Manage animals, enclosures, and staff to ensure animal welfare and efficient zoo operations.
- **Reason/Effect**: Clarifies that the database must track care and logistics, ensuring animals are healthy and visitors are satisfied.

### 2. Identify System Needs

- **Operational Needs**:
  - Track animal health and details.
  - Manage enclosure assignments.
  - Assign keepers to animals.
- **Data Requirements**:
  - Animal species, health status; enclosure types, locations; keeper contact info.
- **Reason/Effect**: Ensures the database supports care, housing, and staffing, preventing issues like overcrowding or neglect.

### 3. Define Entities

- **Animal**: Represents creatures in the zoo.
- **Enclosure**: Represents habitats for animals.
- **Keeper**: Represents staff caring for animals.
- **Reason/Effect**: These entities cover the zoo’s core components, enabling tracking of animals and operations.

### 4. Assign Attributes

- **Animal**:
  - AnimalID (unique; tracks individuals; effect: no confusion).
  - Species (e.g., Lion; identifies type; effect: proper care).
  - Name (e.g., Simba; personalizes; effect: staff recognition).
  - BirthDate (e.g., 2020-03-15; tracks age; effect: breeding plans).
  - Gender (e.g., Male; supports programs; effect: population management).
  - HealthStatus (e.g., Healthy; monitors welfare; effect: timely treatment).
- **Enclosure**:
  - EnclosureID (unique; tracks habitats; effect: clear records).
  - Type (e.g., Savanna; ensures suitability; effect: animal safety).
  - Capacity (e.g., 5; prevents overcrowding; effect: comfort).
  - Location (e.g., North Wing; aids navigation; effect: efficiency).
- **Keeper**:
  - KeeperID (unique; tracks staff; effect: accurate assignments).
  - FirstName (e.g., Jane; identifies; effect: communication).
  - LastName (e.g., Doe; completes identity; effect: records).
  - ContactNumber (e.g., 555-1234; reaches staff; effect: emergencies).
- **Reason/Effect**: Attributes provide details for care and operations, like knowing when an animal needs a vet.

### 5. Establish Relationships

- **Animal–Enclosure**: One Enclosure houses many Animals; one Animal lives in one Enclosure.
  - **Purpose**: Ensures animals are in suitable habitats (effect: proper care, e.g., lions in savanna).
- **Animal–Keeper**: One Keeper cares for many Animals; one Animal is cared for by one Keeper.
  - **Purpose**: Assigns clear responsibilities (effect: consistent feeding, health checks).
- **Reason/Effect**: Links data to reflect real operations, enabling efficient care and management.

### 6. Validate the Model

- **Check**: Covers animal care, enclosure management, and keeper assignments.
- **Gaps**: None—attributes like HealthStatus support welfare; relationships ensure housing.
- **Reason/Effect**: Confirms the database supports all zoo needs, ensuring welfare and smooth operations.

---

## Example 2: Restaurant Recipe Database

### 1. System Overview

- **Purpose**: Manage recipes, ingredients, and menus to ensure consistent dishes and cost control.
- **Reason/Effect**: Focuses the database on cooking and inventory, ensuring quality food and profitability.

### 2. Identify System Needs

- **Operational Needs**:
  - Store and retrieve recipes.
  - Track ingredient inventory.
  - Organize menus for service.
- **Data Requirements**:
  - Recipe details, instructions; ingredient quantities, costs; menu schedules.
- **Reason/Effect**: Ensures the database supports cooking, stocking, and menu planning, avoiding shortages or inconsistencies.

### 3. Define Entities

- **Recipe**: Represents dishes served.
- **Ingredient**: Represents items used in recipes.
- **Menu**: Represents collections of recipes offered.
- **Reason/Effect**: Covers the core elements of restaurant operations, organizing data for kitchen and service.

### 4. Assign Attributes

- **Recipe**:
  - RecipeID (unique; tracks dishes; effect: no mix-ups).
  - Name (e.g., Margherita Pizza; identifies; effect: menu clarity).
  - Category (e.g., Main Course; groups dishes; effect: easy selection).
  - PreparationTime (e.g., 30 min; plans kitchen; effect: timing).
  - ServingSize (e.g., 2 people; scales portions; effect: consistency).
  - Instructions (e.g., “Knead dough”; guides chefs; effect: same taste).
- **Ingredient**:
  - IngredientID (unique; tracks items; effect: clear inventory).
  - Name (e.g., Tomato; specifies; effect: correct orders).
  - Unit (e.g., Kilogram; measures; effect: accurate stocking).
  - StockQuantity (e.g., 10 kg; monitors supply; effect: no shortages).
  - CostPerUnit (e.g., $2/kg; tracks expenses; effect: budgeting).
- **Menu**:
  - MenuID (unique; tracks menus; effect: clear offerings).
  - Name (e.g., Lunch Menu; identifies; effect: customer choice).
  - StartDate (e.g., 2025-04-01; sets period; effect: timely menus).
  - EndDate (e.g., 2025-04-30; limits scope; effect: seasonal updates).
- **Reason/Effect**: Attributes enable cooking consistency, inventory control, and menu planning.

### 5. Establish Relationships

- **Recipe–Ingredient**: One Recipe uses many Ingredients; one Ingredient is used in many Recipes.
  - **Purpose**: Links dishes to supplies (effect: chefs know needs, e.g., flour for pizza).
- **Recipe–Menu**: One Menu includes many Recipes; one Recipe can be in many Menus.
  - **Purpose**: Organizes offerings (effect: flexible menus, e.g., pizza in lunch and dinner).
- **Reason/Effect**: Relationships ensure recipes are prepared and served correctly, streamlining operations.

### 6. Validate the Model

- **Check**: Covers recipes, inventory, and menus.
- **Gaps**: None—StockQuantity prevents shortages; relationships support menu updates.
- **Reason/Effect**: Confirms the database meets all restaurant needs, ensuring efficiency and customer satisfaction.

---

## Example 3: Library Database

### 1. System Overview

- **Purpose**: Manage books, borrowers, and loans to ensure efficient library operations and user satisfaction.
- **Reason/Effect**: Focuses the database on lending and tracking, ensuring books are available and loans are managed.

### 2. Identify System Needs

- **Operational Needs**:
  - Track book availability.
  - Manage borrower information.
  - Record loans and returns.
- **Data Requirements**:
  - Book titles, status; borrower contacts; loan dates.
- **Reason/Effect**: Ensures the database supports lending and tracking, preventing issues like lost books.

### 3. Define Entities

- **Book**: Represents items in the library.
- **Borrower**: Represents users borrowing books.
- **Loan**: Represents borrowing transactions.
- **Reason/Effect**: Covers the core elements of lending, organizing data for library functions.

### 4. Assign Attributes

- **Book**:
  - BookID (unique; tracks books; effect: no confusion).
  - Title (e.g., “1984”; identifies; effect: user searches).
  - Author (e.g., Orwell; describes; effect: cataloging).
  - Status (e.g., Available; shows availability; effect: lending decisions).
- **Borrower**:
  - BorrowerID (unique; tracks users; effect: clear records).
  - Name (e.g., John Smith; identifies; effect: communication).
  - Contact (e.g., john@email.com; reaches user; effect: reminders).
- **Loan**:
  - LoanID (unique; tracks transactions; effect: accurate records).
  - BookID (links book; effect: knows what’s borrowed).
  - BorrowerID (links user; effect: knows who borrowed).
  - DueDate (e.g., 2025-04-25; tracks return; effect: loan management).
- **Reason/Effect**: Attributes provide details for lending, ensuring the system tracks books and users.

### 5. Establish Relationships

- **Book–Loan**: One Book has many Loans; one Loan involves one Book.
  - **Purpose**: Tracks which books are borrowed (effect: accurate availability).
- **Borrower–Loan**: One Borrower has many Loans; one Loan belongs to one Borrower.
  - **Purpose**: Links users to loans (effect: clear responsibility).
- **Reason/Effect**: Relationships connect data, enabling the system to manage lending efficiently.

### 6. Validate the Model

- **Check**: Covers book tracking, borrower management, and loans.
- **Gaps**: None—DueDate supports returns; relationships ensure tracking.
- **Reason/Effect**: Confirms the database meets all library needs, ensuring smooth operations.

---

## How to Use This Template

1. **Define the System**: Start with the overview to understand the goal (e.g., zoo welfare).
2. **List Needs**: Identify key tasks and data (e.g., track animal health).
3. **Choose Entities**: Pick core objects (e.g., Animal, Enclosure).
4. **Add Attributes**: Specify details for each entity (e.g., HealthStatus).
5. **Define Relationships**: Connect entities based on interactions (e.g., Animal–Enclosure).
6. **Validate**: Ensure the model covers all needs without extras.

This template is adaptable to any system, from hospitals to e-commerce, ensuring a clear, functional database design.

---
