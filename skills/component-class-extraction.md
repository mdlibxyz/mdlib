---
name: Component & Class Extraction Skill
description: Guidance for identifying and extracting cohesive components and classes from monolithic code structures using abstraction and SOLID principles.
type: skill
platform: other
category: Development
recommendedLLMs:
  - claude-3.5-sonnet
  - gpt-4o
tags:
  - refactoring
  - code-quality
  - SOLID
  - architecture
  - abstraction
  - component-design
version: 1.0.0
---

# Component & Class Extraction Skill

## Overview
This skill guides the Refactorer subagent through the process of identifying and extracting cohesive components and classes from monolithic code structures. It's essential for improving maintainability, testability, and adhering to SOLID principles.

## Skill Categories

### 1. **Identification Patterns**

#### When to Extract a Component/Class
Extract when code exhibits one or more of these characteristics:

- **Multiple Responsibilities:** The entity handles unrelated concerns (authentication, persistence, UI rendering)
- **High Cohesion Needed:** Code naturally groups into logical subsystems
- **Reusability Potential:** Logic is duplicated or could be shared across modules
- **Testing Challenges:** Difficult to unit test due to tight coupling
- **Size Bloat:** Class exceeds 200 lines or has 15+ methods
- **Cyclical Dependencies:** Circular references suggest boundaries need redrawing

#### Anti-Patterns to Recognize
- Large Classes: Large classes doing everything
- Feature Envy: Classes heavily using another class's internals
- Data Clumps: Groups of variables always appearing together
- Long Parameter Lists: Methods with 5+ parameters
- Comments Explaining Logic: Complex code needing extensive comments suggests extraction

---

### 2. **Extraction Strategies**

#### Strategy A: Extract by Responsibility (SRP)
**When:** A class/component handles multiple unrelated concerns

**Process:**
1. Identify distinct responsibility clusters within the entity
2. Group methods and fields by responsibility
3. Create new classes/components for each responsibility
4. Update dependencies and inject extracted components
5. Validate extracted entities have single, clear purpose

**Example:**
```typescript
// BEFORE: UserManager handles auth, persistence, notifications
class UserManager {
  authenticate(username, password) { /* ... */ }
  validatePassword(pwd) { /* ... */ }
  saveUser(user) { /* ... */ }
  loadUser(id) { /* ... */ }
  sendWelcomeEmail(user) { /* ... */ }
  notifyAdmins(action) { /* ... */ }
}

// AFTER: Extracted responsibilities
class AuthService {
  authenticate(username, password) { /* ... */ }
  validatePassword(pwd) { /* ... */ }
}

class UserRepository {
  saveUser(user) { /* ... */ }
  loadUser(id) { /* ... */ }
}

class NotificationService {
  sendWelcomeEmail(user) { /* ... */ }
  notifyAdmins(action) { /* ... */ }
}
```

#### Strategy B: Extract by Data/State
**When:** Related data and methods operate on a cohesive data structure

**Process:**
1. Identify data clusters (groups of fields always used together)
2. Create new class/component around each data cluster
3. Move methods operating on that data to the new entity
4. Replace references with dependency injection

**Example:**
```python
# BEFORE: Person class mixes identity and contact info
class Person:
  def __init__(self, name, age, street, city, zip, phone, email):
    self.name = name
    self.age = age
    self.street = street
    self.city = city
    self.zip = zip
    self.phone = phone
    self.email = email
  
  def format_address(self): ...
  def format_contact(self): ...

# AFTER: Extracted data-driven classes
class Person:
  def __init__(self, name, age, address, contact):
    self.name = name
    self.age = age
    self.address = address
    self.contact = contact

class Address:
  def __init__(self, street, city, zip):
    self.street = street
    self.city = city
    self.zip = zip
  
  def format(self): ...

class Contact:
  def __init__(self, phone, email):
    self.phone = phone
    self.email = email
  
  def format(self): ...
```

#### Strategy C: Extract by Behavior/Algorithm
**When:** Complex algorithm or business logic can be isolated

**Process:**
1. Identify algorithmic boundaries
2. Extract algorithm into dedicated class/service
3. Define clear input/output contracts
4. Make injectable and testable in isolation

**Example:**
```javascript
// BEFORE: Complex discount calculation embedded in OrderProcessor
class OrderProcessor {
  calculateTotal(items, customer) {
    let subtotal = items.reduce((sum, item) => sum + item.price * item.qty, 0);
    let discount = 0;
    if (customer.isVIP) discount = subtotal * 0.15;
    else if (customer.isPremium) discount = subtotal * 0.10;
    else if (subtotal > 1000) discount = subtotal * 0.05;
    return subtotal - discount;
  }
}

// AFTER: Extracted discount strategy
interface DiscountCalculator {
  calculate(subtotal, customer): number;
}

class DiscountStrategy implements DiscountCalculator {
  calculate(subtotal, customer) { /* ... */ }
}

class OrderProcessor {
  constructor(discountCalculator: DiscountCalculator) { /* ... */ }
  
  calculateTotal(items, customer) {
    let subtotal = items.reduce((sum, item) => sum + item.price * item.qty, 0);
    return subtotal - this.discountCalculator.calculate(subtotal, customer);
  }
}
```

#### Strategy D: Extract by Abstraction/Interface
**When:** You need to decouple from a concrete implementation or anticipate multiple implementations

**Process:**
1. Define interface/protocol/abstract base representing the contract
2. Extract concrete implementation(s) into separate class(es)
3. Update dependent code to depend on the abstraction, not the concrete class
4. Use dependency injection to wire implementations
5. **Keep logically coupled concerns together** — don't segregate interfaces unless they're truly independent

**Note:** You don't need multiple implementations *before* extracting the abstraction. Extract the abstraction first, then concrete implementations can follow later without changing dependent code.

**Example (Composite Interface):**
```typescript
// BEFORE: Hardcoded payment processing
class CheckoutService {
  processPayment(method, amount) {
    if (method === "credit_card") {
      validateCreditCard(card);
      chargeCard(card, amount);
    } else if (method === "paypal") {
      authorizePayPal(token);
      capturePayPal(token, amount);
    }
  }
}

// AFTER: Unified interface maintaining logical flow
interface PaymentStrategy {
  authorize(details): bool;
  capture(amount): bool;
}

class CreditCardStrategy implements PaymentStrategy {
  authorize(details) { /* validate card */ }
  capture(amount) { /* charge card */ }
}

class PayPalStrategy implements PaymentStrategy {
  authorize(token) { /* authorize via PayPal */ }
  capture(amount) { /* capture via PayPal */ }
}

class InstantCheckoutStrategy implements PaymentStrategy {
  authorize(details) { return true; } // no-op: pre-authorized
  capture(amount) { /* process immediately */ }
}

class CheckoutService {
  constructor(paymentStrategy: PaymentStrategy) { /* ... */ }
  
  processPayment(amount) {
    if (this.paymentStrategy.authorize(...)) {
      this.paymentStrategy.capture(amount);
    }
  }
}
```

**When to Segregate Interfaces (ISP):** Only split interfaces when concerns are truly independent. Example: A data repository might have separate `Readable` and `Writable` interfaces because some clients only read, others only write. But payment authorization and capture are sequentially dependent and should stay together.

#### Strategy E: Extract Utility/Helper Classes
**When:** Common operations repeated across codebase

**Process:**
1. Identify repeated patterns/utilities
2. Extract into static utilities or standalone services
3. Create focused utility classes with minimal state
4. Utility classes should be stateless when possible; use state only when encapsulating persistent data or configuration
5. Ensure high reusability and predictable behavior

**Example:**
```python
# BEFORE: String validation repeated across classes
class UserValidator:
  def validate_email(self, email):
    return "@" in email and "." in email.split("@")[1]

class NewsletterSignup:
  def signup(self, email):
    if "@" in email and "." in email.split("@")[1]:
      subscribe(email)

# AFTER: Extracted utility with dependency injection (DIP)
class EmailValidator:
  def is_valid(self, email):
    return "@" in email and "." in email.split("@")[1]

class UserValidator:
  def __init__(self, email_validator: EmailValidator):
    self.email_validator = email_validator
  
  def validate_email(self, email):
    return self.email_validator.is_valid(email)

class NewsletterSignup:
  def __init__(self, email_validator: EmailValidator):
    self.email_validator = email_validator
  
  def signup(self, email):
    if self.email_validator.is_valid(email):
      subscribe(email)
```

---

### 3. **Extraction Checklist**

Before extracting, verify:

- [ ] **Single Responsibility:** Extracted entity has one clear purpose
- [ ] **Cohesion:** Methods/fields in extracted class are tightly related
- [ ] **Dependencies Identified:** All external dependencies are explicit (parameters/injection)
- [ ] **Interface Clear:** Public API is minimal and focused
- [ ] **No Duplication:** Extraction doesn't create new code duplication
- [ ] **Testability:** Extracted class can be unit tested in isolation
- [ ] **Naming:** Entity name clearly describes its responsibility
- [ ] **No Circular Deps:** No circular dependencies introduced
- [ ] **Documentation:** Public methods documented with purpose
- [ ] **Tests Updated:** Existing tests updated to use extracted components
- [ ] **Abstraction Quality:** If extracting an abstraction, contract is clear and enables substitution
- [ ] **Abstraction Level:** Abstraction matches domain concepts and doesn't leak implementation details (may be narrowly scoped for decoupling even with single implementation)

---

### 4. **Risk Mitigation**

#### Risks During Extraction
- **Breaking Changes:** Refactoring affects public APIs
- **Hidden Dependencies:** Implicit dependencies become visible, breaking code
- **Test Failure:** Tests tightly coupled to old structure break
- **Performance Regression:** Extra indirection or instantiation costs

#### Mitigation Strategies
1. **Preserve Backward Compatibility:** Keep old class as facade if external API
2. **Staged Rollout:** Extract internally first, expose gradually
3. **Comprehensive Tests:** Ensure tests cover new extraction boundaries
4. **Performance Profiling:** Validate no performance regression
5. **Documentation:** Update API docs, migration guides for consumers
6. **Preview Mode:** Show diffs before applying refactors
7. **Version Control:** Small, atomic commits per extraction

---

### 5. **Abstraction Design Principles**

#### Define Clear Abstractions
Extracted components should represent meaningful abstractions:
- **Behavior Abstractions:** Interfaces/protocols defining "what" (contract), not "how" (implementation)
- **Semantic Abstractions:** Component names express domain concepts, not implementation details
- **Level Consistency:** All abstractions at the same level of abstraction hierarchy

#### Abstraction Boundaries
- **Cohesion Rule:** All elements in abstraction serve the same purpose
- **Single Responsibility:** Each abstraction encapsulates one concept or behavior
- **Minimal Surface:** Public API exposes only what's necessary; hide implementation details
- **Dependency Inversion:** Depend on abstractions (interfaces), not concrete implementations

#### Abstraction Anti-Patterns to Avoid
- **Leaky Abstraction:** Implementation details leak into public interface
- **Wrong Level:** Mixing high-level business logic with low-level technical details
- **Overly Generic:** Abstractions so broad they lose meaning (e.g., `IProcessor` used everywhere)
- **Over-Segregated Interfaces:** Splitting logically dependent concerns (ISP violation in reverse) — causes caller complexity
- **Premature Extraction:** Creating abstractions for speculative reuse when no decoupling is needed

**Interface Segregation vs Cohesion:** Don't segregate interfaces just because ISP exists. If two methods are logically dependent (both needed in sequence), keep them together. Segregate only when clients truly need independent concerns (read-only vs write-only access).

#### Effective Abstraction Design
1. **Identify the Core Behavior:** What core responsibility does this abstraction encapsulate?
2. **Define the Contract:** What methods/properties must all implementations provide?
3. **Hide Complexity:** Keep implementation details private; expose only essential interface
4. **Enable Substitution:** Implementations must be interchangeable (Liskov Substitution)
5. **Document the Intent:** Why does this abstraction exist? When should it be used?

---

### 6. **Validation & Testing**

#### Post-Extraction Validation
1. **Compilation:** Code compiles/syntax is valid
2. **Type Safety:** No type errors introduced
3. **Unit Tests:** All extracted classes pass unit tests
4. **Integration Tests:** Extracted classes integrate correctly with dependents
5. **Regression Tests:** Original functionality preserved
6. **Performance Tests:** No performance regression
7. **Code Review:** Peer review extraction rationale and implementation

#### Test Coverage Expectations
- **Extracted Class:** 80%+ line/branch coverage
- **Dependent Code:** Updated tests reflect new structure
- **Integration Points:** Tests verify injected dependencies work correctly
- **Edge Cases:** Tests cover boundary conditions

---

### 7. **Common Pitfalls**

| Pitfall | Problem | Solution |
|---------|---------|----------|
| **Over-Extraction** | Too many small, trivial classes | Extract only cohesive, meaningful units |
| **Under-Extraction** | Classes still too large/complex | Aggressively break down large entities |
| **Wrong Boundaries** | Extracted classes poorly model domain | Validate abstraction matches business logic |
| **Complex Injector** | Dependency injection becomes too complex | Use factories, service locators for complex wiring |
| **Brittle Interfaces** | Extracted interfaces too specific to one use | Design interfaces for reusability |
| **Incomplete Extraction** | Some coupling remains | Verify all dependencies explicitly injected |
| **Lost Context** | Extracted class loses business logic clarity | Preserve intent via naming and documentation |

---

### 8. **Success Metrics**

After successful extraction:

- **Cyclomatic Complexity:** Reduced in original and extracted classes
- **Class Size:** Each class under 200 lines with <15 methods
- **Code Reuse:** Common logic reused instead of duplicated
- **Test Coverage:** Improved coverage due to isolated, testable units
- **Coupling:** Explicit dependencies (constructor params) vs implicit
- **Cohesion:** Methods and fields logically grouped
- **Naming Clarity:** Class names accurately describe responsibility
- **Maintainability Index:** Overall codebase maintainability improved

---

## Quick Reference

**Extract When:**
- Class has 2+ unrelated responsibilities
- More than 200 lines or 15+ methods
- High coupling to other classes
- Difficult to test in isolation
- Logic duplicated elsewhere

**Don't Extract:**
- Trivial utility (unless significant reuse)
- Single-use, tightly coupled logic
- Will only increase complexity
- Breaking public API without need

