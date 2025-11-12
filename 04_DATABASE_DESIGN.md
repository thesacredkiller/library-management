# Library Management System - Database Design

## 💾 Database Architecture & Data Models

This document provides complete details about the database design, including schemas, relationships, and data structures used in the Library Management System.

---

## 🏗️ Database Platform

**Database Type**: Cloud Firestore (NoSQL Document Database)

**Provider**: Google Firebase

**Version**: 6.0.2

**Database URL**: `https://library-management-syste-f2a85.firebaseio.com`

---

## 📊 Entity Relationship Diagram

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#8b5cf6','primaryTextColor':'#fff','primaryBorderColor':'#7c3aed','fontSize':'18px','fontFamily':'Consolas, Monaco, Courier New, monospace'}}}%%
erDiagram
    ADMIN ||--o{ BOOKS : "manages and creates"
    STUDENT ||--o{ BOOKS : "borrows and reads"
    ADMIN ||--o{ STUDENT : "views and monitors"
    
    ADMIN {
        string email PK "Primary Key"
        string password "Hashed by Firebase"
        string role "Always 'admin'"
        timestamp createdAt "Account creation"
        timestamp lastLogin "Last login time"
    }
    
    STUDENT {
        string Roll_Number PK "Primary Key - Unique ID"
        string name "Full name of student"
        string Email UK "Unique email - Auth identifier"
        string DOB "Date of birth"
        array books FK "Array of book codes"
        timestamp createdAt "Registration date"
        timestamp updatedAt "Last profile update"
    }
    
    BOOKS {
        string bookcode PK "Primary Key - Unique ID"
        string bookname "Title of the book"
        string author1 "Primary author name"
        string author2 "Secondary author - optional"
        string subject "Subject category"
        string tags "Comma-separated search tags"
        timestamp createdAt "Date added to system"
        string addedBy "Admin email who added"
    }
```

---

## 📚 Collection: Books

### Document Structure

```javascript
{
  // Document ID = bookcode (e.g., "B001", "CS101")
  bookcode: "B001",                    // Type: string, Primary Key
  bookname: "Introduction to AI",      // Type: string, Required
  author1: "John Doe",                 // Type: string, Required
  author2: "Jane Smith",               // Type: string, Optional
  subject: "Computer Science",         // Type: string, Required
  tags: "AI, ML, CS, Technology"       // Type: string, Optional
}
```

### Field Specifications

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| **bookcode** | string | ✅ Yes | Unique, PK | Unique identifier for the book |
| **bookname** | string | ✅ Yes | 1-500 chars | Full title of the book |
| **author1** | string | ✅ Yes | 1-200 chars | Primary author name |
| **author2** | string | ❌ No | 1-200 chars | Secondary author (optional) |
| **subject** | string | ✅ Yes | 1-100 chars | Subject/category of book |
| **tags** | string | ❌ No | Max 500 chars | Comma-separated keywords for search |

### Example Documents

```javascript
// Example 1: Computer Science Book
{
  bookcode: "CS101",
  bookname: "Data Structures and Algorithms",
  author1: "Robert Sedgewick",
  author2: "Kevin Wayne",
  subject: "Computer Science",
  tags: "algorithms, data structures, programming, java"
}

// Example 2: Mathematics Book
{
  bookcode: "MATH201",
  bookname: "Linear Algebra",
  author1: "Gilbert Strang",
  author2: "",
  subject: "Mathematics",
  tags: "linear algebra, matrices, vectors"
}

// Example 3: Physics Book
{
  bookcode: "PHY301",
  bookname: "Quantum Mechanics",
  author1: "Richard Feynman",
  author2: "Albert Hibbs",
  subject: "Physics",
  tags: "quantum, physics, mechanics, theory"
}
```

### Collection Visualization

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#3b82f6','primaryTextColor':'#fff','primaryBorderColor':'#2563eb','fontSize':'16px','fontFamily':'Courier New, monospace'}}}%%
graph TB
    COLLECTION[📚 Books Collection]
    
    COLLECTION --> DOC1[Document: B001<br/>bookcode: B001<br/>bookname: Intro to AI<br/>author1: John Doe<br/>author2: Jane Smith<br/>subject: CS<br/>tags: AI, ML]
    
    COLLECTION --> DOC2[Document: CS101<br/>bookcode: CS101<br/>bookname: Data Structures<br/>author1: Sedgewick<br/>author2: Wayne<br/>subject: CS<br/>tags: algorithms]
    
    COLLECTION --> DOC3[Document: MATH201<br/>bookcode: MATH201<br/>bookname: Linear Algebra<br/>author1: Strang<br/>author2: empty<br/>subject: Mathematics<br/>tags: matrices]
    
    COLLECTION -.More documents.-> DOCN[...]
    
    style COLLECTION fill:#3b82f6,stroke:#2563eb,stroke-width:4px,color:#fff,font-weight:bold
    style DOC1 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#fff
    style DOC2 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#fff
    style DOC3 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#fff
    style DOCN fill:#93c5fd,stroke:#60a5fa,stroke-width:2px,color:#333
```

---

## 👥 Collection: Users

### Document Structure

```javascript
{
  // Document ID = Roll_Number (e.g., "2018001", "STU12345")
  Roll_Number: "2018001",              // Type: string, Primary Key
  name: "Alice Johnson",               // Type: string, Required
  Email: "alice@example.com",          // Type: string, Required, Unique
  DOB: "1999-05-15",                  // Type: string (YYYY-MM-DD), Required
  books: ["B001", "CS101", "MATH201"]  // Type: array of strings, Optional
}
```

### Field Specifications

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| **Roll_Number** | string | ✅ Yes | Unique, PK | Student's roll/ID number |
| **name** | string | ✅ Yes | 1-200 chars | Full name of student |
| **Email** | string | ✅ Yes | Valid email, Unique | Email for authentication |
| **DOB** | string | ✅ Yes | Date format | Date of birth |
| **books** | array | ❌ No | Array of strings | List of borrowed book codes |

### Example Documents

```javascript
// Example 1: Student with multiple borrowed books
{
  Roll_Number: "2018001",
  name: "Alice Johnson",
  Email: "alice.johnson@university.edu",
  DOB: "1999-05-15",
  books: ["CS101", "MATH201", "PHY301"]
}

// Example 2: Student with one borrowed book
{
  Roll_Number: "2018002",
  name: "Bob Smith",
  Email: "bob.smith@university.edu",
  DOB: "1998-08-22",
  books: ["B001"]
}

// Example 3: New student with no borrowed books
{
  Roll_Number: "2020150",
  name: "Charlie Brown",
  Email: "charlie.brown@university.edu",
  DOB: "2001-12-10",
  books: []
}
```

### Collection Visualization

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#10b981','primaryTextColor':'#fff','primaryBorderColor':'#059669','fontSize':'16px','fontFamily':'Courier New, monospace'}}}%%
graph TB
    COLLECTION[👥 Users Collection]
    
    COLLECTION --> USER1[Document: 2018001<br/>Roll_Number: 2018001<br/>name: Alice Johnson<br/>Email: alice@university.edu<br/>DOB: 1999-05-15<br/>books: CS101, MATH201, PHY301]
    
    COLLECTION --> USER2[Document: 2018002<br/>Roll_Number: 2018002<br/>name: Bob Smith<br/>Email: bob@university.edu<br/>DOB: 1998-08-22<br/>books: B001]
    
    COLLECTION --> USER3[Document: 2020150<br/>Roll_Number: 2020150<br/>name: Charlie Brown<br/>Email: charlie@university.edu<br/>DOB: 2001-12-10<br/>books: empty array]
    
    COLLECTION -.More users.-> USERN[...]
    
    USER1 -.borrows.-> BOOK1[📚 Book: CS101]
    USER1 -.borrows.-> BOOK2[📚 Book: MATH201]
    USER1 -.borrows.-> BOOK3[📚 Book: PHY301]
    USER2 -.borrows.-> BOOK4[📚 Book: B001]
    
    style COLLECTION fill:#10b981,stroke:#059669,stroke-width:4px,color:#fff,font-weight:bold
    style USER1 fill:#34d399,stroke:#10b981,stroke-width:2px,color:#fff
    style USER2 fill:#34d399,stroke:#10b981,stroke-width:2px,color:#fff
    style USER3 fill:#34d399,stroke:#10b981,stroke-width:2px,color:#fff
    style USERN fill:#6ee7b7,stroke:#34d399,stroke-width:2px,color:#333
    style BOOK1 fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style BOOK2 fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style BOOK3 fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style BOOK4 fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px,color:#fff
```

---

## 🔗 Data Relationships

### Books ↔ Users Relationship

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#f59e0b','primaryTextColor':'#fff','primaryBorderColor':'#d97706','fontSize':'16px','fontFamily':'Arial, sans-serif'}}}%%
graph LR
    subgraph "Books Collection"
        B1[Book: CS101]
        B2[Book: MATH201]
        B3[Book: PHY301]
        B4[Book: B001]
    end
    
    subgraph "Users Collection"
        U1[Student: 2018001<br/>books: CS101, MATH201, PHY301]
        U2[Student: 2018002<br/>books: B001]
        U3[Student: 2018003<br/>books: CS101, B001]
    end
    
    U1 -.references.-> B1
    U1 -.references.-> B2
    U1 -.references.-> B3
    U2 -.references.-> B4
    U3 -.references.-> B1
    U3 -.references.-> B4
    
    style B1 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#fff
    style B2 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#fff
    style B3 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#fff
    style B4 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#fff
    style U1 fill:#34d399,stroke:#10b981,stroke-width:2px,color:#fff
    style U2 fill:#34d399,stroke:#10b981,stroke-width:2px,color:#fff
    style U3 fill:#34d399,stroke:#10b981,stroke-width:2px,color:#fff
```

**Relationship Type**: One-to-Many (Tracked via Array)

**Implementation**: 
- Users collection stores array of book codes
- No foreign key constraints (NoSQL)
- Application enforces referential integrity
- Array can contain multiple book codes

---

## 🔍 Query Patterns

### Common Queries

#### 1. Fetch All Books
```javascript
db.collection("books")
  .get()
  .then(function(querySnapshot) {
    querySnapshot.forEach(function(doc) {
      console.log(doc.id, " => ", doc.data());
    });
  });
```

#### 2. Fetch Specific Book by Code
```javascript
db.collection("books")
  .doc("CS101")
  .get()
  .then(function(doc) {
    if (doc.exists) {
      console.log("Book data:", doc.data());
    }
  });
```

#### 3. Search Books by Subject
```javascript
db.collection("books")
  .where("subject", "==", "Computer Science")
  .get()
  .then(function(querySnapshot) {
    querySnapshot.forEach(function(doc) {
      console.log(doc.data());
    });
  });
```

#### 4. Fetch User by Email
```javascript
db.collection("users")
  .where("Email", "==", "alice@university.edu")
  .get()
  .then(function(querySnapshot) {
    querySnapshot.forEach(function(doc) {
      console.log("User:", doc.data());
    });
  });
```

#### 5. Fetch All Users
```javascript
db.collection("users")
  .get()
  .then(function(querySnapshot) {
    querySnapshot.forEach(function(doc) {
      console.log(doc.data());
    });
  });
```

---

## 💾 Data Operations Flow

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#ef4444','primaryTextColor':'#fff','primaryBorderColor':'#dc2626','lineColor':'#f87171','fontSize':'16px','fontFamily':'system-ui, sans-serif'}}}%%
sequenceDiagram
    autonumber
    participant App as Application
    participant FS as Firestore API
    participant DB as Cloud Firestore
    participant Storage as Data Storage
    
    Note over App,Storage: CREATE Operation
    App->>FS: db.collection("books").doc("B001").set({...})
    FS->>DB: Validate & Process
    DB->>Storage: Write Document
    Storage-->>DB: Confirm Write
    DB-->>FS: Success Response
    FS-->>App: Document Created
    
    Note over App,Storage: READ Operation
    App->>FS: db.collection("books").doc("B001").get()
    FS->>DB: Query Document
    DB->>Storage: Fetch Data
    Storage-->>DB: Return Data
    DB-->>FS: Document Data
    FS-->>App: Return Document
    
    Note over App,Storage: UPDATE Operation
    App->>FS: db.collection("users").doc("2018001").update({...})
    FS->>DB: Validate Changes
    DB->>Storage: Update Fields
    Storage-->>DB: Confirm Update
    DB-->>FS: Success Response
    FS-->>App: Document Updated
    
    Note over App,Storage: DELETE Operation (Not Implemented)
    App->>FS: db.collection("books").doc("B001").delete()
    FS->>DB: Process Deletion
    DB->>Storage: Remove Document
    Storage-->>DB: Confirm Deletion
    DB-->>FS: Success Response
    FS-->>App: Document Deleted
```

---

## 🔐 Security Rules

### Current Firestore Rules (Conceptual)

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Books Collection
    match /books/{bookId} {
      // Allow read access to all authenticated users
      allow read: if request.auth != null;
      
      // Allow write only to admin
      allow write: if request.auth != null 
                   && request.auth.token.email == 'admin@gmail.com';
    }
    
    // Users Collection
    match /users/{userId} {
      // Allow users to read their own data
      allow read: if request.auth != null 
                  && request.auth.token.email == resource.data.Email;
      
      // Allow admin to read all users
      allow read: if request.auth != null 
                  && request.auth.token.email == 'admin@gmail.com';
      
      // Allow users to update their own data
      allow update: if request.auth != null 
                    && request.auth.token.email == resource.data.Email;
      
      // Allow write for registration
      allow create: if request.auth != null;
    }
  }
}
```

---

## 📈 Data Growth Projections

### Scalability Considerations

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#8b5cf6','primaryTextColor':'#fff','primaryBorderColor':'#7c3aed','fontSize':'16px','fontFamily':'Verdana, sans-serif'}}}%%
graph TB
    START[Initial State<br/>Books: 100<br/>Users: 500] --> YEAR1[After 1 Year<br/>Books: 500<br/>Users: 2000]
    
    YEAR1 --> YEAR2[After 2 Years<br/>Books: 1500<br/>Users: 5000]
    
    YEAR2 --> YEAR3[After 3 Years<br/>Books: 3000<br/>Users: 10000]
    
    YEAR3 --> SCALE{Scaling<br/>Considerations}
    
    SCALE --> OPT1[Firestore Auto-scales<br/>✅ No action needed]
    SCALE --> OPT2[Index Optimization<br/>🔍 Create composite indexes]
    SCALE --> OPT3[Query Optimization<br/>⚡ Use pagination]
    
    style START fill:#fbbf24,stroke:#f59e0b,stroke-width:3px,color:#333
    style YEAR1 fill:#34d399,stroke:#10b981,stroke-width:2px,color:#fff
    style YEAR2 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#fff
    style YEAR3 fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style SCALE fill:#ef4444,stroke:#dc2626,stroke-width:3px,color:#fff
    style OPT1 fill:#10b981,stroke:#059669,stroke-width:2px,color:#fff
    style OPT2 fill:#f59e0b,stroke:#d97706,stroke-width:2px,color:#fff
    style OPT3 fill:#06b6d4,stroke:#0891b2,stroke-width:2px,color:#fff
```

### Storage Estimates

| Entity | Avg Size | 1000 Records | 10000 Records |
|--------|----------|--------------|---------------|
| **Book Document** | ~500 bytes | ~500 KB | ~5 MB |
| **User Document** | ~300 bytes | ~300 KB | ~3 MB |
| **Total (Mixed)** | - | ~800 KB | ~8 MB |

**Note**: Firestore scales automatically and pricing is based on reads/writes, not storage.

---

## 🔄 Backup & Recovery

### Firestore Automatic Features
- **Point-in-time Recovery**: Restore to any point in last 7 days
- **Multi-region Replication**: Automatic data replication
- **Automatic Backups**: Managed by Firebase
- **Export/Import**: Manual export for long-term backup

### Backup Strategy
1. **Firebase Console**: Use built-in export features
2. **Cloud Scheduler**: Automated exports to Cloud Storage
3. **Version Control**: Document structure changes in Git

---

## 📊 Database Statistics

### Current Implementation
- **Collections**: 2 (Books, Users)
- **Indexes**: Auto-generated by Firestore
- **Security Rules**: Configured for role-based access
- **Regions**: Multi-region by default
- **Replication**: Automatic

### Performance Metrics
- **Read Latency**: < 100ms (average)
- **Write Latency**: < 200ms (average)
- **Concurrent Users**: Scales automatically
- **Query Performance**: Optimized by Firestore indexes

---

## 🎯 Best Practices Implemented

✅ **Document ID Strategy**: Using meaningful IDs (bookcode, Roll_Number)

✅ **Data Denormalization**: Storing book codes in users array for quick access

✅ **Flat Structure**: Avoiding deep nesting for better performance

✅ **Required Fields**: Enforcing required fields at application level

✅ **Email Uniqueness**: Ensured through Firebase Authentication

✅ **Array Usage**: Using arrays for one-to-many relationships

---

**Document Version**: 1.0  
**Last Updated**: November 12, 2025  
**Next Document**: User Guide & Developer Reference
