---
name: Refactorer
description: Comprehensive code refactoring agent covering SOLID, DRY, Keep It Simple, YAGNI, security, performance, and code quality best practices.
type: subagent
platform: cursor
category: Development
languages:
  - JavaScript
  - TypeScript
  - Python
  - Java
  - Go
  - C#
recommendedLLMs:
  - claude-3.5-sonnet
  - gpt-4o
tags:
  - coding
  - automation
  - productivity
  - refactoring
  - design
  - code-quality
version: 1.1.0
license: MIT
repository: null
documentation: https://example.com/docs/solid-refactoring
---

## For Cursor

### Agent Metadata
- **Name:** Refactorer
- **ID:** `refactorer`
### Purpose
This agent analyzes code across SOLID, code quality, security, performance, and maintainability dimensions.
- **Type:** Subagent (runs within Cursor's editor context)
- **Platform:** Cursor IDE
- **Activation:** Via Command Palette (`cmd+shift+p`) → "Refactorer" or context menu on selected code

### How Cursor Uses This
1. **Registration:** Cursor detects this subagent manifest and registers `Refactorer` as an available subagent.
2. **Invocation:** Users trigger via palette, context menu, or keyboard shortcut.
3. **Context Passing:** Cursor provides selected code, file context, and project structure to the subagent.
4. **Analysis & Suggestions:** The subagent analyzes against SOLID rules and returns findings grouped by principle.
5. **Editor Integration:** Results appear as inline suggestions, diagnostics, and a refactor panel where users can preview and apply changes.
6. **LLM Selection:** Cursor uses recommended LLMs (Claude 3.5 Sonnet or GPT-4o) for analysis and code generation.

### Configuration in Cursor
Place the YAML configuration in your project's `.cursor/refactorer-config.yaml`:
```yaml
# .cursor/refactorer-config.yaml
version: "1.1"
# ... (see Configuration section below for full details)
```

---

# Comprehensive Refactoring Agent

## Overview
This subagent analyzes code across multiple design, quality, and safety dimensions:
- **Design:** SOLID principles (SRP, OCP, LSP, ISP, DIP)
- **Quality:** DRY, Keep It Simple, YAGNI, Composition over Inheritance, Immutability
- **Safety:** Error Handling, Security, Performance, Type Safety
- **Maintainability:** Naming, Documentation, Testability

It recommends and applies refactorings with automated transformations and guided suggestions.

## Features

**SOLID Principles:**
- **Single Responsibility (SRP)**: Identifies classes/modules with multiple concerns and suggests logical splits.
- **Open/Closed (OCP)**: Detects modification points and proposes strategy patterns or plugin architectures.
- **Liskov Substitution (LSP)**: Checks inheritance hierarchies for contract violations.
- **Interface Segregation (ISP)**: Finds fat interfaces and suggests smaller, focused roles.
- **Dependency Inversion (DIP)**: Identifies direct coupling and suggests dependency injection.

**Code Quality Principles:**
- **DRY (Don't Repeat Yourself)**: Detects code duplication and suggests extraction into shared utilities.
- **Keep It Simple**: Flags overly complex logic and suggests simplification patterns.
- **YAGNI (You Aren't Gonna Need It)**: Identifies unused code, dead branches, and over-engineered features.
- **Composition over Inheritance**: Detects deep hierarchies and suggests composition or mixins.
- **Immutability**: Flags mutable state and suggests immutable data structures.

**Safety & Performance:**
- **Error Handling**: Identifies missing or poor exception handling patterns.
- **Security**: Flags hardcoded secrets, SQL injection risks, unsafe deserialization, and vulnerabilities.
- **Performance Optimization**: Detects O(n²) loops, inefficient algorithms, memory leaks.
- **Type Safety**: Suggests stronger typing, generics, null checks (TypeScript/Python).

**Maintainability:**
- **Naming Conventions**: Checks names for clarity and language consistency.
- **Documentation/Comments**: Flags missing or outdated comments on complex logic.
- **Testability**: Identifies tight coupling and suggests refactors for test coverage.

**General Capabilities:**
- **Automated Quick Fixes**: Safely applies refactorings with preview, undo, and test validation.
- **Severity Levels**: Categorizes findings as error, warning, or info.
- **Multi-language Support**: Works with TypeScript, Python, Java, Go, and C#.

## Usage
Use this subagent inside your IDE (Cursor) or CI checks to run targeted SOLID-focused refactor passes.

**Interactive Mode:**
- Open the file or project and invoke the subagent via command palette or subagent menu.
- Review the analysis report grouped by SOLID principle and severity.
- For each finding, choose "Suggest" (show diff) or "Apply" (execute with preview).

**Batch/Audit Mode:**
- Run read-only audits across a codebase to export a compliance report.
- Integrate into CI/CD pipelines for automated quality gates.

**Configuration:**
- Copy the YAML configuration template and customize rules, severity thresholds, and scope.
- Use environment variables to override config values in CI environments.

## Configuration
```yaml
# Complete configuration for the SOLID Refactoring Agent
version: "1.1"
scan:
  include:
    - src/
    - lib/
    - app/
  exclude:
    - tests/
    - migrations/
    - node_modules/
    - "**/*.test.ts"
  maxFileSize: 10000 # lines per file
  timeout: 30 # seconds per file

rules:
  # SOLID Principles
  SRP:
    enabled: true
    severity: warning
    threshold: 2 # number of responsibility clusters before flagging
    methodCountLimit: 15 # methods per class
  OCP:
    enabled: true
    severity: warning
    detectModificationPoints: true
  LSP:
    enabled: true
    severity: error
    checkReturnTypes: true
  ISP:
    enabled: true
    severity: warning
    minMethodsToFlag: 4 # fat interface detection
  DIP:
    enabled: true
    severity: warning
    checkDirectInstantiation: true

  # Code Quality Principles
  DRY:
    enabled: true
    severity: warning
    duplicateThreshold: 0.85 # similarity score (0-1)
    minLinesToFlag: 5 # minimum lines for duplication
  Keep It Simple:
    enabled: true
    severity: warning
    cyclomaticComplexityLimit: 10
    nestingDepthLimit: 4 # max nesting levels
  YAGNI:
    enabled: true
    severity: info
    flagUnusedCode: true
    flagUnusedDependencies: true
  CompositionOverInheritance:
    enabled: true
    severity: warning
    inheritanceDepthLimit: 3
  Immutability:
    enabled: true
    severity: info
    flagMutableGlobals: true
    flagMutableDefaults: true

  # Safety & Performance
  ErrorHandling:
    enabled: true
    severity: error
    flagMissingCatches: true
    flagSwallowedExceptions: true
  Security:
    enabled: true
    severity: error
    flagHardcodedSecrets: true
    flagSQLInjection: true
    flagUnsafeDeserialize: true
  Performance:
    enabled: true
    severity: warning
    flagNestedLoops: true
    flagExpensiveOperations: true
  TypeSafety:
    enabled: true
    severity: warning
    flagAnyTypes: true
    flagNullChecks: true

  # Maintainability
  NamingConventions:
    enabled: true
    severity: info
    enforceCase: true # camelCase, PascalCase, snake_case
    flagAmbiguousNames: true
  Documentation:
    enabled: true
    severity: info
    flagMissingDocstrings: true
    flagStaleComments: true
  Testability:
    enabled: true
    severity: warning
    flagTightCoupling: true
    flagHiddenDependencies: true

behavior:
  autoApply: false # false=preview, true=apply with test validation
  validateAfterApply: true # run tests post-refactor
  generateBackup: true
  maxRefactorsPerRun: 5

report:
  format: html # options: html, json, markdown
  output: solid-refactor-report.html
  includeDiffPreview: true
  groupBySeverity: true
  groupByPrinciple: true

performance:
  parallel: true
  workers: 4
  cache: true
``` 

## Examples

### Example 1: Extracting Responsibilities (SRP)
**Before:** A `UserManager` class handles authentication, data persistence, user notifications, and audit logging.

**Agent Suggestion:** Extract into `AuthService`, `UserRepository`, `NotificationService`, and `AuditLogger` with updated tests and dependency injection wiring.

**Severity:** Warning | **Impact:** High readability gain

---

### Example 2: Interface Segregation (ISP)
**Before:** `IWidget` exposes `render()`, `save()`, `export()`, `validate()` while consumers only need 1–2 methods.

**Agent Suggestion:** Split into `IRenderable`, `IPersistable`, `IExportable`, `IValidatable` and update client dependencies.

**Severity:** Warning | **Impact:** Reduces coupling

---

### Example 3: Dependency Inversion (DIP)
**Before:** `OrderService` directly instantiates `SqlOrderRepository` in its constructor.

**Agent Suggestion:** Introduce `IOrderRepository` interface, inject via constructor, and wire in factory/container.

**Severity:** Warning | **Impact:** Testability, portability

---

### Example 4: Open/Closed Violation (OCP)
**Before:** `PaymentProcessor.process()` has a large `if/else` chain for each payment type (Visa, PayPal, Stripe).

**Agent Suggestion:** Introduce `IPaymentStrategy` interface and a factory, allowing new payment types without modifying existing code.

**Severity:** Error | **Impact:** Maintainability, extension safety

---

### Example 5: DRY Violation
**Before:** `calculateTax()` and `calculateTotal()` methods contain identical tax computation logic duplicated across OrderProcessor and InvoiceProcessor.

**Agent Suggestion:** Extract shared tax logic into `TaxCalculator` utility class and inject it into both processors.

**Severity:** Warning | **Impact:** Reduced maintenance burden

---

### Example 6: Keep It Simple Violation
**Before:** A validation function with 8 levels of nesting and cyclomatic complexity of 22.

**Agent Suggestion:** Break into smaller, focused validators; use early returns; introduce helper methods.

**Severity:** Warning | **Impact:** Readability, debuggability

---

### Example 7: Security Issue
**Before:** `executeQuery(sql)` method directly concatenates user input into SQL string without parameterization.

**Agent Suggestion:** Refactor to use parameterized queries or ORM with bound parameters.

**Severity:** Error | **Impact:** Critical security fix

---

### Example 8: Type Safety (TypeScript)
**Before:** Function accepts `any` type and uses `obj.data.value` without checks.

**Agent Suggestion:** Define strict interface, add null checks, use generics where appropriate.

**Severity:** Warning | **Impact:** Runtime error prevention

## Notes

### Safety & Testing
- The agent only auto-applies refactorings when `autoApply: true` with passing tests enabled.
- Manual mode produces diff previews for review before committing.
- Always run full test suites after large refactors; integrate into CI/CD for validation.

### Best Practices
- **Incremental:** Start with audits and low-risk suggestions before sweeping changes.
- **Prioritize Errors:** Address high-severity findings (LSP violations, DIP direct coupling) first.
- **Custom Rules:** Extend with domain-specific patterns via plugin architecture.
- **CI Integration:** Run read-only audits on PRs to catch violations early.

### Limitations
- Heuristic-based detection; some findings may require manual review.
- Complex refactors (large method extraction, circular dependency breaking) may need manual intervention.
- Performance depends on codebase size; configure `workers` and `timeout` accordingly.

### CLI / Integration
```bash
# Run audit and generate HTML report
solid-refactor audit --config ./solid-config.yaml --output report.html

# Apply refactorings interactively
solid-refactor refactor --config ./solid-config.yaml --interactive

# CI mode (non-interactive, exit code indicates pass/fail)
solid-refactor audit --config ./solid-config.yaml --strict --exit-on-error
```
