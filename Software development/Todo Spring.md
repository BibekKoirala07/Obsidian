### **Week 1: HelloSpring API**

- Goal: Setup Spring Boot, use `@RestController`
    
- Build: `/hello` and `/add?a=3&b=4` API
    
- Tech: Spring Initializr, IntelliJ, annotations
    

### 🔹 **Week 2: Todo API (In-memory)**

- Goal: Learn `@PostMapping`, `@DeleteMapping`, `@RequestBody`
    
- Build: CRUD for todos (no DB)
    
- Tech: Java collections (`List<Todo>`), DTOs
    

### 🔹 **Week 3: Notes API + Layered Architecture**

- Goal: Add `@Service`, `@Autowired`
    
- Build: Notes CRUD with separate service layer
    
- Tech: Clean code principles
    

### 🔹 **Week 4: User API + H2 DB**

- Goal: Introduce `@Entity`, JPA, `JpaRepository`
    
- Build: Create, read, delete users with H2 database
    
- Tech: DB config, `application.properties`, `@Id`
    

---

## 🔁 Month 2 – Real DB + Validation + Relationships

### 🔹 **Week 5: Bookstore API**

- Goal: CRUD + Validation (`@NotBlank`, `@Size`, etc.)
    
- Build: Store books (title, price, stock)
    
- Tech: Validation, JPA
    

### 🔹 **Week 6: Author-Books Relationship**

- Goal: `@OneToMany`, `@ManyToOne`
    
- Build: Authors → Books (bi-directional)
    
- Tech: Join tables, cascade types
    

### 🔹 **Week 7: Course API + Exception Handling**

- Goal: Add `@ControllerAdvice` for global error handling
    
- Build: Courses with custom exception messages
    
- Tech: `ResponseEntity`, `@ExceptionHandler`
    

### 🔹 **Week 8: Task Manager (MySQL)**

- Goal: Use real MySQL instead of H2
    
- Build: Tasks with priorities and due dates
    
- Tech: DB config, migrations (optionally Liquibase/Flyway)
    

---

## 🔐 Month 3 – Production Patterns & Advanced Features

### 🔹 **Week 9: Login + Auth (Basic Security)**

- Goal: Add Spring Security + basic login
    
- Build: Simple `/login` with hardcoded users
    
- Tech: `UserDetailsService`, `BCrypt`, secured endpoints
    

### 🔹 **Week 10: JWT Auth + Protected API**

- Goal: Issue JWT token and secure endpoints
    
- Build: Auth + `/profile` API
    
- Tech: JWT filters, login/logout
    

### 🔹 **Week 11: DTO Mapping + Refactor**

- Goal: Use DTOs to return safe data
    
- Build: Refactor any old project
    
- Tech: ModelMapper or manual mapping
    

### 🔹 **Week 12: Capstone Project (You Choose)**

- Options:
    
    - Student Management System
        
    - Mini e-Commerce backend
        
    - Course Platform API
        
- Goal: Use everything you’ve learned so far
    

---

# ✅ Bonus Tips