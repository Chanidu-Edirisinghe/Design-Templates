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
