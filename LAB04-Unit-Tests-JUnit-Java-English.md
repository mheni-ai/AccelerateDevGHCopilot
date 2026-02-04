# LAB 04 - Develop Unit Tests with JUnit (Java)

## Description
Learn to use GitHub Copilot to create complete and maintainable JUnit 5 unit tests.

**Estimated Duration**: 30 minutes

---

## Learning Objectives

By the end of this exercise, you will be able to:
- Generate JUnit 5 tests with GitHub Copilot
- Create parameterized tests with `@ParameterizedTest`
- Use Mockito for mocks and stubs
- Implement complex assertions
- Organize tests with nested classes
- Use GitHub Copilot to improve test coverage

---

## Prerequisites

- Visual Studio Code with extensions:
  - GitHub Copilot
  - GitHub Copilot Chat
  - Extension Pack for Java
- Active GitHub Copilot subscription
- JDK 17 or higher
- Maven
- Basic knowledge of JUnit 5

---

## Exercise Scenario

You have a calculator application with various operations. You need to create comprehensive unit tests to ensure code quality and facilitate future refactoring.

---

## Part 1: Setup the Test Project

### Step 1: Create the Maven project

```bash
mvn archetype:generate -DgroupId=com.calculator -DartifactId=calculator-tests -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd calculator-tests
```

### Step 2: Configure pom.xml

Add these dependencies to `pom.xml`:

```xml
<dependencies>
    <!-- JUnit 5 -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.9.3</version>
        <scope>test</scope>
    </dependency>

    <!-- Mockito -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-core</artifactId>
        <version>5.3.1</version>
        <scope>test</scope>
    </dependency>

    <!-- Mockito JUnit Jupiter -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-junit-jupiter</artifactId>
        <version>5.3.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Step 3: Create the Calculator class

Create `src/main/java/com/calculator/Calculator.java`:

```java
package com.calculator;

public class Calculator {

    public double add(double a, double b) {
        return a + b;
    }

    public double subtract(double a, double b) {
        return a - b;
    }

    public double multiply(double a, double b) {
        return a * b;
    }

    public double divide(double a, double b) {
        if (b == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return a / b;
    }

    public double power(double base, double exponent) {
        return Math.pow(base, exponent);
    }

    public double squareRoot(double number) {
        if (number < 0) {
            throw new IllegalArgumentException("Cannot calculate square root of negative number");
        }
        return Math.sqrt(number);
    }
}
```

---

## Part 2: Generate Basic Tests with GitHub Copilot

### Step 1: Generate the test class structure

1. Create `src/test/java/com/calculator/CalculatorTest.java`
2. Write this comment:

```java
// Complete JUnit 5 test class for Calculator
// Test all methods with various cases
// Include edge cases and exception handling
```

3. Press Enter and accept Copilot suggestions

### Step 2: Review and complete the tests

GitHub Copilot should generate something like:

```java
package com.calculator;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.DisplayName;
import static org.junit.jupiter.api.Assertions.*;

@DisplayName("Calculator Tests")
class CalculatorTest {

    private Calculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }

    @Test
    @DisplayName("Test addition of two positive numbers")
    void testAddPositiveNumbers() {
        assertEquals(5.0, calculator.add(2.0, 3.0));
    }

    @Test
    @DisplayName("Test addition with negative numbers")
    void testAddNegativeNumbers() {
        assertEquals(-5.0, calculator.add(-2.0, -3.0));
    }

    @Test
    @DisplayName("Test subtraction")
    void testSubtract() {
        assertEquals(1.0, calculator.subtract(3.0, 2.0));
    }

    @Test
    @DisplayName("Test multiplication")
    void testMultiply() {
        assertEquals(6.0, calculator.multiply(2.0, 3.0));
    }

    @Test
    @DisplayName("Test division")
    void testDivide() {
        assertEquals(2.0, calculator.divide(6.0, 3.0));
    }

    @Test
    @DisplayName("Test division by zero throws exception")
    void testDivideByZero() {
        assertThrows(ArithmeticException.class, () -> {
            calculator.divide(10.0, 0.0);
        });
    }

    @Test
    @DisplayName("Test power calculation")
    void testPower() {
        assertEquals(8.0, calculator.power(2.0, 3.0));
    }

    @Test
    @DisplayName("Test square root")
    void testSquareRoot() {
        assertEquals(4.0, calculator.squareRoot(16.0));
    }

    @Test
    @DisplayName("Test square root of negative number throws exception")
    void testSquareRootNegative() {
        assertThrows(IllegalArgumentException.class, () -> {
            calculator.squareRoot(-1.0);
        });
    }
}
```

---

## Part 3: Add Parameterized Tests

### Step 1: Generate parameterized tests with Copilot

1. In the test class, add this comment:

```java
// Add parameterized tests for addition with multiple test cases
// Test cases: (1,2,3), (0,0,0), (-1,1,0), (100,200,300)
```

2. Accept the Copilot suggestion

### Expected Result

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

@ParameterizedTest
@DisplayName("Parameterized test for addition")
@CsvSource({
    "1.0, 2.0, 3.0",
    "0.0, 0.0, 0.0",
    "-1.0, 1.0, 0.0",
    "100.0, 200.0, 300.0"
})
void testAddParameterized(double a, double b, double expected) {
    assertEquals(expected, calculator.add(a, b));
}
```

### Step 2: Add more parameterized tests

Use GitHub Copilot Chat to generate:

```
Create parameterized tests for the divide method with the following cases:
- Normal division: 10/2=5, 20/4=5
- Division with decimals: 7/2=3.5
- Division by 1: 5/1=5
Include appropriate assertions
```

---

## Part 4: Organize Tests with Nested Classes

### Step 1: Use GitHub Copilot to restructure

1. Open GitHub Copilot Chat
2. Select all test code
3. Enter this prompt:

```
Refactor these tests using @Nested classes to organize by functionality:
- ArithmeticOperations (add, subtract, multiply, divide)
- AdvancedOperations (power, squareRoot)
- ExceptionHandling (division by zero, negative square root)
```

4. Accept and apply the suggestions

### Expected Result

```java
@DisplayName("Calculator Tests")
class CalculatorTest {

    private Calculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }

    @Nested
    @DisplayName("Arithmetic Operations")
    class ArithmeticOperations {

        @Test
        @DisplayName("Test addition")
        void testAdd() {
            assertEquals(5.0, calculator.add(2.0, 3.0));
        }

        @Test
        @DisplayName("Test subtraction")
        void testSubtract() {
            assertEquals(1.0, calculator.subtract(3.0, 2.0));
        }

        @Test
        @DisplayName("Test multiplication")
        void testMultiply() {
            assertEquals(6.0, calculator.multiply(2.0, 3.0));
        }

        @Test
        @DisplayName("Test division")
        void testDivide() {
            assertEquals(2.0, calculator.divide(6.0, 3.0));
        }
    }

    @Nested
    @DisplayName("Advanced Operations")
    class AdvancedOperations {

        @Test
        @DisplayName("Test power")
        void testPower() {
            assertEquals(8.0, calculator.power(2.0, 3.0));
        }

        @Test
        @DisplayName("Test square root")
        void testSquareRoot() {
            assertEquals(4.0, calculator.squareRoot(16.0));
        }
    }

    @Nested
    @DisplayName("Exception Handling")
    class ExceptionHandling {

        @Test
        @DisplayName("Division by zero")
        void testDivideByZero() {
            ArithmeticException exception = assertThrows(
                ArithmeticException.class,
                () -> calculator.divide(10.0, 0.0)
            );
            assertEquals("Cannot divide by zero", exception.getMessage());
        }

        @Test
        @DisplayName("Square root of negative number")
        void testSquareRootNegative() {
            IllegalArgumentException exception = assertThrows(
                IllegalArgumentException.class,
                () -> calculator.squareRoot(-1.0)
            );
            assertTrue(exception.getMessage().contains("negative"));
        }
    }
}
```

---

## Part 5: Add Mocks with Mockito

### Step 1: Create a service to mock

Create `src/main/java/com/calculator/CalculatorService.java`:

```java
package com.calculator;

public class CalculatorService {
    private Calculator calculator;

    public CalculatorService(Calculator calculator) {
        this.calculator = calculator;
    }

    public double calculateTotal(double[] numbers) {
        double total = 0;
        for (double num : numbers) {
            total = calculator.add(total, num);
        }
        return total;
    }

    public double calculateAverage(double[] numbers) {
        if (numbers.length == 0) {
            throw new IllegalArgumentException("Array cannot be empty");
        }
        double total = calculateTotal(numbers);
        return calculator.divide(total, numbers.length);
    }
}
```

### Step 2: Generate tests with mocks

1. Create `CalculatorServiceTest.java`
2. Use GitHub Copilot Chat:

```
Create JUnit 5 tests for CalculatorService using Mockito to mock the Calculator dependency.
Include tests for:
- calculateTotal with various arrays
- calculateAverage with normal cases
- calculateAverage with empty array
Use @Mock and @InjectMocks annotations
```

### Expected Result

```java
package com.calculator;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.anyDouble;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
@DisplayName("Calculator Service Tests")
class CalculatorServiceTest {

    @Mock
    private Calculator calculator;

    @InjectMocks
    private CalculatorService calculatorService;

    @Test
    @DisplayName("Test calculate total")
    void testCalculateTotal() {
        when(calculator.add(anyDouble(), anyDouble()))
            .thenAnswer(invocation -> {
                double a = invocation.getArgument(0);
                double b = invocation.getArgument(1);
                return a + b;
            });

        double[] numbers = {1.0, 2.0, 3.0};
        double result = calculatorService.calculateTotal(numbers);

        assertEquals(6.0, result);
        verify(calculator, times(3)).add(anyDouble(), anyDouble());
    }

    @Test
    @DisplayName("Test calculate average")
    void testCalculateAverage() {
        when(calculator.add(anyDouble(), anyDouble()))
            .thenAnswer(invocation -> {
                double a = invocation.getArgument(0);
                double b = invocation.getArgument(1);
                return a + b;
            });
        when(calculator.divide(6.0, 3.0)).thenReturn(2.0);

        double[] numbers = {1.0, 2.0, 3.0};
        double result = calculatorService.calculateAverage(numbers);

        assertEquals(2.0, result);
    }

    @Test
    @DisplayName("Test calculate average with empty array")
    void testCalculateAverageEmptyArray() {
        double[] numbers = {};

        assertThrows(IllegalArgumentException.class, () -> {
            calculatorService.calculateAverage(numbers);
        });
    }
}
```

---

## Part 6: Test Coverage Analysis

### Step 1: Run tests with coverage

```bash
mvn test
mvn jacoco:report
```

### Step 2: Use GitHub Copilot to improve coverage

1. Open GitHub Copilot Chat
2. Ask:

```
Analyze my test class and suggest additional test cases to improve coverage:
- Edge cases not covered
- Boundary conditions
- Error scenarios
```

3. Implement the suggested tests

---

## Part 7: Best Practices with GitHub Copilot

### Use descriptive comments

Instead of:
```java
// test add
```

Use:
```java
// Test addition of two positive integers returns correct sum
```

### Leverage slash commands

In Chat:
- `/tests` - Generate tests
- `/explain` - Explain test logic
- `/fix` - Fix failing tests

### Review and refine

Always review generated tests and ensure they:
- Have meaningful assertions
- Cover edge cases
- Use appropriate test data
- Follow naming conventions

---

## Verification Checklist

- [ ] All basic operations are tested
- [ ] Parameterized tests are working
- [ ] Exception cases are covered
- [ ] Nested classes organize tests logically
- [ ] Mocks are used correctly
- [ ] Tests have descriptive names
- [ ] All tests pass successfully

---

## Additional Challenge (Optional)

Use GitHub Copilot to create:

1. **Performance tests** using `@RepeatedTest`
2. **Timeout tests** using `@Timeout`
3. **Conditional tests** using `@EnabledOnOs`
4. **Test suites** with `@Suite`

---

## Summary

In this lab, you learned to:
- ✅ Generate comprehensive JUnit 5 tests
- ✅ Create parameterized tests
- ✅ Use Mockito for mocking dependencies
- ✅ Organize tests with nested classes
- ✅ Improve test coverage with AI assistance
- ✅ Apply testing best practices

---

## Cleanup

```bash
cd ..
rm -rf calculator-tests
```

---

## Additional Resources

- [JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)
- [Mockito Documentation](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html)
- [GitHub Copilot Best Practices](https://docs.github.com/en/copilot/using-github-copilot/best-practices-for-using-github-copilot)
- [Maven Surefire Plugin](https://maven.apache.org/surefire/maven-surefire-plugin/)
