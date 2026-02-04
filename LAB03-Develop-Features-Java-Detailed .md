# LAB 03 - Develop Code Features with GitHub Copilot (Java)

## Description
Learn to use GitHub Copilot to develop complete features in a Java application.

**Estimated Duration**: 35 minutes

---

## Learning Objectives

By the end of this exercise, you will be able to:
- Use GitHub Copilot to generate complete Java classes
- Create business methods with GitHub Copilot
- Generate JUnit unit tests automatically
- Use inline suggestions and chat for code development
- Refactor code with GitHub Copilot assistance

---

## Prerequisites

- Visual Studio Code with the following extensions:
  - GitHub Copilot
  - GitHub Copilot Chat
  - Extension Pack for Java (Microsoft)
- Active GitHub Copilot subscription
- JDK 17 or higher installed
- Maven installed

---

## Exercise Scenario

You are developing a library management system. You need to implement the following features:

1. Book class with title, author, ISBN, and availability status
2. Library class to manage a collection of books
3. Loan management with loan and return methods
4. Search by title or author
5. Unit tests for all features

---

## Part 1: Create the Book Class with Inline Suggestions

### Step 1: Create the project structure

Open a terminal in VS Code and execute:

```bash
mvn archetype:generate -DgroupId=com.library -DartifactId=library-management -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd library-management
code .
```

This creates a Maven project with the following structure:

```
library-management/
├── pom.xml
└── src/
    ├── main/
    │   └── java/
    │       └── com/
    │           └── library/
    │               └── App.java
    └── test/
        └── java/
            └── com/
                └── library/
                    └── AppTest.java
```

### Step 2: Generate the Book class using inline suggestions

**Action**: Create a new file `src/main/java/com/library/Book.java`

**Prompt to type**:

```java
package com.library;

// Book class with title, author, ISBN, availability status
// Include getters, setters, constructor, and toString method
```

**Expected behavior**:
- After typing the comment, press Enter
- GitHub Copilot will display a grayed-out suggestion
- Press Tab to accept the suggestion

**Query 1**: Initial class structure suggestion

```java
package com.library;

// Book class with title, author, ISBN, availability status
// Include getters, setters, constructor, and toString method
public class Book {
    private String title;
    private String author;
    private String isbn;
    private boolean available;

    public Book(String title, String author, String isbn) {
        this.title = title;
        this.author = author;
        this.isbn = isbn;
        this.available = true;
    }
}
```

**Query 2**: Add getter methods

After accepting the constructor, type this comment:

```java
// Getters for all fields
```

**Expected suggestion**:

```java
// Getters for all fields
public String getTitle() {
    return title;
}

public String getAuthor() {
    return author;
}

public String getIsbn() {
    return isbn;
}

public boolean isAvailable() {
    return available;
}
```

**Query 3**: Add setter methods

Type this comment:

```java
// Setters for all fields
```

**Expected suggestion**:

```java
// Setters for all fields
public void setTitle(String title) {
    this.title = title;
}

public void setAuthor(String author) {
    this.author = author;
}

public void setIsbn(String isbn) {
    this.isbn = isbn;
}

public void setAvailable(boolean available) {
    this.available = available;
}
```

**Query 4**: Add business methods

Type this comment:

```java
// Method to loan this book - sets available to false
```

**Expected suggestion**:

```java
// Method to loan this book - sets available to false
public void loan() {
    this.available = false;
}
```

**Query 5**: Add return method

Type this comment:

```java
// Method to return this book - sets available to true
```

**Expected suggestion**:

```java
// Method to return this book - sets available to true
public void returnBook() {
    this.available = true;
}
```

**Query 6**: Add toString method

Type this comment:

```java
// Override toString to display book information
```

**Expected suggestion**:

```java
// Override toString to display book information
@Override
public String toString() {
    return "Book{" +
            "title='" + title + ''' +
            ", author='" + author + ''' +
            ", isbn='" + isbn + ''' +
            ", available=" + available +
            '}';
}
```

### Complete Book.java file

After all queries, your Book.java should look like this:

```java
package com.library;

public class Book {
    private String title;
    private String author;
    private String isbn;
    private boolean available;

    public Book(String title, String author, String isbn) {
        this.title = title;
        this.author = author;
        this.isbn = isbn;
        this.available = true;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public String getIsbn() {
        return isbn;
    }

    public void setIsbn(String isbn) {
        this.isbn = isbn;
    }

    public boolean isAvailable() {
        return available;
    }

    public void setAvailable(boolean available) {
        this.available = available;
    }

    public void loan() {
        this.available = false;
    }

    public void returnBook() {
        this.available = true;
    }

    @Override
    public String toString() {
        return "Book{" +
                "title='" + title + ''' +
                ", author='" + author + ''' +
                ", isbn='" + isbn + ''' +
                ", available=" + available +
                '}';
    }
}
```

---

## Part 2: Create the Library Class with Chat

### Step 1: Use GitHub Copilot Chat

**Action**: Open GitHub Copilot Chat
- Press `Ctrl+Shift+I` (Windows/Linux) or `Cmd+Shift+I` (Mac)
- Or click the chat icon in the Activity Bar

**Query 7**: Generate Library class structure

Type this prompt in the Chat view:

```
Create a Library class in Java that:
- Contains a list of Book objects
- Has methods to add and remove books
- Has a method to search books by title (case-insensitive)
- Has a method to search books by author (case-insensitive)
- Has a method to loan a book by ISBN
- Has a method to return a book by ISBN
- Handles cases when book is not found
```

**Expected response from Copilot**:

```java
package com.library;

import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

public class Library {
    private List<Book> books;

    public Library() {
        this.books = new ArrayList<>();
    }

    public void addBook(Book book) {
        books.add(book);
    }

    public boolean removeBook(String isbn) {
        return books.removeIf(book -> book.getIsbn().equals(isbn));
    }

    public List<Book> searchByTitle(String title) {
        return books.stream()
                .filter(book -> book.getTitle().toLowerCase().contains(title.toLowerCase()))
                .collect(Collectors.toList());
    }

    public List<Book> searchByAuthor(String author) {
        return books.stream()
                .filter(book -> book.getAuthor().toLowerCase().contains(author.toLowerCase()))
                .collect(Collectors.toList());
    }

    public boolean loanBook(String isbn) {
        Book book = books.stream()
                .filter(b -> b.getIsbn().equals(isbn))
                .findFirst()
                .orElse(null);

        if (book != null && book.isAvailable()) {
            book.loan();
            return true;
        }
        return false;
    }

    public boolean returnBook(String isbn) {
        Book book = books.stream()
                .filter(b -> b.getIsbn().equals(isbn))
                .findFirst()
                .orElse(null);

        if (book != null && !book.isAvailable()) {
            book.returnBook();
            return true;
        }
        return false;
    }

    public List<Book> getAllBooks() {
        return new ArrayList<>(books);
    }
}
```

**Action**: 
1. Create file `src/main/java/com/library/Library.java`
2. Copy the generated code
3. Save the file

### Step 2: Refine with Inline Chat

**Query 8**: Add method to get available books count

**Action**: 
1. Place cursor at the end of the Library class (before the closing brace)
2. Press `Ctrl+I` (Windows/Linux) or `Cmd+I` (Mac) to open inline chat
3. Type: "Add a method to count available books"

**Expected suggestion**:

```java
public int getAvailableBooksCount() {
    return (int) books.stream()
            .filter(Book::isAvailable)
            .count();
}
```

**Query 9**: Add method to get loaned books

**Action**: Open inline chat again and type:

```
Add a method to get all loaned books
```

**Expected suggestion**:

```java
public List<Book> getLoanedBooks() {
    return books.stream()
            .filter(book -> !book.isAvailable())
            .collect(Collectors.toList());
}
```

### Complete Library.java file

After all modifications:

```java
package com.library;

import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

public class Library {
    private List<Book> books;

    public Library() {
        this.books = new ArrayList<>();
    }

    public void addBook(Book book) {
        books.add(book);
    }

    public boolean removeBook(String isbn) {
        return books.removeIf(book -> book.getIsbn().equals(isbn));
    }

    public List<Book> searchByTitle(String title) {
        return books.stream()
                .filter(book -> book.getTitle().toLowerCase().contains(title.toLowerCase()))
                .collect(Collectors.toList());
    }

    public List<Book> searchByAuthor(String author) {
        return books.stream()
                .filter(book -> book.getAuthor().toLowerCase().contains(author.toLowerCase()))
                .collect(Collectors.toList());
    }

    public boolean loanBook(String isbn) {
        Book book = books.stream()
                .filter(b -> b.getIsbn().equals(isbn))
                .findFirst()
                .orElse(null);

        if (book != null && book.isAvailable()) {
            book.loan();
            return true;
        }
        return false;
    }

    public boolean returnBook(String isbn) {
        Book book = books.stream()
                .filter(b -> b.getIsbn().equals(isbn))
                .findFirst()
                .orElse(null);

        if (book != null && !book.isAvailable()) {
            book.returnBook();
            return true;
        }
        return false;
    }

    public List<Book> getAllBooks() {
        return new ArrayList<>(books);
    }

    public int getAvailableBooksCount() {
        return (int) books.stream()
                .filter(Book::isAvailable)
                .count();
    }

    public List<Book> getLoanedBooks() {
        return books.stream()
                .filter(book -> !book.isAvailable())
                .collect(Collectors.toList());
    }
}
```

---

## Part 3: Generate Unit Tests

### Step 1: Configure JUnit 5 in pom.xml

**Action**: Open `pom.xml` and replace the dependencies section:

```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.9.3</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Step 2: Generate tests for Book class

**Query 10**: Generate BookTest class

**Action**:
1. Open `Book.java`
2. Select the entire class (Ctrl+A or Cmd+A)
3. Open GitHub Copilot Chat
4. Type: `/tests Generate complete JUnit 5 tests for this class`

**Expected response**:

```java
package com.library;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import static org.junit.jupiter.api.Assertions.*;

class BookTest {

    private Book book;

    @BeforeEach
    void setUp() {
        book = new Book("Clean Code", "Robert Martin", "978-0132350884");
    }

    @Test
    void testBookConstructor() {
        assertNotNull(book);
        assertEquals("Clean Code", book.getTitle());
        assertEquals("Robert Martin", book.getAuthor());
        assertEquals("978-0132350884", book.getIsbn());
        assertTrue(book.isAvailable());
    }

    @Test
    void testLoanBook() {
        book.loan();
        assertFalse(book.isAvailable());
    }

    @Test
    void testReturnBook() {
        book.loan();
        book.returnBook();
        assertTrue(book.isAvailable());
    }

    @Test
    void testSetTitle() {
        book.setTitle("Effective Java");
        assertEquals("Effective Java", book.getTitle());
    }

    @Test
    void testSetAuthor() {
        book.setAuthor("Joshua Bloch");
        assertEquals("Joshua Bloch", book.getAuthor());
    }

    @Test
    void testSetIsbn() {
        book.setIsbn("978-0134685991");
        assertEquals("978-0134685991", book.getIsbn());
    }

    @Test
    void testToString() {
        String result = book.toString();
        assertTrue(result.contains("Clean Code"));
        assertTrue(result.contains("Robert Martin"));
        assertTrue(result.contains("978-0132350884"));
    }
}
```

**Action**: 
1. Create file `src/test/java/com/library/BookTest.java`
2. Copy the generated code
3. Save the file

### Step 3: Generate tests for Library class

**Query 11**: Generate LibraryTest class

**Action**:
1. Open `Library.java`
2. Select the entire class
3. In Chat, type:

```
/tests Create JUnit 5 tests for the Library class including:
- Test adding and removing books
- Test search methods
- Test loan and return operations
- Use appropriate assertions
```

**Expected response**:

```java
package com.library;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import java.util.List;
import static org.junit.jupiter.api.Assertions.*;

class LibraryTest {

    private Library library;
    private Book book1;
    private Book book2;
    private Book book3;

    @BeforeEach
    void setUp() {
        library = new Library();
        book1 = new Book("Clean Code", "Robert Martin", "978-0132350884");
        book2 = new Book("Effective Java", "Joshua Bloch", "978-0134685991");
        book3 = new Book("Design Patterns", "Gang of Four", "978-0201633610");

        library.addBook(book1);
        library.addBook(book2);
        library.addBook(book3);
    }

    @Test
    void testAddBook() {
        Book newBook = new Book("Refactoring", "Martin Fowler", "978-0134757599");
        library.addBook(newBook);

        List<Book> allBooks = library.getAllBooks();
        assertEquals(4, allBooks.size());
        assertTrue(allBooks.contains(newBook));
    }

    @Test
    void testRemoveBook() {
        boolean removed = library.removeBook("978-0132350884");

        assertTrue(removed);
        assertEquals(2, library.getAllBooks().size());
    }

    @Test
    void testRemoveNonExistentBook() {
        boolean removed = library.removeBook("999-9999999999");

        assertFalse(removed);
        assertEquals(3, library.getAllBooks().size());
    }

    @Test
    void testSearchByTitle() {
        List<Book> results = library.searchByTitle("Clean");

        assertEquals(1, results.size());
        assertEquals("Clean Code", results.get(0).getTitle());
    }

    @Test
    void testSearchByTitleCaseInsensitive() {
        List<Book> results = library.searchByTitle("clean");

        assertEquals(1, results.size());
        assertEquals("Clean Code", results.get(0).getTitle());
    }

    @Test
    void testSearchByAuthor() {
        List<Book> results = library.searchByAuthor("Martin");

        assertEquals(1, results.size());
        assertEquals("Robert Martin", results.get(0).getAuthor());
    }

    @Test
    void testSearchByAuthorCaseInsensitive() {
        List<Book> results = library.searchByAuthor("martin");

        assertEquals(1, results.size());
    }

    @Test
    void testLoanBook() {
        boolean loaned = library.loanBook("978-0132350884");

        assertTrue(loaned);
        assertFalse(book1.isAvailable());
    }

    @Test
    void testLoanAlreadyLoanedBook() {
        library.loanBook("978-0132350884");
        boolean loanedAgain = library.loanBook("978-0132350884");

        assertFalse(loanedAgain);
    }

    @Test
    void testLoanNonExistentBook() {
        boolean loaned = library.loanBook("999-9999999999");

        assertFalse(loaned);
    }

    @Test
    void testReturnBook() {
        library.loanBook("978-0132350884");
        boolean returned = library.returnBook("978-0132350884");

        assertTrue(returned);
        assertTrue(book1.isAvailable());
    }

    @Test
    void testReturnNotLoanedBook() {
        boolean returned = library.returnBook("978-0132350884");

        assertFalse(returned);
    }

    @Test
    void testGetAvailableBooksCount() {
        assertEquals(3, library.getAvailableBooksCount());

        library.loanBook("978-0132350884");
        assertEquals(2, library.getAvailableBooksCount());
    }

    @Test
    void testGetLoanedBooks() {
        library.loanBook("978-0132350884");
        library.loanBook("978-0134685991");

        List<Book> loanedBooks = library.getLoanedBooks();

        assertEquals(2, loanedBooks.size());
        assertFalse(loanedBooks.get(0).isAvailable());
        assertFalse(loanedBooks.get(1).isAvailable());
    }

    @Test
    void testGetAllBooks() {
        List<Book> allBooks = library.getAllBooks();

        assertEquals(3, allBooks.size());
    }
}
```

**Action**: 
1. Create file `src/test/java/com/library/LibraryTest.java`
2. Copy the generated code
3. Save the file

---

## Part 4: Refactor with GitHub Copilot

### Step 1: Add exception handling

**Query 12**: Add custom exception

**Action**: Open Chat and type:

```
Create a custom BookNotFoundException class that extends RuntimeException with constructors for message and message with cause
```

**Expected response**:

```java
package com.library;

public class BookNotFoundException extends RuntimeException {

    public BookNotFoundException(String message) {
        super(message);
    }

    public BookNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

**Action**: 
1. Create file `src/main/java/com/library/BookNotFoundException.java`
2. Copy the code
3. Save the file

**Query 13**: Update Library class to use exception

**Action**:
1. Open `Library.java`
2. Select the `loanBook` method
3. Open Inline Chat (Ctrl+I or Cmd+I)
4. Type: "Throw BookNotFoundException if book with this ISBN doesn't exist"

**Expected modification**:

```java
public boolean loanBook(String isbn) {
    Book book = books.stream()
            .filter(b -> b.getIsbn().equals(isbn))
            .findFirst()
            .orElseThrow(() -> new BookNotFoundException("Book with ISBN " + isbn + " not found"));

    if (book.isAvailable()) {
        book.loan();
        return true;
    }
    return false;
}
```

**Query 14**: Update returnBook method

**Action**: Repeat the same for `returnBook` method

```java
public boolean returnBook(String isbn) {
    Book book = books.stream()
            .filter(b -> b.getIsbn().equals(isbn))
            .findFirst()
            .orElseThrow(() -> new BookNotFoundException("Book with ISBN " + isbn + " not found"));

    if (!book.isAvailable()) {
        book.returnBook();
        return true;
    }
    return false;
}
```

---

## Part 5: Testing and Validation

### Step 1: Run the tests

**Action**: Open terminal in VS Code and run:

```bash
mvn clean test
```

**Expected output**:

```
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running com.library.BookTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.library.LibraryTest
[INFO] Tests run: 15, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] Results:
[INFO] 
[INFO] Tests run: 22, Failures: 0, Errors: 0, Skipped: 0
[INFO]
[INFO] BUILD SUCCESS
```

### Step 2: Create a Main class for demonstration

**Query 15**: Generate Main class

**Action**: Open Chat and type:

```
Create a Main class with a main method that demonstrates:
- Creating a library
- Adding several books
- Searching for books by title
- Loaning a book
- Trying to loan an already loaned book
- Returning a book
- Displaying all available and loaned books
```

**Expected response**:

```java
package com.library;

import java.util.List;

public class Main {
    public static void main(String[] args) {
        // Create a new library
        Library library = new Library();

        // Add books
        System.out.println("Adding books to the library...");
        library.addBook(new Book("Clean Code", "Robert Martin", "978-0132350884"));
        library.addBook(new Book("Effective Java", "Joshua Bloch", "978-0134685991"));
        library.addBook(new Book("Design Patterns", "Gang of Four", "978-0201633610"));
        library.addBook(new Book("Refactoring", "Martin Fowler", "978-0134757599"));

        System.out.println("Total books: " + library.getAllBooks().size());
        System.out.println("Available books: " + library.getAvailableBooksCount());
        System.out.println();

        // Search by title
        System.out.println("Searching for 'Clean'...");
        List<Book> searchResults = library.searchByTitle("Clean");
        searchResults.forEach(System.out::println);
        System.out.println();

        // Search by author
        System.out.println("Searching for author 'Martin'...");
        searchResults = library.searchByAuthor("Martin");
        searchResults.forEach(System.out::println);
        System.out.println();

        // Loan a book
        System.out.println("Loaning 'Clean Code'...");
        boolean loaned = library.loanBook("978-0132350884");
        System.out.println("Loan successful: " + loaned);
        System.out.println("Available books: " + library.getAvailableBooksCount());
        System.out.println();

        // Try to loan the same book again
        System.out.println("Trying to loan 'Clean Code' again...");
        loaned = library.loanBook("978-0132350884");
        System.out.println("Loan successful: " + loaned);
        System.out.println();

        // Display loaned books
        System.out.println("Currently loaned books:");
        library.getLoanedBooks().forEach(System.out::println);
        System.out.println();

        // Return a book
        System.out.println("Returning 'Clean Code'...");
        boolean returned = library.returnBook("978-0132350884");
        System.out.println("Return successful: " + returned);
        System.out.println("Available books: " + library.getAvailableBooksCount());
        System.out.println();

        // Display all books
        System.out.println("All books in library:");
        library.getAllBooks().forEach(System.out::println);

        // Try exception handling
        System.out.println("
Trying to loan non-existent book...");
        try {
            library.loanBook("999-9999999999");
        } catch (BookNotFoundException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

**Action**: 
1. Replace content of `src/main/java/com/library/App.java` with Main.java content
2. Or create new `Main.java` file
3. Save the file

### Step 3: Run the application

**Action**: Execute in terminal:

```bash
mvn clean compile exec:java -Dexec.mainClass="com.library.Main"
```

**Expected output**:

```
Adding books to the library...
Total books: 4
Available books: 4

Searching for 'Clean'...
Book{title='Clean Code', author='Robert Martin', isbn='978-0132350884', available=true}

Searching for author 'Martin'...
Book{title='Clean Code', author='Robert Martin', isbn='978-0132350884', available=true}
Book{title='Refactoring', author='Martin Fowler', isbn='978-0134757599', available=true}

Loaning 'Clean Code'...
Loan successful: true
Available books: 3

Trying to loan 'Clean Code' again...
Loan successful: false

Currently loaned books:
Book{title='Clean Code', author='Robert Martin', isbn='978-0132350884', available=false}

Returning 'Clean Code'...
Return successful: true
Available books: 4

All books in library:
Book{title='Clean Code', author='Robert Martin', isbn='978-0132350884', available=true}
Book{title='Effective Java', author='Joshua Bloch', isbn='978-0134685991', available=true}
Book{title='Design Patterns', author='Gang of Four', isbn='978-0201633610', available=true}
Book{title='Refactoring', author='Martin Fowler', isbn='978-0134757599', available=true}

Trying to loan non-existent book...
Error: Book with ISBN 999-9999999999 not found
```

---

## Summary of All Queries Used

| Query | Purpose | Method | Result |
|-------|---------|--------|--------|
| Query 1 | Create Book class structure | Inline suggestions | Basic class with fields and constructor |
| Query 2 | Add getter methods | Inline suggestions | All getter methods |
| Query 3 | Add setter methods | Inline suggestions | All setter methods |
| Query 4 | Add loan method | Inline suggestions | loan() method |
| Query 5 | Add return method | Inline suggestions | returnBook() method |
| Query 6 | Add toString method | Inline suggestions | toString() override |
| Query 7 | Create Library class | Chat view | Complete Library class |
| Query 8 | Add available count method | Inline chat | getAvailableBooksCount() |
| Query 9 | Add loaned books method | Inline chat | getLoanedBooks() |
| Query 10 | Generate Book tests | Chat /tests | BookTest.java |
| Query 11 | Generate Library tests | Chat /tests | LibraryTest.java |
| Query 12 | Create exception class | Chat view | BookNotFoundException |
| Query 13 | Update loanBook with exception | Inline chat | Modified loanBook() |
| Query 14 | Update returnBook with exception | Inline chat | Modified returnBook() |
| Query 15 | Create demo Main class | Chat view | Main.java demo |

---

## Verification Questions

1. How many suggestions did GitHub Copilot provide for each class?
   - Answer: Multiple suggestions per query, typically 1-3 alternatives

2. Did you need to modify the generated code?
   - Answer: Minor modifications may be needed for specific requirements

3. How did Inline Chat help refine the code?
   - Answer: Quick targeted changes without leaving the editor

4. Did the generated tests cover all cases?
   - Answer: Most cases covered, additional edge cases can be added

---

## Additional Challenge (Optional)

Use GitHub Copilot to add the following features:

### Challenge 1: Add Reservation System

**Prompt**: "Add a reservation system that allows patrons to reserve books that are currently loaned"

### Challenge 2: Add Late Fee Calculation

**Prompt**: "Add a loan date and calculate late fees based on days overdue"

### Challenge 3: Add Book Categories

**Prompt**: "Add categories to books (Fiction, Non-Fiction, Science, etc.) and filtering by category"

### Challenge 4: Add Persistence

**Prompt**: "Add methods to save and load library data from JSON file using Gson"

---

## Project Structure Final

```
library-management/
├── pom.xml
└── src/
    ├── main/
    │   └── java/
    │       └── com/
    │           └── library/
    │               ├── Book.java
    │               ├── Library.java
    │               ├── BookNotFoundException.java
    │               └── Main.java
    └── test/
        └── java/
            └── com/
                └── library/
                    ├── BookTest.java
                    └── LibraryTest.java
```

---

## Cleanup

If you want to delete the project:

```bash
cd ..
rm -rf library-management
```

---

## Key Takeaways

In this lab, you learned to:
- Generate complete Java classes with GitHub Copilot inline suggestions
- Use Chat view for complex features requiring multiple classes
- Use Inline Chat for quick targeted refinements
- Generate comprehensive unit tests with /tests command
- Refactor code with AI assistance
- Handle exceptions and edge cases
- Create demonstration applications

---

## Additional Resources

- [GitHub Copilot Documentation](https://docs.github.com/copilot)
- [JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)
- [Maven Getting Started](https://maven.apache.org/guides/getting-started/)
- [Java Streams API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)
- [Java Lambda Expressions](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)
