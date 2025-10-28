# 💳 Family Cash Card REST API

A secure RESTful API for managing family cash cards, built with Spring Boot and Spring Security.

[![Java](https://img.shields.io/badge/Java-17-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [API Documentation](#api-documentation)
- [Security](#security)
- [Testing](#testing)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [Author](#author)

## 🎯 Overview

The Family Cash Card API is a Spring Boot application that allows families to manage virtual cash cards. Each user can create, view, update, and delete their own cash cards securely. The application implements best practices in REST API design, security, and testing.

## ✨ Features

### Core Functionality
- 🔐 **Secure Authentication** - Basic Auth with encrypted passwords
- 👥 **Role-Based Access Control** - CARD-OWNER role required
- 🎫 **CRUD Operations** - Full Create, Read, Update, Delete support
- 📄 **Pagination & Sorting** - Efficient data retrieval
- 🔒 **Data Ownership** - Users can only access their own cards
- 🛡️ **Security Best Practices** - No information leakage

### Technical Features
- RESTful API design
- Spring Security integration
- In-memory H2 database
- Comprehensive test coverage
- Clean code architecture
- TDD approach (Red-Green-Refactor)

## 🛠️ Tech Stack

| Technology | Version | Purpose |
|------------|---------|---------|
| **Java** | 17 | Programming Language |
| **Spring Boot** | 3.x | Application Framework |
| **Spring Security** | 6.x | Security & Authentication |
| **Spring Data JPA** | 3.x | Data Access Layer |
| **H2 Database** | 2.x | In-Memory Database |
| **JUnit 5** | 5.x | Testing Framework |
| **AssertJ** | 3.x | Fluent Assertions |
| **Gradle** | 8.x | Build Tool |

## 🚀 Getting Started

### Prerequisites

- Java 17 or higher
- Gradle 8.x (or use the included wrapper)
- Your favorite IDE (IntelliJ IDEA, Eclipse, VS Code)

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/Juste120/family-cashcard-api.git
cd family-cashcard-api
```

2. **Build the project**
```bash
./gradlew build
```

3. **Run the tests**
```bash
./gradlew test
```

4. **Run the application**
```bash
./gradlew bootRun
```

The application will start on `http://localhost:8080`

### Test Users

The application comes with pre-configured test users:

| Username | Password | Role | Cash Cards |
|----------|----------|------|------------|
| `sarah1` | `abc123` | CARD-OWNER | IDs: 99, 100, 101 |
| `kumar2` | `xyz789` | CARD-OWNER | ID: 102 |
| `hank-owns-no-cards` | `qrs456` | NON-OWNER | None |

## 📚 API Documentation

### Base URL
```
http://localhost:8080
```

### Authentication
All endpoints require Basic Authentication:
```bash
Authorization: Basic base64(username:password)
```

### Endpoints

#### 1. Get a Cash Card
```http
GET /cashcards/{id}
```

**Example Request:**
```bash
curl -u sarah1:abc123 http://localhost:8080/cashcards/99
```

**Success Response (200 OK):**
```json
{
  "id": 99,
  "amount": 123.45,
  "owner": "sarah1"
}
```

**Error Response (404 NOT FOUND):**
```
Card not found or not owned by user
```

---

#### 2. Get All Cash Cards (Paginated)
```http
GET /cashcards?page={page}&size={size}&sort={field},{direction}
```

**Example Request:**
```bash
curl -u sarah1:abc123 "http://localhost:8080/cashcards?page=0&size=10&sort=amount,desc"
```

**Success Response (200 OK):**
```json
[
  {
    "id": 101,
    "amount": 150.00,
    "owner": "sarah1"
  },
  {
    "id": 99,
    "amount": 123.45,
    "owner": "sarah1"
  },
  {
    "id": 100,
    "amount": 1.00,
    "owner": "sarah1"
  }
]
```

**Query Parameters:**
- `page` (optional): Page number (default: 0)
- `size` (optional): Items per page (default: 20)
- `sort` (optional): Sort field and direction (e.g., `amount,desc`)

---

#### 3. Create a Cash Card
```http
POST /cashcards
Content-Type: application/json
```

**Example Request:**
```bash
curl -u sarah1:abc123 \
  -X POST http://localhost:8080/cashcards \
  -H "Content-Type: application/json" \
  -d '{"amount": 250.00}'
```

**Success Response (201 CREATED):**
```
Location: http://localhost:8080/cashcards/103
```

---

#### 4. Update a Cash Card
```http
PUT /cashcards/{id}
Content-Type: application/json
```

**Example Request:**
```bash
curl -u sarah1:abc123 \
  -X PUT http://localhost:8080/cashcards/99 \
  -H "Content-Type: application/json" \
  -d '{"amount": 19.99}'
```

**Success Response (204 NO CONTENT):**
```
No content returned
```

**Error Response (404 NOT FOUND):**
```
Card not found or not owned by user
```

---

#### 5. Delete a Cash Card
```http
DELETE /cashcards/{id}
```

**Example Request:**
```bash
curl -u sarah1:abc123 \
  -X DELETE http://localhost:8080/cashcards/99
```

**Success Response (204 NO CONTENT):**
```
No content returned
```

**Error Response (404 NOT FOUND):**
```
Card not found or not owned by user
```

---

### HTTP Status Codes

| Status Code | Description |
|-------------|-------------|
| `200 OK` | Request successful |
| `201 CREATED` | Resource created successfully |
| `204 NO CONTENT` | Request successful, no content to return |
| `401 UNAUTHORIZED` | Invalid credentials |
| `403 FORBIDDEN` | Insufficient permissions |
| `404 NOT FOUND` | Resource not found or not owned by user |

## 🔒 Security

### Security Layers

1. **Authentication**
   - Basic Authentication with BCrypt password encoding
   - All endpoints require valid credentials

2. **Authorization (RBAC)**
   - Only users with `CARD-OWNER` role can access cash card endpoints
   - `NON-OWNER` role users receive `403 FORBIDDEN`

3. **Data Ownership**
   - Users can only access their own cash cards
   - Attempting to access another user's card returns `404 NOT FOUND`
   - Prevents information leakage about existence of other users' data

### Security Best Practices Implemented

✅ Password encryption with BCryptPasswordEncoder  
✅ Principal-based ownership validation  
✅ Repository-level data filtering  
✅ Generic error responses (no data leakage)  
✅ CSRF protection disabled for REST API  
✅ Stateless authentication  

### Example Security Flow
```
Client Request
    ↓
Spring Security Filter Chain
    ↓
Authentication (Credentials Valid?)
    ↓ Yes
Authorization (Has CARD-OWNER Role?)
    ↓ Yes
Controller (Ownership Check)
    ↓ Yes
Repository (Filter by Owner)
    ↓
Response
```

## 🧪 Testing

### Test Coverage

The project includes **15+ comprehensive tests** covering:

- ✅ Authentication tests
- ✅ Authorization tests
- ✅ CRUD operation tests
- ✅ Ownership validation tests
- ✅ Edge case handling
- ✅ Security scenarios

### Running Tests
```bash
# Run all tests
./gradlew test

# Run tests with detailed output
./gradlew test --info

# Run tests and generate coverage report
./gradlew test jacocoTestReport
```

### Test Structure
```java
@SpringBootTest
class CashcardApplicationTests {
    
    @Test
    void shouldReturnACashCardWhenDataIsSaved() { }
    
    @Test
    void shouldNotReturnACashCardWhenUsingBadCredentials() { }
    
    @Test
    void shouldRejectUsersWhoAreNotCardOwners() { }
    
    @Test
    void shouldNotAllowAccessToCashCardsTheyDoNotOwn() { }
    
    // ... 11 more tests
}
```

### View Test Reports

After running tests, open the HTML report:
```bash
open build/reports/tests/test/index.html
```

## 📁 Project Structure
```
family-cashcard-api/
├── src/
│   ├── main/
│   │   ├── java/juste/cashcard/
│   │   │   ├── CashCard.java                 # Entity (Record)
│   │   │   ├── CashCardController.java       # REST Controller
│   │   │   ├── CashCardRepository.java       # Data Repository
│   │   │   ├── SecurityConfig.java           # Security Configuration
│   │   │   └── CashcardApplication.java      # Main Application
│   │   └── resources/
│   │       ├── application.properties         # App Configuration
│   │       ├── schema.sql                     # Database Schema
│   │       └── data.sql                       # Test Data
│   └── test/
│       ├── java/juste/cashcard/
│       │   ├── CashcardApplicationTests.java  # Integration Tests
│       │   └── CashCardJsonTest.java          # JSON Serialization Tests
│       └── resources/juste/cashcard/
│           ├── expected.json                  # Test Fixtures
│           └── list.json                      # Test Fixtures
├── build.gradle                               # Build Configuration
├── settings.gradle                            # Project Settings
├── gradlew                                    # Gradle Wrapper (Unix)
├── gradlew.bat                                # Gradle Wrapper (Windows)
├── LICENSE                                    # MIT License
└── README.md                                  # This file
```

## 🏗️ Architecture

### Layer Architecture
```
┌─────────────────────────────────────┐
│         REST API Layer              │
│      (CashCardController)           │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│       Service Layer (Implicit)      │
│    (Business Logic in Controller)   │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│      Repository Layer               │
│    (CashCardRepository + JPA)       │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│        Database Layer               │
│          (H2 Database)              │
└─────────────────────────────────────┘
```

### Design Patterns Used

- **Repository Pattern**: Data access abstraction
- **Dependency Injection**: Spring IoC container
- **Record Pattern**: Immutable data objects (Java 17)
- **Builder Pattern**: User creation in SecurityConfig
- **REST Maturity Model Level 2**: HTTP verbs and status codes

## 🎓 Learning Outcomes

This project demonstrates:

✅ Building REST APIs with Spring Boot  
✅ Implementing Spring Security  
✅ Test-Driven Development (TDD)  
✅ CRUD operations with Spring Data JPA  
✅ Basic Authentication and Authorization  
✅ Role-Based Access Control (RBAC)  
✅ Data ownership and multi-tenancy  
✅ Pagination and sorting  
✅ HTTP status codes and REST best practices  
✅ Clean code and refactoring  

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Code Style

- Follow Java naming conventions
- Write meaningful commit messages
- Include tests for new features
- Keep methods small and focused
- Use descriptive variable names

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**Pakou Komi Juste**
- GitHub: [@Juste120](https://github.com/Juste120)
- LinkedIn: [Pakou Komi Juste](https://www.linkedin.com/in/pakou-komi-juste-941536314/)
- Location: Togo, Maritime

## 🙏 Acknowledgments

- Spring Boot Team for the excellent framework
- Spring Academy for the learning resources
- The open-source community

---

**Built with ❤️ using Spring Boot**

⭐ Star this repo if you find it helpful!

## 3. Fichier LICENSE (MIT)

Créez un fichier `LICENSE` :
```
MIT License

Copyright (c) 2025 Juste

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
