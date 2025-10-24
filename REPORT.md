# Lab 3 Complete a Web API -- Project Report
## Description of Changes
In this laboratory, I implemented and verified the test cases for the `EmployeeControler` REST API, which exposes 
the following endpoints:
- `POST /employees`
- `GET /employees/{id}`
- `PUT /employees/{id}`
- `DELETE /employees/{id}`

The main goal was to demonstrate the **safety** and **idempotency** properties of each HTTP method by completing the 
missing **SETUP** and **VERIFY** blocks in `ControllerTests.kt`.
- When POST is not safe and not idempotent:
  ```kotlin
    every {
        employeeRepository.save(any<Employee>())
    } answers {
        Employee("Mary", "Manager", 1)
    } andThenAnswer {
        Employee("Mary", "Manager", 2)
    }
  ```
- When GET is safe and idempotent
  ```kotlin
    every {
        employeeRepository.findById(1)
    } answers {
        Optional.of(Employee("Mary", "Manager", 1))
    }

    every {
        employeeRepository.findById(2)
    } answers {
        Optional.empty()
    }
  ```
- When PUT is idempotent but not safe
  ```kotlin
    every {
        employeeRepository.findById(1)
    } answers {
        Optional.empty()
    } andThenAnswer {
        Optional.of(Employee("Tom", "Manager", 1))
    }
    every {
        employeeRepository.save(any<Employee>())
    } answers {
        Employee("Tom", "Manager", 1)
    }
  ```
- DELETE is idempotent but not safe
  ```kotlin
    justRun {
        employeeRepository.deleteById(1)
    }
  ```

## Technical Decisions
- **Ktlint for code quality:** The code was formatted using `./gradlew ktlintFormat` to follow Kotlin conventions.
- **Test strategy:** Each test simulates two consecutive calls to the same endpoint to verify **idempotency** and checks
  whether repository methods are **safe** (no state changes) or **unsafe** (modifies state).

## Learning Outcomes
[What you learned from this assignment]
From this laboratory I was able to learn more about:
- The difference between safe and idempotent HTTP methods:
  - `GET` is safe and idempotent.
  - `POST` is neither safe nor idempotent.
  - `PUT` and `DELETE` is idempotent but not safe.
- How to verify controller logic using `MockMvc`.
- How to follow Kotlin's clean code and testing conventions using Ktlint.

## AI Disclosure
Only Grammarly was used in this project in order to increase the clarity of this report.