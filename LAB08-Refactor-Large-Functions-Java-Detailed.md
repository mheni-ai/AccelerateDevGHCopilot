# LAB 08 - Refactor Large Functions with GitHub Copilot (Java)

## Description
Learn to identify and refactor methods that are too long into smaller, cohesive, and testable methods with the help of GitHub Copilot.

**Estimated Duration**: 30 minutes

---

## Learning Objectives

By the end of this exercise, you will be able to:
- Identify methods violating the Single Responsibility Principle
- Extract cohesive methods with GitHub Copilot
- Improve code readability and testability
- Apply best practices for function decomposition

---

## Prerequisites

- JDK 17+, Maven 3.8+
- VS Code with Extension Pack for Java
- GitHub Copilot enabled
- Knowledge of SOLID principles

---

## Exercise Scenario

You inherit a `ReportGenerator` class that contains a `generateReport()` method of 150 lines that does too many things:
- Data retrieval
- Validation
- Statistical calculations
- Formatting
- File generation
- Email sending

Your mission: decompose this method into smaller, cohesive methods.

---

## Part 1: Analyze Legacy Code

### Initial Code: ReportGenerator.java (BEFORE)

Create a new Maven project:

```bash
mvn archetype:generate -DgroupId=com.example -DartifactId=refactor-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd refactor-demo
```

Create file `src/main/java/com/example/reporting/ReportGenerator.java`:

```java
package com.example.reporting;

import java.io.*;
import java.time.*;
import java.util.*;
import java.util.stream.*;

public class ReportGenerator {

    public void generateReport(String reportType, LocalDate startDate, LocalDate endDate) {
        // LINES 1-20: Validation
        if (reportType == null || reportType.isEmpty()) {
            throw new IllegalArgumentException("Report type cannot be null or empty");
        }
        if (startDate == null || endDate == null) {
            throw new IllegalArgumentException("Dates cannot be null");
        }
        if (startDate.isAfter(endDate)) {
            throw new IllegalArgumentException("Start date must be before end date");
        }
        if (!Arrays.asList("SALES", "INVENTORY", "FINANCIAL").contains(reportType)) {
            throw new IllegalArgumentException("Invalid report type: " + reportType);
        }

        // LINES 21-40: Data retrieval
        List<Map<String, Object>> rawData = new ArrayList<>();
        try {
            // Simulate database call
            for (LocalDate date = startDate; !date.isAfter(endDate); date = date.plusDays(1)) {
                Map<String, Object> record = new HashMap<>();
                record.put("date", date);
                record.put("amount", Math.random() * 10000);
                record.put("quantity", (int)(Math.random() * 100));
                record.put("category", reportType);
                rawData.add(record);
            }
        } catch (Exception e) {
            System.err.println("Error retrieving data: " + e.getMessage());
            throw new RuntimeException("Failed to retrieve data", e);
        }

        // LINES 41-60: Data filtering and cleaning
        List<Map<String, Object>> cleanedData = new ArrayList<>();
        for (Map<String, Object> record : rawData) {
            if (record.get("amount") != null && (double)record.get("amount") > 0) {
                if (record.get("quantity") != null && (int)record.get("quantity") > 0) {
                    cleanedData.add(record);
                }
            }
        }

        // LINES 61-80: Statistical calculations
        double total = 0;
        double average = 0;
        double min = Double.MAX_VALUE;
        double max = Double.MIN_VALUE;
        int count = 0;

        for (Map<String, Object> record : cleanedData) {
            double amount = (double) record.get("amount");
            total += amount;
            count++;
            if (amount < min) min = amount;
            if (amount > max) max = amount;
        }

        if (count > 0) {
            average = total / count;
        }

        // LINES 81-100: Calculate percentages and trends
        double variance = 0;
        for (Map<String, Object> record : cleanedData) {
            double amount = (double) record.get("amount");
            variance += Math.pow(amount - average, 2);
        }
        variance = variance / count;
        double stdDeviation = Math.sqrt(variance);

        // LINES 101-120: Format report content
        StringBuilder reportContent = new StringBuilder();
        reportContent.append("========================================\n");
        reportContent.append("         " + reportType + " REPORT\n");
        reportContent.append("========================================\n");
        reportContent.append("Period: " + startDate + " to " + endDate + "\n");
        reportContent.append("----------------------------------------\n");
        reportContent.append("Total Records: " + count + "\n");
        reportContent.append("Total Amount: $" + String.format("%.2f", total) + "\n");
        reportContent.append("Average Amount: $" + String.format("%.2f", average) + "\n");
        reportContent.append("Min Amount: $" + String.format("%.2f", min) + "\n");
        reportContent.append("Max Amount: $" + String.format("%.2f", max) + "\n");
        reportContent.append("Standard Deviation: $" + String.format("%.2f", stdDeviation) + "\n");
        reportContent.append("----------------------------------------\n");
        reportContent.append("\nDetailed Records:\n");

        for (Map<String, Object> record : cleanedData) {
            reportContent.append(String.format("Date: %s, Amount: $%.2f, Quantity: %d\n",
                record.get("date"),
                (double)record.get("amount"),
                (int)record.get("quantity")));
        }
        reportContent.append("========================================\n");

        // LINES 121-140: Save to file
        String fileName = reportType + "_" + startDate + "_to_" + endDate + ".txt";
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(fileName))) {
            writer.write(reportContent.toString());
            System.out.println("Report saved to: " + fileName);
        } catch (IOException e) {
            System.err.println("Error writing report: " + e.getMessage());
            throw new RuntimeException("Failed to write report", e);
        }

        // LINES 141-150: Send email notification
        String emailBody = "Report generated successfully.\n\n" + reportContent.toString();
        try {
            // Simulate email sending
            System.out.println("Sending email notification...");
            System.out.println("To: admin@example.com");
            System.out.println("Subject: " + reportType + " Report Generated");
            System.out.println("Email sent successfully!");
        } catch (Exception e) {
            System.err.println("Warning: Failed to send email notification");
        }
    }
}
```

### Problem Analysis

This method violates several principles:

1. **Single Responsibility Principle**: Does validation, data retrieval, calculations, formatting, file I/O, and email
2. **Too Long**: 150 lines make it hard to read and maintain
3. **Hard to Test**: Cannot test individual parts separately
4. **Hard to Reuse**: Cannot reuse validation or calculation logic
5. **Poor Maintainability**: Changes in one area affect the entire method

---

## Part 2: Refactor with GitHub Copilot - Step by Step

### Step 1: Extract Validation Logic

**Query 1**: Extract validation into separate method

**Action**:
1. Open `ReportGenerator.java`
2. Select lines 12-24 (validation code)
3. Open Inline Chat (Ctrl+I or Cmd+I)
4. Type: "Extract this validation logic into a private method called validateInputs"

**Expected Result**:

```java
private void validateInputs(String reportType, LocalDate startDate, LocalDate endDate) {
    if (reportType == null || reportType.isEmpty()) {
        throw new IllegalArgumentException("Report type cannot be null or empty");
    }
    if (startDate == null || endDate == null) {
        throw new IllegalArgumentException("Dates cannot be null");
    }
    if (startDate.isAfter(endDate)) {
        throw new IllegalArgumentException("Start date must be before end date");
    }
    if (!Arrays.asList("SALES", "INVENTORY", "FINANCIAL").contains(reportType)) {
        throw new IllegalArgumentException("Invalid report type: " + reportType);
    }
}
```

Replace the validation block in `generateReport` with:

```java
public void generateReport(String reportType, LocalDate startDate, LocalDate endDate) {
    validateInputs(reportType, startDate, endDate);

    // Rest of the code...
}
```

---

### Step 2: Extract Data Retrieval

**Query 2**: Extract data retrieval logic

**Action**:
1. Select lines 26-39 (data retrieval code)
2. Open Inline Chat
3. Type: "Extract data retrieval into a method that returns List<Map<String, Object>>"

**Expected Result**:

```java
private List<Map<String, Object>> retrieveRawData(String reportType, LocalDate startDate, LocalDate endDate) {
    List<Map<String, Object>> rawData = new ArrayList<>();
    try {
        for (LocalDate date = startDate; !date.isAfter(endDate); date = date.plusDays(1)) {
            Map<String, Object> record = new HashMap<>();
            record.put("date", date);
            record.put("amount", Math.random() * 10000);
            record.put("quantity", (int)(Math.random() * 100));
            record.put("category", reportType);
            rawData.add(record);
        }
    } catch (Exception e) {
        System.err.println("Error retrieving data: " + e.getMessage());
        throw new RuntimeException("Failed to retrieve data", e);
    }
    return rawData;
}
```

Update `generateReport`:

```java
List<Map<String, Object>> rawData = retrieveRawData(reportType, startDate, endDate);
```

---

### Step 3: Extract Data Cleaning

**Query 3**: Extract data cleaning logic

**Action**:
1. Select the data filtering code
2. Open Inline Chat
3. Type: "Extract data cleaning into a method that filters valid records"

**Expected Result**:

```java
private List<Map<String, Object>> cleanData(List<Map<String, Object>> rawData) {
    List<Map<String, Object>> cleanedData = new ArrayList<>();
    for (Map<String, Object> record : rawData) {
        if (record.get("amount") != null && (double)record.get("amount") > 0) {
            if (record.get("quantity") != null && (int)record.get("quantity") > 0) {
                cleanedData.add(record);
            }
        }
    }
    return cleanedData;
}
```

**Query 4**: Improve data cleaning with streams

**Action**:
1. Select the `cleanData` method
2. Open Chat view
3. Type: "Refactor this method to use Java streams for better readability"

**Expected Result**:

```java
private List<Map<String, Object>> cleanData(List<Map<String, Object>> rawData) {
    return rawData.stream()
        .filter(record -> record.get("amount") != null && (double)record.get("amount") > 0)
        .filter(record -> record.get("quantity") != null && (int)record.get("quantity") > 0)
        .collect(Collectors.toList());
}
```

---

### Step 4: Extract Statistical Calculations

**Query 5**: Create a Statistics class

**Action**:
1. Open Chat view
2. Type: "Create a ReportStatistics class to hold total, average, min, max, count, and standard deviation"

**Expected Result**:

Create `src/main/java/com/example/reporting/ReportStatistics.java`:

```java
package com.example.reporting;

public class ReportStatistics {
    private final double total;
    private final double average;
    private final double min;
    private final double max;
    private final int count;
    private final double stdDeviation;

    public ReportStatistics(double total, double average, double min, double max, 
                           int count, double stdDeviation) {
        this.total = total;
        this.average = average;
        this.min = min;
        this.max = max;
        this.count = count;
        this.stdDeviation = stdDeviation;
    }

    public double getTotal() { return total; }
    public double getAverage() { return average; }
    public double getMin() { return min; }
    public double getMax() { return max; }
    public int getCount() { return count; }
    public double getStdDeviation() { return stdDeviation; }
}
```

**Query 6**: Extract calculation logic

**Action**:
1. Select the statistics calculation code
2. Open Inline Chat
3. Type: "Extract statistics calculation into a method returning ReportStatistics"

**Expected Result**:

```java
private ReportStatistics calculateStatistics(List<Map<String, Object>> data) {
    double total = 0;
    double min = Double.MAX_VALUE;
    double max = Double.MIN_VALUE;
    int count = 0;

    for (Map<String, Object> record : data) {
        double amount = (double) record.get("amount");
        total += amount;
        count++;
        if (amount < min) min = amount;
        if (amount > max) max = amount;
    }

    double average = count > 0 ? total / count : 0;

    double variance = 0;
    for (Map<String, Object> record : data) {
        double amount = (double) record.get("amount");
        variance += Math.pow(amount - average, 2);
    }
    variance = count > 0 ? variance / count : 0;
    double stdDeviation = Math.sqrt(variance);

    return new ReportStatistics(total, average, min, max, count, stdDeviation);
}
```

**Query 7**: Optimize with streams

**Action**:
1. Select `calculateStatistics` method
2. Open Chat
3. Type: "Optimize this using Java streams and DoubleSummaryStatistics"

**Expected Result**:

```java
private ReportStatistics calculateStatistics(List<Map<String, Object>> data) {
    DoubleSummaryStatistics stats = data.stream()
        .mapToDouble(record -> (double) record.get("amount"))
        .summaryStatistics();

    double average = stats.getAverage();
    double variance = data.stream()
        .mapToDouble(record -> (double) record.get("amount"))
        .map(amount -> Math.pow(amount - average, 2))
        .average()
        .orElse(0);
    double stdDeviation = Math.sqrt(variance);

    return new ReportStatistics(
        stats.getSum(),
        stats.getAverage(),
        stats.getMin(),
        stats.getMax(),
        (int) stats.getCount(),
        stdDeviation
    );
}
```

---

### Step 5: Extract Report Formatting

**Query 8**: Extract report formatting

**Action**:
1. Select the formatting code
2. Open Inline Chat
3. Type: "Extract report formatting into a method returning String"

**Expected Result**:

```java
private String formatReport(String reportType, LocalDate startDate, LocalDate endDate,
                           ReportStatistics stats, List<Map<String, Object>> data) {
    StringBuilder report = new StringBuilder();
    report.append("========================================\n");
    report.append("         ").append(reportType).append(" REPORT\n");
    report.append("========================================\n");
    report.append("Period: ").append(startDate).append(" to ").append(endDate).append("\n");
    report.append("----------------------------------------\n");
    report.append("Total Records: ").append(stats.getCount()).append("\n");
    report.append("Total Amount: $").append(String.format("%.2f", stats.getTotal())).append("\n");
    report.append("Average Amount: $").append(String.format("%.2f", stats.getAverage())).append("\n");
    report.append("Min Amount: $").append(String.format("%.2f", stats.getMin())).append("\n");
    report.append("Max Amount: $").append(String.format("%.2f", stats.getMax())).append("\n");
    report.append("Standard Deviation: $").append(String.format("%.2f", stats.getStdDeviation())).append("\n");
    report.append("----------------------------------------\n");
    report.append("\nDetailed Records:\n");

    for (Map<String, Object> record : data) {
        report.append(String.format("Date: %s, Amount: $%.2f, Quantity: %d\n",
            record.get("date"),
            (double)record.get("amount"),
            (int)record.get("quantity")));
    }
    report.append("========================================\n");

    return report.toString();
}
```

**Query 9**: Further decompose formatting

**Action**: Open Chat and type:

```
Split formatReport into three methods:
1. formatHeader - formats the header section
2. formatStatistics - formats the statistics section  
3. formatDetailedRecords - formats the detailed records
```

**Expected Result**:

```java
private String formatHeader(String reportType, LocalDate startDate, LocalDate endDate) {
    return "========================================\n" +
           "         " + reportType + " REPORT\n" +
           "========================================\n" +
           "Period: " + startDate + " to " + endDate + "\n" +
           "----------------------------------------\n";
}

private String formatStatistics(ReportStatistics stats) {
    return "Total Records: " + stats.getCount() + "\n" +
           "Total Amount: $" + String.format("%.2f", stats.getTotal()) + "\n" +
           "Average Amount: $" + String.format("%.2f", stats.getAverage()) + "\n" +
           "Min Amount: $" + String.format("%.2f", stats.getMin()) + "\n" +
           "Max Amount: $" + String.format("%.2f", stats.getMax()) + "\n" +
           "Standard Deviation: $" + String.format("%.2f", stats.getStdDeviation()) + "\n" +
           "----------------------------------------\n";
}

private String formatDetailedRecords(List<Map<String, Object>> data) {
    StringBuilder records = new StringBuilder("\nDetailed Records:\n");
    for (Map<String, Object> record : data) {
        records.append(String.format("Date: %s, Amount: $%.2f, Quantity: %d\n",
            record.get("date"),
            (double)record.get("amount"),
            (int)record.get("quantity")));
    }
    records.append("========================================\n");
    return records.toString();
}

private String formatReport(String reportType, LocalDate startDate, LocalDate endDate,
                           ReportStatistics stats, List<Map<String, Object>> data) {
    return formatHeader(reportType, startDate, endDate) +
           formatStatistics(stats) +
           formatDetailedRecords(data);
}
```

---

### Step 6: Extract File Operations

**Query 10**: Extract file saving

**Action**:
1. Select file writing code
2. Open Inline Chat
3. Type: "Extract file saving into a method"

**Expected Result**:

```java
private void saveReportToFile(String reportType, LocalDate startDate, LocalDate endDate, 
                             String content) {
    String fileName = reportType + "_" + startDate + "_to_" + endDate + ".txt";
    try (BufferedWriter writer = new BufferedWriter(new FileWriter(fileName))) {
        writer.write(content);
        System.out.println("Report saved to: " + fileName);
    } catch (IOException e) {
        System.err.println("Error writing report: " + e.getMessage());
        throw new RuntimeException("Failed to write report", e);
    }
}
```

---

### Step 7: Extract Email Notification

**Query 11**: Extract email notification

**Action**:
1. Select email code
2. Open Inline Chat
3. Type: "Extract email notification into a method"

**Expected Result**:

```java
private void sendEmailNotification(String reportType, String content) {
    String emailBody = "Report generated successfully.\n\n" + content;
    try {
        System.out.println("Sending email notification...");
        System.out.println("To: admin@example.com");
        System.out.println("Subject: " + reportType + " Report Generated");
        System.out.println("Email sent successfully!");
    } catch (Exception e) {
        System.err.println("Warning: Failed to send email notification");
    }
}
```

---

## Part 3: Final Refactored Code

### Complete Refactored ReportGenerator.java (AFTER)

```java
package com.example.reporting;

import java.io.*;
import java.time.*;
import java.util.*;
import java.util.stream.*;

public class ReportGenerator {

    public void generateReport(String reportType, LocalDate startDate, LocalDate endDate) {
        validateInputs(reportType, startDate, endDate);

        List<Map<String, Object>> rawData = retrieveRawData(reportType, startDate, endDate);
        List<Map<String, Object>> cleanedData = cleanData(rawData);
        ReportStatistics stats = calculateStatistics(cleanedData);

        String reportContent = formatReport(reportType, startDate, endDate, stats, cleanedData);

        saveReportToFile(reportType, startDate, endDate, reportContent);
        sendEmailNotification(reportType, reportContent);
    }

    private void validateInputs(String reportType, LocalDate startDate, LocalDate endDate) {
        if (reportType == null || reportType.isEmpty()) {
            throw new IllegalArgumentException("Report type cannot be null or empty");
        }
        if (startDate == null || endDate == null) {
            throw new IllegalArgumentException("Dates cannot be null");
        }
        if (startDate.isAfter(endDate)) {
            throw new IllegalArgumentException("Start date must be before end date");
        }
        if (!Arrays.asList("SALES", "INVENTORY", "FINANCIAL").contains(reportType)) {
            throw new IllegalArgumentException("Invalid report type: " + reportType);
        }
    }

    private List<Map<String, Object>> retrieveRawData(String reportType, LocalDate startDate, 
                                                      LocalDate endDate) {
        List<Map<String, Object>> rawData = new ArrayList<>();
        try {
            for (LocalDate date = startDate; !date.isAfter(endDate); date = date.plusDays(1)) {
                Map<String, Object> record = new HashMap<>();
                record.put("date", date);
                record.put("amount", Math.random() * 10000);
                record.put("quantity", (int)(Math.random() * 100));
                record.put("category", reportType);
                rawData.add(record);
            }
        } catch (Exception e) {
            System.err.println("Error retrieving data: " + e.getMessage());
            throw new RuntimeException("Failed to retrieve data", e);
        }
        return rawData;
    }

    private List<Map<String, Object>> cleanData(List<Map<String, Object>> rawData) {
        return rawData.stream()
            .filter(record -> record.get("amount") != null && (double)record.get("amount") > 0)
            .filter(record -> record.get("quantity") != null && (int)record.get("quantity") > 0)
            .collect(Collectors.toList());
    }

    private ReportStatistics calculateStatistics(List<Map<String, Object>> data) {
        DoubleSummaryStatistics stats = data.stream()
            .mapToDouble(record -> (double) record.get("amount"))
            .summaryStatistics();

        double average = stats.getAverage();
        double variance = data.stream()
            .mapToDouble(record -> (double) record.get("amount"))
            .map(amount -> Math.pow(amount - average, 2))
            .average()
            .orElse(0);
        double stdDeviation = Math.sqrt(variance);

        return new ReportStatistics(
            stats.getSum(),
            stats.getAverage(),
            stats.getMin(),
            stats.getMax(),
            (int) stats.getCount(),
            stdDeviation
        );
    }

    private String formatReport(String reportType, LocalDate startDate, LocalDate endDate,
                               ReportStatistics stats, List<Map<String, Object>> data) {
        return formatHeader(reportType, startDate, endDate) +
               formatStatistics(stats) +
               formatDetailedRecords(data);
    }

    private String formatHeader(String reportType, LocalDate startDate, LocalDate endDate) {
        return "========================================\n" +
               "         " + reportType + " REPORT\n" +
               "========================================\n" +
               "Period: " + startDate + " to " + endDate + "\n" +
               "----------------------------------------\n";
    }

    private String formatStatistics(ReportStatistics stats) {
        return "Total Records: " + stats.getCount() + "\n" +
               "Total Amount: $" + String.format("%.2f", stats.getTotal()) + "\n" +
               "Average Amount: $" + String.format("%.2f", stats.getAverage()) + "\n" +
               "Min Amount: $" + String.format("%.2f", stats.getMin()) + "\n" +
               "Max Amount: $" + String.format("%.2f", stats.getMax()) + "\n" +
               "Standard Deviation: $" + String.format("%.2f", stats.getStdDeviation()) + "\n" +
               "----------------------------------------\n";
    }

    private String formatDetailedRecords(List<Map<String, Object>> data) {
        StringBuilder records = new StringBuilder("\nDetailed Records:\n");
        for (Map<String, Object> record : data) {
            records.append(String.format("Date: %s, Amount: $%.2f, Quantity: %d\n",
                record.get("date"),
                (double)record.get("amount"),
                (int)record.get("quantity")));
        }
        records.append("========================================\n");
        return records.toString();
    }

    private void saveReportToFile(String reportType, LocalDate startDate, LocalDate endDate, 
                                 String content) {
        String fileName = reportType + "_" + startDate + "_to_" + endDate + ".txt";
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(fileName))) {
            writer.write(content);
            System.out.println("Report saved to: " + fileName);
        } catch (IOException e) {
            System.err.println("Error writing report: " + e.getMessage());
            throw new RuntimeException("Failed to write report", e);
        }
    }

    private void sendEmailNotification(String reportType, String content) {
        String emailBody = "Report generated successfully.\n\n" + content;
        try {
            System.out.println("Sending email notification...");
            System.out.println("To: admin@example.com");
            System.out.println("Subject: " + reportType + " Report Generated");
            System.out.println("Email sent successfully!");
        } catch (Exception e) {
            System.err.println("Warning: Failed to send email notification");
        }
    }
}
```

---

## Part 4: Generate Unit Tests

### Query 12: Generate comprehensive tests

**Action**: Open Chat and type:

```
/tests Generate JUnit 5 tests for the refactored ReportGenerator class. Create separate test methods for:
- validateInputs with valid and invalid cases
- cleanData with various data scenarios
- calculateStatistics with different datasets
- formatHeader, formatStatistics, formatDetailedRecords
```

**Expected Result**:

Create `src/test/java/com/example/reporting/ReportGeneratorTest.java`:

```java
package com.example.reporting;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import java.time.LocalDate;
import java.util.*;
import static org.junit.jupiter.api.Assertions.*;

class ReportGeneratorTest {

    private ReportGenerator generator;

    @BeforeEach
    void setUp() {
        generator = new ReportGenerator();
    }

    @Test
    @DisplayName("Should generate report successfully with valid inputs")
    void testGenerateReportSuccess() {
        assertDoesNotThrow(() -> {
            generator.generateReport("SALES", 
                LocalDate.of(2024, 1, 1), 
                LocalDate.of(2024, 1, 5));
        });
    }

    @Test
    @DisplayName("Should throw exception for null report type")
    void testValidateInputsNullReportType() {
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            generator.generateReport(null, 
                LocalDate.of(2024, 1, 1), 
                LocalDate.of(2024, 1, 5));
        });
        assertTrue(exception.getMessage().contains("cannot be null"));
    }

    @Test
    @DisplayName("Should throw exception for empty report type")
    void testValidateInputsEmptyReportType() {
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            generator.generateReport("", 
                LocalDate.of(2024, 1, 1), 
                LocalDate.of(2024, 1, 5));
        });
        assertTrue(exception.getMessage().contains("cannot be null or empty"));
    }

    @Test
    @DisplayName("Should throw exception for null start date")
    void testValidateInputsNullStartDate() {
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            generator.generateReport("SALES", null, LocalDate.of(2024, 1, 5));
        });
        assertTrue(exception.getMessage().contains("cannot be null"));
    }

    @Test
    @DisplayName("Should throw exception when start date is after end date")
    void testValidateInputsInvalidDateRange() {
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            generator.generateReport("SALES", 
                LocalDate.of(2024, 1, 10), 
                LocalDate.of(2024, 1, 5));
        });
        assertTrue(exception.getMessage().contains("must be before"));
    }

    @Test
    @DisplayName("Should throw exception for invalid report type")
    void testValidateInputsInvalidReportType() {
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            generator.generateReport("INVALID", 
                LocalDate.of(2024, 1, 1), 
                LocalDate.of(2024, 1, 5));
        });
        assertTrue(exception.getMessage().contains("Invalid report type"));
    }

    @Test
    @DisplayName("Should accept valid SALES report type")
    void testValidateInputsValidSalesType() {
        assertDoesNotThrow(() -> {
            generator.generateReport("SALES", 
                LocalDate.of(2024, 1, 1), 
                LocalDate.of(2024, 1, 2));
        });
    }

    @Test
    @DisplayName("Should accept valid INVENTORY report type")
    void testValidateInputsValidInventoryType() {
        assertDoesNotThrow(() -> {
            generator.generateReport("INVENTORY", 
                LocalDate.of(2024, 1, 1), 
                LocalDate.of(2024, 1, 2));
        });
    }

    @Test
    @DisplayName("Should accept valid FINANCIAL report type")
    void testValidateInputsValidFinancialType() {
        assertDoesNotThrow(() -> {
            generator.generateReport("FINANCIAL", 
                LocalDate.of(2024, 1, 1), 
                LocalDate.of(2024, 1, 2));
        });
    }
}
```

---

## Part 5: Testing and Comparison

### Create Main class to demonstrate

Create `src/main/java/com/example/Main.java`:

```java
package com.example;

import com.example.reporting.ReportGenerator;
import java.time.LocalDate;

public class Main {
    public static void main(String[] args) {
        ReportGenerator generator = new ReportGenerator();

        System.out.println("Generating SALES report...");
        generator.generateReport("SALES", 
            LocalDate.of(2024, 1, 1), 
            LocalDate.of(2024, 1, 7));

        System.out.println("\nGenerating INVENTORY report...");
        generator.generateReport("INVENTORY", 
            LocalDate.of(2024, 2, 1), 
            LocalDate.of(2024, 2, 5));
    }
}
```

### Run the application

```bash
mvn clean compile exec:java -Dexec.mainClass="com.example.Main"
```

### Run tests

```bash
mvn test
```

**Expected output**:

```
[INFO] Tests run: 9, Failures: 0, Errors: 0, Skipped: 0
[INFO] BUILD SUCCESS
```

---

## Comparison: Before vs After

### Metrics

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| Main method lines | 150 | 10 | 93% reduction |
| Total methods | 1 | 11 | Better organization |
| Average method length | 150 | 13 | 91% reduction |
| Testability | Poor | Excellent | Each method testable |
| Readability | Low | High | Clear responsibilities |
| Maintainability | Difficult | Easy | Isolated changes |
| Reusability | None | High | Methods reusable |
| Cyclomatic Complexity | 15 | 2-3 per method | Much simpler |

### Benefits Achieved

1. **Single Responsibility**: Each method has one clear purpose
2. **Easy Testing**: Can test validation, calculations, formatting separately
3. **Better Readability**: Main method reads like documentation
4. **Easier Maintenance**: Changes isolated to specific methods
5. **Code Reuse**: Methods can be reused in other contexts
6. **Reduced Complexity**: Each method is simple to understand

---

## Summary of All Queries Used

| Query | Purpose | Method | Result |
|-------|---------|--------|--------|
| Query 1 | Extract validation | Inline Chat | validateInputs() |
| Query 2 | Extract data retrieval | Inline Chat | retrieveRawData() |
| Query 3 | Extract data cleaning | Inline Chat | cleanData() |
| Query 4 | Optimize with streams | Chat | Stream-based cleanData() |
| Query 5 | Create statistics class | Chat | ReportStatistics class |
| Query 6 | Extract calculations | Inline Chat | calculateStatistics() |
| Query 7 | Optimize calculations | Chat | Stream-based statistics |
| Query 8 | Extract formatting | Inline Chat | formatReport() |
| Query 9 | Split formatting | Chat | formatHeader(), formatStatistics(), formatDetailedRecords() |
| Query 10 | Extract file operations | Inline Chat | saveReportToFile() |
| Query 11 | Extract email | Inline Chat | sendEmailNotification() |
| Query 12 | Generate tests | Chat /tests | Comprehensive test suite |

---

## Additional Challenges (Optional)

### Challenge 1: Extract to separate classes

**Prompt**: "Refactor further by creating separate classes for DataRetriever, DataCleaner, StatisticsCalculator, ReportFormatter, FileWriter, and EmailSender"

### Challenge 2: Add dependency injection

**Prompt**: "Refactor to use constructor injection for better testability and to follow dependency inversion principle"

### Challenge 3: Add interfaces

**Prompt**: "Create interfaces for each service (IDataRetriever, IReportFormatter, etc.) to allow multiple implementations"

### Challenge 4: Add configuration

**Prompt**: "Extract hardcoded values (email address, file paths) into a configuration class"

---

## Key Takeaways

In this lab, you learned to:
- Identify code smells in long methods
- Use GitHub Copilot to extract cohesive methods
- Apply Single Responsibility Principle
- Improve code with Java streams
- Create value objects for data transfer
- Generate comprehensive unit tests
- Measure refactoring improvements

---

## Refactoring Best Practices

1. **Extract Method**: When a method does more than one thing
2. **Introduce Parameter Object**: When methods have many parameters
3. **Replace Temp with Query**: When temporary variables can be method calls
4. **Decompose Conditional**: When conditionals are complex
5. **Extract Class**: When a class has too many responsibilities
6. **Use Streams**: For more readable collection operations
7. **Test After Each Refactoring**: Ensure functionality unchanged

---

## Additional Resources

- [Refactoring by Martin Fowler](https://refactoring.com/)
- [Clean Code by Robert Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
- [SOLID Principles](https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)
- [Java Streams Tutorial](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)
- [GitHub Copilot Refactoring Guide](https://docs.github.com/en/copilot/using-github-copilot/example-use-cases/refactoring-code-with-github-copilot)

---

## Project Cleanup

```bash
cd ..
rm -rf refactor-demo
```
