# Hands-On Coding and Mini Project: SOLID Principles & Advanced C# Features
### Overview
### Objective: Create a small console application that manages a library system using SOLID principles and advanced C# features.

## Mini Project: Library Management System
### Project Requirements
#### Entities:

- Book class
- Member class
- Loan class
#### Repositories:

- IBookRepository interface
- IMemberRepository interface
- LoanManager class for managing loans

#### Services:

- ILoanService interface
- LoanService class to handle loan logic

#### Features:

- Add, remove, and list books.
- Register and remove members.
- Allow members to borrow and return books.

### Step 1: Setting Up the Project and Entities
#### Activity 1: Project Setup
Create a new Console Application project in Visual Studio. Set up a basic directory structure:
- Models for entity classes
- Repositories for repository interfaces and implementations
- Services for service interfaces and implementations
#### Activity 2: Implement Entities
#### Book Class:

```C#

public class Book {
    public int Id { get; set; }
    public string Title { get; set; }
    public string Author { get; set; }
    public bool IsAvailable { get; set; } = true; // Default to available
}
```
#### Member Class:

````C#
public class Member {
    public int Id { get; set; }
    public string Name { get; set; }
}

````
#### Loan Class:

```C# 
public class Loan {
    public Book Book { get; set; }
    public Member Member { get; set; }
    public DateTime LoanDate { get; set; }
}
```
### Step 2: Implementing Repositories and Services
#### Activity 3: Implement Repositories

#### IBookRepository Interface:

```C#
public interface IBookRepository {
    void Add(Book book);
    void Remove(int id);
    IEnumerable<Book> GetAllBooks();
    Book GetBookById(int id);
}
```
#### IMemberRepository Interface:

```C#
public interface IMemberRepository {
    void Register(Member member);
    void Remove(int id);
    IEnumerable<Member> GetAllMembers();
}
```

In-Memory Implementations (for simplicity):

```C# 

public class BookRepository : IBookRepository {
    private List<Book> books = new List<Book>();
    
    // Implement methods for adding, removing, and retrieving books
}

public class MemberRepository : IMemberRepository {
    private List<Member> members = new List<Member>();
    
    // Implement methods for registering, removing, and retrieving members
}
```

#### Activity 4: Implement Loan Service
#### ILoanService Interface:

```C# 
public interface ILoanService {
    void LoanBook(int bookId, int memberId);
    void ReturnBook(int bookId);
}
```
#### LoanService Class:

```C#

public class LoanService : ILoanService {
    private readonly IBookRepository bookRepo;
    private readonly IMemberRepository memberRepo;
    private List<Loan> loans = new List<Loan>();

    public LoanService(IBookRepository bookRepo, IMemberRepository memberRepo) {
        this.bookRepo = bookRepo;
        this.memberRepo = memberRepo;
    }

    public void LoanBook(int bookId, int memberId) {
        // Implement loan logic
    }

    public void ReturnBook(int bookId) {
        // Implement return logic
    }
}
```
### Step 3: Implementing Business Logic and User Interface

#### Activity 5: Implementing Business Logic
Complete the logic in LoanService for borrowing and returning books, ensuring to check the availability of books and managing the loan records.
#### Activity 6: User Interface
Create a simple text-based menu to interact with the application:

```C#

static void Main(string[] args) {
    // Initialize repositories and services
    // Display menu options for adding/removing books, registering members, and borrowing/returning books
}
```
### Final Deliverables
A fully functional console application that adheres to SOLID principles:
- Each class has a single responsibility.
- The repository interfaces allow for easy extension without modification.
- The loan logic is separated from the book and member management.

### Additional Activities
- Code Review: Review each other’s implementations to identify adherence to SOLID principles.
- Refactoring Session: Encourage participants to refactor their code based on feedback.

This structured approach ensures not only learn theoretical concepts but also apply them practically, reinforcing their understanding through hands-on coding and collaborative activities.