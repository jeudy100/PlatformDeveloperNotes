# Apex & .NET Basics

## Notes on ".NET Concepts Mapped to the Lightning Platform"

### Meet the Lightning Platform

- Metadata-driven architecture.
- Tight integration with the database.
- Built-in features like user interface, security, and reporting.
- Developer productivity at the cost of some control.

### Apex Basics

- Declarative development is emphasized.
- Apex is similar to java in terms of object-oriented design.
- Basic syntax and example of an Apex class provided.
- Supported data types include primitives, sObjects, and enums.
- Collections like List, Set, and Map are available.

### Similarity to VB.NET

- Apex programming language shares similarities with VB.NET.
- It operates directly on the Lightning Platform.
- Like VB.NET, Apex is object-oriented.
- Supports principles such as encapsulation, abstraction, inheritance, and polymorphism.

### Object-oriented Design

- Apex supports object-oriented design principles.
- Includes constructs like classes, interfaces, properties, and collections.
- Basic class syntax provided.

### Example Class: HelloWorld

- Illustrative example of an Apex class named HelloWorld.
- The `printMessage` method outputs "Hello World" to the system debug log.

#### Basic syntax

```java
public with sharing class HelloWorld {
    public void printMessage() {
        String msg = 'Hello World';
        System.debug(msg);
    }
}

```

```java
private | public | global
[virtual | abstract | with sharing | without sharing]
class ClassName [implements InterfaceNameList] [extends ClassName]
{
    // The body of the class
}
```

### Data Types

- Apex supports various data types:
  - Primitive types: Integer, Double, Long, Date, Datetime, String, Boolean.
  - ID data type for Lightning Platform record identifiers.
  - sObjects: Standard or custom Salesforce objects.
  - Enumerations.
  - Typed lists of values.

### Working with Collections

- Apex has three primary collection types: List, Set, Map.

#### List

- Ordered collection similar to arrays.
- Example list declaration provided.

```java
// Instantiate list
List<String> myStrings =  new List<String>();
String[] myStrings = new List<String>();
List<String> myStrings =  new List<String> {'String1', 'String2', 'String3' };

// Add to list
List<String> myStrings = new List<String>();
myStrings.add('String1');
myStrings.add('String2');
myStrings.add('String3');

// Database query
List<Account> myAccounts = [SELECT Id, Name FROM Account];
```

#### Set

- Unordered collection that does not contain duplicates.
- Often used for storing unique ID values.
- Example set declaration and usage provided.

```java
Set<ID> accountIds = new Set<ID>{'001d000000BOaHSAA1','001d000000BOaHTAA1'};

// use sets to query for objects by the value of the set.
List<Account> accounts = [SELECT Name FROM Account WHERE Id IN :accountIds]
```

#### Map

- Collection of key-value pairs.
- Keys must be unique.
- Example map declaration and usage provided.

```java
Map<Id, Account> accountMap = new Map<Id, Account>([SELECT Id, Name FROM Account]);
Id accId = '001d000000BOaHSAA1';
Account account = accountMap.get(accId);
```

### ASP.NET to Visualforce

- Visualforce resembles ASP.NET with a clear separation of markup and code.
- Similarities in form fields mapping to controller properties.
- Viewstate is a challenge in both ASP.NET and Visualforce.

```xml
<apex:page standardController="Contact">
  <apex:form>
    <apex:pageBlock title="Edit Contact" mode="Edit">
       <apex:pageBlockButtons >
         <apex:commandButton action="{!edit}" id="editButton" value="Edit"/>
         <apex:commandButton action="{!save}" id="saveButton" value="Save"/>
         <apex:commandButton action="{!cancel}" id="cancelButton" value="Cancel"/>
       </apex:pageBlockButtons>
       <apex:pageBlockSection >
          <apex:inputField value="{!contact.lastname}" />
          <apex:inputField value="{!contact.accountId}"/>
          <apex:inputField value="{!contact.phone}"/>
       </apex:pageBlockSection>
    </apex:pageBlock>
  </apex:form>
</apex:page>
```

### What Is Different?

- Apex is not case-sensitive unlike java.
- Tight coupling between Apex code and the database.
- Different design patterns compared to .NET.
- 75% test coverage required for deployment in production.
- No solution, project, or config files.
- Smaller class library in Apex compared to .NET.

### Development Tools

- Developer Console for editing, debugging, and executing code.
- Salesforce Extensions for VS Code for local development.
- Salesforce CLI for command-line interface lovers.

### Handling Security

- Authentication and identity handled by the platform.
- Access control at multiple levels.
- Security configured declaratively by administrators.

### Integration

- SOAP and REST commonly used for integration.
- Creation and invocation of web services in Apex.
- SOAP and REST APIs provided by Salesforce.
- Numerous third-party integration options available on AppExchange.

### Creating an Apex Class

- Use Developer Console to create an Apex class named EmailManager.
- Implement a public method `sendMail` and a private helper method `inspectResults`.
- Use Anonymous Apex to invoke the `sendMail` method.

```java
public with sharing class EmailManager {
    // Public method
    public static void sendMail(String address, String subject, String body) {
        // Create an email message object
        Messaging.SingleEmailMessage mail = new Messaging.SingleEmailMessage();
        String[] toAddresses = new String[] {address};
        mail.setToAddresses(toAddresses);
        mail.setSubject(subject);
        mail.setPlainTextBody(body);
        // Pass this email message to the built-in sendEmail method
        // of the Messaging class
        Messaging.SendEmailResult[] results = Messaging.sendEmail(
            new Messaging.SingleEmailMessage[] { mail });
        // Call a helper method to inspect the returned results.
        inspectResults(results);
    }
    // Helper method
    private static Boolean inspectResults(Messaging.SendEmailResult[] results) {
        Boolean sendResult = true;
        // sendEmail returns an array of result objects.
        // Iterate through the list to inspect results.
        // In this class, the methods send only one email,
        // so we should have only one result.
        for (Messaging.SendEmailResult res : results) {
            if (res.isSuccess()) {
                System.debug('Email sent successfully');
            } else {
                sendResult = false;
                System.debug('The following errors occurred: ' + res.getErrors());
            }
        }
        return sendResult;
    }
}
```

### Conclusion

- Lightning Platform offers a unique approach to development compared to .NET.
- Developers need to adapt to differences in syntax, design patterns, and tooling.
- Despite differences, Lightning Platform provides robust features for rapid application development.

## Notes on "Understand Execution Context"

### What Is Execution Context?

In the context of the Lightning Platform, code execution occurs within an execution context, representing the time between code execution and its completion. It's crucial to note that the Apex code being written isn't the only code executing.

### Methods of Invoking Apex

- **Database Trigger**: Triggered for specific events on objects.
- **Anonymous Apex**: Code snippets executed dynamically.
- **Asynchronous Apex**: Includes futures, queueable Apex, batch jobs, and scheduled Apex.
- **Web Services**: Code exposed via SOAP or REST.
- **Email Services**: Code for processing inbound email.
- **Visualforce or Lightning Pages**: Apex can be executed automatically or on user actions.

### Additional Triggers for Actions

Actions like creating tasks, sending emails, updating fields, or sending outbound messages can be triggered by platform features, all within an execution context.

### User Context Consideration

Apex typically executes in system context with full object and field access. However, the context of the executing user is significant. Using `with sharing` or `without sharing` keywords can enforce sharing rules for the current user.

For further details, refer to the topic on Using the `with sharing` or `without sharing` keywords (https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_classes_keywords_sharing.htm).

## Notes on "Introduction to Database Triggers in Salesforce"

### What are Database Triggers?

Similar to SQL Server triggers, Apex database triggers execute logic before or after events on records in Salesforce. These triggers can be defined for specific events such as before/after insert, update, delete, or undelete operations.

- before insert
- before update
- before delete
- after insert
- after update
- after delete
- after undelete

The basic syntax for a trigger in Apex is as follows:

```java
trigger TriggerName on ObjectName (trigger_events) {
   // code_block
}
```

**Trigger Context Variables**

Triggers in Salesforce provide implicit variables for accessing runtime context. These variables are encapsulated within the `System.Trigger` class.

- **isExecuting**: 
  - Returns true if the current context is a trigger.

- **isInsert**: 
  - Returns true if the trigger was fired due to an insert operation.

- **isUpdate**: 
  - Returns true if the trigger was fired due to an update operation.

- **isDelete**: 
  - Returns true if the trigger was fired due to a delete operation.

- **isBefore**: 
  - Returns true if the trigger was fired before any record was saved.

- **isAfter**: 
  - Returns true if the trigger was fired after all records were saved.

- **isUndelete**: 
  - Returns true if the trigger was fired after a record is recovered from the Recycle Bin.

- **new**: 
  - Returns a list of new versions of sObject records.
  - Available in insert, update, and undelete triggers.
  - Records can only be modified in before triggers.

- **newMap**: 
  - Returns a map of IDs to new versions of sObject records.
  - Available in before update, after insert, after update, and after undelete triggers.

- **old**: 
  - Returns a list of old versions of sObject records.
  - Available in update and delete triggers.

- **oldMap**: 
  - Returns a map of IDs to old versions of sObject records.
  - Available in update and delete triggers.

- **operationType**: 
  - Returns an enum of type System.TriggerOperation corresponding to the current operation.
  - Possible values: BEFORE_INSERT, BEFORE_UPDATE, BEFORE_DELETE, AFTER_INSERT, AFTER_UPDATE, AFTER_DELETE, and AFTER_UNDELETE.

- **size**: 
  - Represents the total number of records in a trigger invocation, both old and new.

**Note**: 
- The record firing a trigger may include invalid field values, such as division by zero in a formula.
- In such cases, the field value is set to null in the `new`, `newMap`, `old`, and `oldMap` variables.

### Best Practices

Using triggers should be a deliberate choice, and developers should strive to minimize technical overhead by leveraging the platform's declarative capabilities wherever possible.

- Usage Consideration: Triggers should only be used when absolutely necessary, as many tasks can be accomplished using point-and-click automation tools.
- Automation Tools: Salesforce provides powerful tools like Flow Builder for managing business logic without code, which are often more efficient than triggers.
- New Developers: New developers are encouraged to explore the Approve Records with Approval Processes module in the Developer Beginner trail before resorting to triggers to avoid unnecessary technical complexity.

### Trigger Creation

To better understand execution context, let’s walk through creating an Apex database trigger that creates an opportunity when a new account is entered. This trigger calls a method from a handler class, so we first need to create that.

1. Open Developer Console: From Setup, select Your Name > Developer Console.
2. Create Apex Class:
   - Select File > New > Apex Class.
   - Enter AccountHandler for the class name and click OK.
   - Delete existing code and insert the provided snippet.
   - Save the class.

```java
public with sharing class AccountHandler {
    public static void CreateNewOpportunity(List<Account> accts) {
        for (Account a : accts) {
            Opportunity opp = new Opportunity();
            opp.Name = a.Name + ' Opportunity';
            opp.AccountId = a.Id;
            opp.StageName = 'Prospecting';
            opp.CloseDate = System.Today().addMonths(1);
            insert opp;
        }
    }
}
```

**Note:** It’s considered a best practice to use only one trigger per object.

3. Create Apex Trigger:
   - Select File > New > Apex Trigger.
   - Enter AccountTrigger as the name, and select Account as the sObject.
   - Delete existing code and insert the provided snippet.
   - Save the trigger.

```java
trigger AccountTrigger on Account (before insert, before update, before
    delete, after insert, after update, after delete,  after undelete) {
    if (Trigger.isAfter && Trigger.isInsert) {
        AccountHandler.CreateNewOpportunity(Trigger.New);
    }
}
```

4. Execute Anonymous Code:
   - Open Execute Anonymous Window from Debug menu.
   - Delete existing code and insert the provided snippet.
   - Execute the code.

```java
Account acct = new Account(
    Name='Test Account 2',
    Phone='(415)555-8989',
    NumberOfEmployees=50,
    BillingCity='San Francisco');
insert acct;
```

### Examining the Execution Log

When examining the execution log in the Developer Console, it's important to note the sequence of events and the execution context.

1. **Execution Start**: The log begins with the EXECUTION_STARTED event, and it ends with the EXECUTION_FINISHED event. Everything between these events constitutes the execution context.

2. **CODE_UNIT_STARTED Event**: This event marks the start of code execution, such as when code from the Execute Anonymous window is initiated. It includes events like BeforeInsert and AfterInsert.

3. **Governor Limits**: Salesforce operates in a multi-tenanted environment, meaning each instance of a Salesforce org must adhere to governor limits to prevent resource overconsumption. Understanding these limits is crucial to prevent system crashes and maintain stability.

4. **Importance**: Recognizing the execution context and governor limits is essential for developers to ensure efficient code execution and prevent system instability.

Understanding the execution log and governor limits helps developers optimize code and maintain the stability of Salesforce instances.

### Working with Limits

When developing on the Salesforce platform, it's crucial to understand and work within the various limits imposed by the system. Two of the most commonly encountered limits are related to the number of SOQL queries and DML statements.

1. **SOQL Queries Limit**: Developers often encounter issues with the limit on the number of SOQL queries they can execute. This limit is important to prevent excessive querying and to maintain system performance.

2. **DML Statements Limit**: Similarly, the limit on the number of Data Manipulation Language (DML) statements, such as insert, update, delete, or undelete operations, is vital. Exceeding this limit can lead to unexpected behavior and performance issues.

**Note:** Salesforce imposes various limits, and they may change with each major release. It's essential for developers to stay updated on these limits by referring to the latest documentation on Execution Governors and Limits.

Understanding and effectively managing these limits is critical for developing efficient and stable applications on the Salesforce platform.

### Working in Bulk

- Designing code to handle single records only is a common mistake on the Lightning Platform.
- Apex triggers can receive up to 200 records at once, with limits on SOQL queries and DML statements.
- Performing SOQL queries or DML statements inside a loop during bulk operations can lead to limits errors.
- Code should be designed to handle bulk operations from the start to avoid issues.
- Bulkifying code after deployment can be time-consuming.
- Optimize code by collecting records in a list within the loop and performing a single insert operation outside the loop.
- Unit tests should be written to ensure code works as expected, simulating bulk operations.
- Adhering to bulk patterns and writing comprehensive unit tests can prevent limits errors and ensure efficient code execution.

```java
public with sharing class AccountHandler {
    public static void CreateNewOpportunity(List<Account> accts) {
        List<Opportunity> opps = new List<Opportunity>();
        for (Account a : accts) {
            Opportunity opp = new Opportunity();
            opp.Name = a.Name + ' Opportunity';
            opp.AccountId = a.Id;
            opp.StageName = 'Prospecting';
            opp.CloseDate = System.Today().addMonths(1);
            opps.add(opp);
        }
        if (opps.size() > 0) {
            insert opps;
        }
    }
}
```

```java
@isTest
private class AccountTrigger_Test {
    @isTest static void TestCreateNewAccountInBulk() {
        // Test Setup data
        // Create 200 new Accounts
        List<Account> accts = new List<Account>();
        for(Integer i=0; i < 200; i++) {
            Account acct = new Account(Name='Test Account ' + i);
            accts.add(acct);
        }
        // Perform Test
        Test.startTest();
        insert accts;
        Test.stopTest();
        // Verify that 200 new Accounts were inserted
        List<Account> verifyAccts = [SELECT Id FROM Account];
        System.assertEquals(200, verifyAccts.size());
        // Also verify that 200 new Opportunities were inserted
        List<Opportunity> verifyOpps = [SELECT Id FROM Opportunity];
        System.assertEquals(200, verifyOpps.size());
    }
}
```

### More Insights:

- **Exception Handling in Apex**: Apex employs try-catch-finally blocks for exception handling. However, the catch statement and rollback strategy might differ depending on the execution context. For best practices, refer to the provided resource link.

- **Data Persistence in Lightning Platform**: Unlike traditional application or session variables, there are no such entities in the Lightning Platform. Instead, static variables are available for persisting data between classes. However, static variables function differently from those in .NET. They can only retain information within a single execution context. Explore Advanced Apex resources for alternative methods of maintaining data across trigger invocations.

- **Considerations with Limits**: When dealing with limits, developers, especially those creating managed packages, must weigh various tradeoffs. Managed packages are often used by Salesforce partners to distribute and sell applications. This module provides a basic overview, but for a deeper understanding of Apex development, delve into Advanced Apex resources.

## Notes on "Use Asynchronous Apex"

### When to Go Asynchronous

Three reasons are usually behind choosing asynchronous programming:

1. **Processing a very large number of records:** This reason is unique to the multi-tenanted world of the Lightning Platform where limits rule. The limits associated with asynchronous processes are higher than those with synchronous processes. Therefore, if you need to process thousands or even millions of records, asynchronous processing is your best bet.

2. **Making callouts to external web services:** Callouts can take a long time to process, but in the Lightning Platform, triggers can’t make callouts directly.

3. **Creating a better and faster user experience by offloading some processing to asynchronous calls:** Why do everything at once? If it can wait, let it.

### Future Methods

```java
public class MyFutureClass {
    // Include callout=true when making callouts
    @future(callout=true)
    static void myFutureMethod(Set<Id> ids) {
        // Get the list of contacts in the future method since
        // you cannot pass objects as arguments to future methods
        List<Contact> contacts = [SELECT Id, LastName, FirstName, Email
            FROM Contact WHERE Id IN :ids];
        // Loop through the results and call a method
        // which contains the code to do the actual callout
        for (Contact con: contacts) {
            String response = anotherClass.calloutMethod(con.Id,
                con.FirstName,
                con.LastName,
                con.Email);
            // May want to add some code here to log
            // the response to a custom object
        }
    }
}
```

#### Future Limitations

- You can’t track execution because no Apex job ID is returned.
- Parameters must be primitive data types, arrays of primitive data types, or collections of primitive data types. Future methods can’t take objects as arguments.
- You can’t chain future methods and have one call another.

### Batch or Scheduled Apex

- The No. 1 reason to use it is if you need to process a large number of records.
- To use it, your class implements the Database.Batchable interface.
  - You also define start(), execute(), and finish() methods.

```java
global class MyBatchableClass implements Database.Batchable<sObject>, Database.Stateful {
    // Used to record the total number of Accounts processed
    global Integer numOfRecs = 0;

    // Used to gather the records that will be passed to the interface method
    // This method will only be called once and will return either a
    // Database.QueryLocator object or an Iterable that contains the records
    // or objects passed to the job.
    global Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator('SELECT Id, Name FROM Account');
    }

    // This is where the actual processing occurs as data is chunked into
    // batches and the default batch size is 200.
    global void execute(Database.BatchableContext bc, List<Account> scope) {
        for (Account acc : scope) {
            // Do some processing here
            // and then increment the counter variable
            numOfRecs = numOfRecs + 1;
        }
    }

    // Used to execute any post-processing that may need to happen. This
    // is called only once and after all the batches have finished.
    global void finish(Database.BatchableContext bc) {
        EmailManager.sendMail('someAddress@somewhere.com',
                              numOfRecs + ' Accounts were processed!',
                              'Meet me at the bar for drinks to celebrate');
    }
}
```

**Note**: Scheduled Apex and batchable interface are similar.

### Batchable Limitations

- Troubleshooting can be troublesome.
- Jobs are queued and subject to server availability, which can sometimes take longer than anticipated.
- Have we talked about limits yet?

### Queueable Apex

In response to limitations faced by developers with future methods and the batchable interface, Salesforce introduced Queueable Apex in Winter ’15. This new feature combines the advantages of future methods and batchable interfaces, offering a more efficient asynchronous processing tool.

Queueable Apex addresses the shortcomings of future methods by providing the following benefits:

- **Acceptance of Non-primitive Types:** Queueable Apex classes can accept parameter variables of non-primitive data types, such as sObjects or custom Apex types.
- **Job Monitoring:** When submitting a job, Queueable Apex returns a jobId that developers can use to identify the job and monitor its progress.
- **Support for Job Chaining:** Developers can chain one job to another by starting a second job from a running job. This feature is useful for sequential processing.

Compared to Batch Apex, Queueable Apex offers the following advantages:

- **Ease of Implementation:** Queueable Apex is easier to implement compared to Batch Apex.
- **Flexibility:** Queueable Apex does not impose the same limitations as Batch Apex, providing developers with more flexibility in their asynchronous processing tasks.

Developers who previously struggled with the slower batchable interface due to future method constraints now have a more sensible solution in Queueable Apex.

Overall, Queueable Apex represents a significant improvement in Salesforce's asynchronous processing capabilities, offering developers a more efficient and versatile tool for handling complex tasks.

```java
public class MyQueueableClass implements Queueable {
    private List<Contact> contacts;
    // Constructor for the class, where we pass
    // in the list of contacts that we want to process
    public MyQueueableClass(List<Contact> myContacts) {
        contacts = myContacts;
    }
    public void execute(QueueableContext context) {
        // Loop through the contacts passed in through
        // the constructor and call a method
        // which contains the code to do the actual callout
        for (Contact con: contacts) {
            String response = anotherClass.calloutMethod(con.Id,
                    con.FirstName,
                    con.LastName,
                    con.Email);
            // May still want to add some code here to log
            // the response to a custom object
        }
    }
}

// Invoke the queueable apex
List<Contact> contacts = [SELECT Id, LastName, FirstName, Email FROM Contact WHERE Is_Active__c = true];
Id jobId = System.enqueueJob(new MyQueueableClass(contacts));
```

## Notes for "Debug and Run Diagnostics"

### Debug Log

#### Logging Levels

- NONE
- ERROR
- WARN
- INFO
- DEBUG
- FINE
- FINER
- FINEST

#### Logging Limits

- Debug log must be 20MB or smaller
- Each org can retain up to 1000 MB of logs
  - Oldest logs are overwritten

#### Checkpoints

- Similar to breakpoints
- reveal a lot of detailed execution information about a line of code.
- They don’t stop execution on that line

# Formulas and Validations

## Notes on "Use forumla fields"

### Create a new Formula
- Go to the object manager and open up an object (Account, Contact, etc.)
- Create a new field or relationship and the select formula as the type
- The formula editor will appear when the type is formula
- Setup > Object Manager > sObject > Fields & Relationships > New > Formula

### Using the Formula Editor
1. The formula editor comes in two flavors: Simple and Advanced. It’s tempting to use the Simple editor, but we always recommend using the Advanced editor. Advanced doesn’t mean more complicated. It means more tools for you to create powerful formulas.
2. The Insert Field button opens a menu that allows you to select fields to use in your formula. Inserting from this menu automatically generates the correct syntax for accessing fields.
3. The Insert Operator button opens a dropdown list of the available mathematical and logical operators.
4. The Functions menu is where you view and insert formula functions.
5. The text area is where you enter your formula. When writing formulas, keep in mind that: Whitespace doesn’t matter. You can insert as many spaces and line breaks as you want without affecting the formula’s execution. Formulas are case sensitive. Pay attention to capitalization of field and object names. When working with numbers, the standard order of operations applies.
6. Once you’ve written a formula, you can use the Check Syntax button to ensure that everything is in working order before saving. If your formula has issues, the syntax checker alerts you to specific problems.
![formulaeditor](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/617b0fb6-dd13-4a12-8797-fa7b91fde0ae)
![FormulaEditorInsertField](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/38716d22-1bd2-4deb-9ef7-ead5ef6bd0a7)
![FormulaEditorInsertOperator](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/532c7bec-2cef-43c0-b9fb-fb0d8ca8076f)
![formulaeditorinsertfunctions](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/040a56ff-d257-4546-9579-ed8e28eb1e84)

### Debug Formulas
The **Check Syntax** button tells you:
- Missing parentheses
- Incorrect parameter type
- Incorrect number of parameters for function
- Formula result is incompatible with formula return type
- Field does not exist
- Unknown function

## Notes on "Implement Roll-Up Summary Fields
- While formula fields calculate values using fields within a single record, roll-up summary fields calculate values from a set of related records, such as those in a related list.
- You can perform different types of calculations with roll-up summary fields. You can count the number of detail records related to a master record, or calculate the sum, minimum value, or maximum value of a field in the detail records.
- Based on master-detail relationships

### Master-Detail Relationships
- closely link objects together so that the master record controls specific behaviors of the detail and subdetail record
- You define a roll-up summary field on the object that is on the master side of a master-detail relationship
- For example, you can create a roll-up summary field on the Account object, summarizing related opportunities:

![masterdetailrelationship](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/b43277b0-38ac-40ee-90ec-d990b5579db1)

- Types of summaries that can be used:
    - COUNT
        - Totals the number of related records 
    - SUM
        - Totals the values in the field you select in the Field to Aggregate option.
        - Only number, currency, and percent fields are available.
    - MIN
        - Displays the lowest value of the field you select in the Field to Aggregate option for all directly related records.
        - Only number, currency, percent, date, and date/time fields are available.
    - MAX
        - Displays the highest value of the field you select in the Field to Aggregate option for all directly related records.
        - Only number, currency, percent, date, and date/time fields are available.
### Creating the Summary Field
- Setup > ObjectManager > sObject > **Fields & Relationships** > New > Roll-Up Summary
- Examples:
    - Date Opportunity First Created
    - Total Price of All Products Related to an Opportunity
    - Minimum List Price of An Opportunity
## Notes on "Create Validation Rules"
- Rules that verify that data entered by users in records meets the standards you specify before they can save it.
- Can contain a formula or expression that evaluates the data in one or more fields and returns a value of “True” or “False.”
### Defining Validation Rules
- You can create validation rules for objects, fields, campaign members, or case milestones
- Setup > Object Manager > sObject > Validation Rules > New
- Examples:
    - Account number is numeric
    - Date Must Be in the Current Year
    - Number Range Validation
    - Website Extension
# Data Modeling
## Notes on "Optimize Customer Data with Standar and Custom Object"
- Standard objects
    - objects that are included with Salesforce.
    - Common business objects like Account, Contact, Lead, and Opportunity are all standard objects.
- Custom objects
    - objects that you create to store information that’s specific to your company or industry.
    - For DreamHouse, D’Angelo wants to build a custom Property object that stores information about the homes his company is selling.
### Create Custom Object
- Setup > Object Manager > Create | Custom Object

### Get to Know Fields
- Field Types:
    - Identity
        -  A 15-character, case-sensitive field that’s automatically generated for every record.
        -  You can find a record’s ID in its URL.
    - System
        - Read-only fields that provide information about a record from the system, like when the record was created or when it was last changed.
    - Name
        - All records need names so you can distinguish between them.
        - You can use text names or auto-numbered names that automatically increment every time you create a record.
    - Custom
        - Fields you create on standard or custom objects are called custom fields.
- Identity, system, and name fields are standard on every object in Salesforce.
- You can customize standard objects by adding custom fields.
  
### Customize Responsibly
- Be thoughtful about names
- Require fields when necessary
- Help out your users
    - Even with careful naming, your users might not always be clear about the purpose of a particular object or field.
    - Include descriptions for your custom objects and fields. For specialized or complicated customizations, use help text to give more details.

## Notes on "Create Object Relationships"
- There are two main types of object relationships:
    - lookup
        - links two objects together so that you can “look up” one object from the related items on another object.
        - one-to-one or one-to-many.
        - use lookup relationships when objects are only related in some cases.
    - master-detail
        - one object is the master and another is the detail.
        - The master object controls certain behaviors of the detail object, like who can view the detail’s data.
        - the detail object doesn’t work as a stand-alone.
    - hierarchical relationship
        - a special type of lookup relationship.
        - The main difference between the two is that hierarchical relationships are only available on the User object.

## Notes on "Work With Schema Builder"
- A tool that lets you visualize and edit your data model
- Setup > Objects and Fields > Schema Builder

### Create an Object with Schema Builder
1. In the left sidebar, click the Elements tab.
2. Click Object and drag it onto the canvas.
3. Enter information about your object. You can make it whatever you want!
4. Click Save.

### Create Fields with Schema Builder
1. From the Elements tab, choose a field type and drag it onto the object you just created.
2. Fill out the details about your new field.
3. Click Save.

# Data Management

## Notes on "Import Data"

You can effortlessly import external data into Salesforce, primarily in comma delimited text format (.csv). Salesforce provides two main methods for data import:

1. **Data Import Wizard**: This tool, accessible through the Setup menu, facilitates importing data into common standard objects like contacts, leads, accounts, and custom objects. It allows importing up to 50,000 records at a time. Users can easily configure parameters, specify data sources, and map field names between the import file and Salesforce fields via a simple interface.

2. **Data Loader**: This client application enables the import of up to five million records at once, irrespective of data type, from files or a database connection. It can be operated via a user interface or command line. For automation purposes, configurations including data sources, field mappings, and other parameters are set through configuration files. This enables automation of the import process using API calls.

**Note**: The number of records that can be imported depends on permissions, the type of data, and overall data storage limits for the organization. The ability to import specific objects is contingent upon the Salesforce edition.

### Use the Data Import Wizard when:

- You need to load less than 50,000 records.
- The objects you need to import are supported by the wizard.
- You don’t need the import process to be automated.

### Preparation for Data Import:

1. **Export Data**: Utilize existing software to create an export file, which will serve as the basis for the Salesforce import.

2. **Data Cleanup**: Ensure accuracy and consistency by cleaning up the import file. Tasks include removing duplicates, eliminating unnecessary information, correcting errors (e.g., spelling), and enforcing naming conventions.

3. **Field Mapping**: Compare data fields with Salesforce fields to ensure proper mapping. Fine-tune mapping if necessary, ensuring data is mapped to the appropriate Salesforce fields. Refer to the Field Mapping for Data Sources in the online help for details.

4. **Configuration Changes**: Make any necessary configuration changes in Salesforce to accommodate the imported data. This may involve creating new custom fields, adding values to picklists, or temporarily deactivating workflow rules.

**Note**: Salesforce recommends starting with a small test file to ensure proper preparation of source data before proceeding with the full import.

### Using the Data Import Wizard:

Follow these steps to import data using the Data Import Wizard:

1. **Start the Wizard**: 
   - From Setup, enter Data Import Wizard in the Quick Find box, then select Data Import Wizard.
   - Setup > Data Import Wizard
   - Review the information on the welcome page and click Launch Wizard!

2. **Choose Data to Import**:
   - Select Standard Objects for accounts, contacts, leads, etc., or Custom Objects for custom objects.
   - Specify whether to add new records, update existing records, or add and update records simultaneously.

3. **Specify Data File**:
   - Upload your CSV file or select it from the appropriate category.
   - Choose the character encoding method for your file.

4. **Map Data Fields**:
   - Map your data fields to Salesforce data fields. Salesforce attempts automatic mapping and allows manual mapping for unmapped fields.
   - Review and adjust mappings as needed.

5. **Review and Start Import**:
   - Review import information on the Review page.
   - If necessary, go back to specify mappings.
   - Click Start Import.

6. **Check Import Status**:
   - After starting the import, check the status by navigating to "Bulk Data Load Jobs" in Setup.
   - The user who initiates the import receives a status email upon completion.

**Note**: Adjust browser zoom settings if the Map button is not visible. Users receive a status email upon completion of the import process.

### Further Information:

Here are additional details to help integrate imported data into Salesforce:

1. **Picklist Values**:
   - Unrestricted Picklists: Import values from the file.
   - Restricted Picklists: Default value from the picklist is used.
   
2. **Multi-Select Picklists**:
   - Separate values by a semicolon in the import file.
   
3. **Checkboxes**:
   - Use 1 for checked values and 0 for unchecked values.
   
4. **Default Values**:
   - If a field is not mapped, its default value (if any) is automatically inserted.
   
5. **Date/Time Fields**:
   - Ensure imported formats match Salesforce's display format based on locale settings.
   
6. **Formula Fields**:
   - Formula fields are read-only and cannot accept imported data.
   
7. **Field Validation Rules**:
   - Salesforce validates records against rules before importing.
   - Deactivate relevant validation rules if they affect imported records.

## Notes on "Export Data"

Exporting data from Salesforce is easily achievable, either manually or on a scheduled basis. The data is exported in comma-separated values (CSV) format, providing flexibility for backup or integration with other systems.

Salesforce offers two primary methods for data export:

1. **Data Export Service**:
   - Accessible through the Setup menu, it enables manual exports once every 7 or 29 days (weekly or monthly).
   - Automatic exports at weekly or monthly intervals are also available.
   - Weekly exports are supported in Enterprise, Performance, and Unlimited Editions. In Professional and Developer Editions, backups can be generated every 29 days or automatically on a monthly basis.

2. **Data Loader**:
   - A client application that requires separate installation.
   - Operable via user interface or command line.
   - Command line operation is beneficial for automation or integration with other systems using APIs.
     
### Exporting Data Using the Data Export Service:

Follow these steps to export data using the Data Export Service:

1. **Access Data Export Service**:
   - From Setup, enter Data Export in the Quick Find box, then select Data Export.

2. **Initiate Export**:
   - Choose between "Export Now" or "Schedule Export."
     - "Export Now" prepares files for immediate export, available if sufficient time has elapsed since the last export.
     - "Schedule Export" allows scheduling exports at monthly intervals.

3. **Encoding and Options**:
   - Select the desired encoding for the export file.
   - Choose options like including images, documents, attachments, etc.
   - Opt to replace carriage returns with spaces if needed for integrations.

4. **Schedule (if applicable)**:
   - If scheduling, specify frequency, start and end dates, and time of day.
   - Frequency options are available for organizations with monthly exports.

5. **Select Data to Export**:
   - Under "Exported Data," choose the types of data to include. It's recommended to select "Include all data" if unsure.

6. **Start Export**:
   - Click "Start Export" or "Save."
   - Salesforce will create a zip archive of CSV files and notify you via email when it's ready.
   - The completion time is not guaranteed, especially for large exports broken into multiple files.
   - Download the zip file from the provided link in the email or by accessing "Data Export."
   - Note: Zip files are deleted 48 hours after the email is sent.

# Approve Records with Approval Processes

## Notes on "Customize How Records Get Approved"

An approval process automates the approval of Salesforce records in your organization. Here's what you specify within an approval process:

1. **Approval Steps**:
   - Define the steps required for a record to be approved and designate who approves it at each step.
   - For instance, when an employee submits a time-off request, Salesforce can automatically send an approval request to the employee’s manager.

2. **Actions**:
   - Determine actions based on outcomes during the approval process.
   - For example:
     - If a time-off request is approved, update fields on the employee’s record.
     - If the request is rejected, send a notification to the employee.

By configuring these aspects, you streamline and automate the approval workflow within Salesforce, enhancing efficiency and ensuring consistency.

### Building an Approval Process:

To automate the approval of opportunities discounted more than 40%, and reflect their approval status as Approved or Not Approved, follow these steps:

1. **Plan Your Business Process**:
   - Define the workflow: When should an approval be triggered? Who should approve it? What actions should occur upon approval or rejection?
   - Ensure clarity on the criteria for discount percentage and the corresponding approval steps.

2. **Create Approval Process**:
   - Navigate to Setup and search for "Approval Processes."
   - Click on "New Approval Process" and select the object (e.g. Account) for which you're creating the process.

3. **Define Approval Steps**:
   - Set up the approval steps:
     - Create a criteria-based step where the discount percentage is greater than 40%.
     - Assign the step to the appropriate manager for approval.

4. **Specify Approval Actions**:
   - Determine actions based on approval outcomes:
     - If approved, update the opportunity's approval status to "Approved."
     - If rejected, update the status to "Not Approved."

5. **Activate Approval Process**:
   - Once configured, activate the approval process to start automating the approval of opportunities.

## Notes on "Build an Approval Process"

### Creating an Approval Process:

To create the approval process for opportunities in your org, follow these steps:

1. **Access Approval Processes**:
   - From Setup, enter "Approval" in the Quick Find box, then select "Approval Processes."

2. **Select Object**:
   - Under "Manage Approval Processes For," choose "Opportunity."

3. **Start Creation**:
   - Click on "Create New Approval Process" and select "Use Jump Start Wizard." This wizard simplifies the process by assisting in decision-making.

4. **Configure Approval Process**:
   - Follow the prompts in the Jump Start Wizard to configure the approval process:
     - Define entry criteria: Set criteria for opportunities where the discount percent is greater than 40%.
     - Specify the approval steps: Designate the managers who should approve opportunities meeting the criteria.
     - Set final approval actions: Determine actions to be taken upon approval, such as updating the opportunity's approval status to "Approved."
     - Set rejection actions: Define actions to be taken if an opportunity is rejected, such as updating the status to "Not Approved."

5. **Review and Activate**:
   - Review the configured approval process to ensure accuracy.
   - Once satisfied, activate the approval process to start automating approval for opportunities with discounts exceeding 40%.

### Adding a "Submit for Approval" Button to Opportunity Layout:

Follow these steps to add a button that allows users to initiate the approval process:

1. **Access Object Manager**:
   - From Setup, click the "Object Manager" tab.

2. **Select Opportunity**:
   - Click on "Opportunity."

3. **Navigate to Page Layouts**:
   - Click on "Page Layouts."

4. **Choose Opportunity Layout**:
   - Select the "Opportunity Layout."

5. **Access Button Section**:
   - In the layout editor's header bar, click on "Buttons."

6. **Drag Button**:
   - Drag the "Submit for Approval" button to the "Standard Buttons" section in the page layout.

7. **Save Changes**:
   - Click "Save" to apply the changes.

Please note:
- If your org utilizes Lightning Record Pages with Dynamic Actions, you should add the "Submit for Approval" action to the Lightning record page's highlights panel instead.
- Ensure to follow any additional guidelines specific to your organization's setup, especially if using Lightning Record Pages with Dynamic Actions.

# Record-Triggered Flows

## Notes on "Get Started with Triggered Flows"

### Triggered Flows:

1. **Trigger**:
   - The trigger identifies what initiates the flow. 
   - Triggers can be:
     - Scheduled: Launched at a set schedule.
     - Record change: Initiated when specific types of record changes occur.

2. **Criteria**:
   - Criteria specify the specifics of the trigger.
   - For scheduled triggers, criteria typically involve date and time considerations.
   - For record change triggers, criteria include the object and specific changes to field values.

3. **Action**:
   - Actions determine what the flow does.
   - Once triggered and criteria are met, the flow executes predefined actions to automate processes or perform tasks.

### Trigger Types:

There are three types of triggers, each serving distinct purposes:

1. **Schedule Trigger**:
   - **When It Runs**: At a time and frequency specified by the user.
   - **How to Use It**: Useful for running nightly batch jobs or other scheduled tasks.

2. **Platform Event Trigger**:
   - **When It Runs**: When a particular platform event message is received.
   - **How to Use It**: Ideal for subscribing to events and executing actions in response to those events.

3. **Record Trigger**:
   - **When It Runs**: When a record is created, updated, or deleted.
   - **How to Use It**: Valuable for automating processes like updating records and sending notifications based on changes to records.
     
### Record-Triggered Flow Overview:

A record-triggered flow is a commonly used automation in Salesforce, facilitating interaction with records in the organization. Its primary function is to execute actions based on specific triggers, following the principle: "If something happens, then do something else."

**Trigger Options**:
- **Only when a record is created**: Triggered solely upon record creation.
- **Anytime a record is updated**: Activated whenever a record is updated.
- **Anytime a record is created or updated**: Triggered for both record creation and updates.
- **Only when a record is deleted**: Triggered exclusively upon record deletion.

**Usage**:
Record-triggered flows are employed to update another record, send notifications, initiate processes, or maintain data consistency.

**Fine-Tuning Automation**:
Options within the record-triggered flow enable fine-tuning of automation, ensuring optimal performance and avoiding conflicts:
1. **Fast Field Update**:
   - Runs during the record update that triggered the flow, before the update is saved.
   - Used for updating the triggering record to optimize performance.
2. **Related Records and Actions**:
   - Executes during the record update that triggered the flow, after the update is saved.
   - Employed for automating processes involving related records or additional actions.
3. **Run Asynchronously**:
   - Runs immediately after the triggering record update is complete.
   - Useful for executing more complex scenarios without impacting the initial transaction.
4. **Scheduled Paths**:
   - Executes in the future based on specified dates and times after the trigger fires.
   - Suitable for scheduling reminders or follow-ups based on record data.

These options provide flexibility in designing and executing automated processes efficiently within Salesforce.

## Notes on "Build a Record-Triggered Flow"

### Planning and Explaining Your Record-Triggered Flow:

Before building your record-triggered flow, it's essential to plan and consider the following questions:

1. **Timing**:
   - Does the flow run immediately upon a record update, or is it scheduled to run at a specific time or frequency?

2. **Action**:
   - Does the flow create another record, update an existing record, or perform other actions such as sending notifications or initiating processes?

3. **Execution Frequency**:
   - Does the flow run every time a record is updated, or only when specific criteria are met for the first time?

4. **Sketching**:
   - Sketch your business processes to visualize the flow of actions and interactions between records.
   - Use the Description fields within Flow Builder to explain the intent and design of your flow. These descriptions serve as documentation for yourself and others who may update the flow in the future.

### Configure Start:

To begin configuring your record-triggered flow, follow these steps:

1. **Access Flows**:
   - From Setup, enter "Flows" in the Quick Find box, then select "Flows."

2. **Create New Flow**:
   - Click on "New Flow."

3. **Select Record-Triggered Flow**:
   - Choose "Record-Triggered Flow."

4. **Create Flow**:
   - Click "Create" to proceed. This action opens the Configure Start window.

### Define the Trigger

### Define the Trigger:

![DefineTrigger](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/e22a9046-5a5d-44fb-8a63-98237836480e)

Configure the Start element's configuration panel with the following steps:

1. **Object Selection**:
   - Select "Opportunity" as the object.

2. **Trigger Configuration**:
   - For "Trigger the Flow When," choose "A record is created or updated."
   - Conditions are checked every time a record is updated to ensure no changes are missed.
   
3. **Condition Requirements**:
   - Select "All Conditions Are Met (AND)."

4. **Define First Condition**:
   - Field: StageName
   - Operator: Equals
   - Value: ClosedWon
   - Click "Add Condition."

5. **Define Second Condition**:
   - Field: Amount
   - Operator: Greater Than
   - Value: 25000

6. **When to Run the Flow for Updated Records**:
   - Select "Only when a record is updated to meet the condition requirements."
   - This option ensures the flow runs only when the triggered record changes from not meeting to meeting the entry conditions.

7. **Optimize the Flow For**:
   - Select "Actions and Related Records."
   - This option allows updating any record and performing actions, not just the record that triggered the flow.

By configuring the trigger in this manner, you ensure that the flow executes efficiently and effectively based on defined criteria.

### Create a New Record:

To create a Salesforce record using values from the flow, follow these steps:

1. **Add Create Records Element**:
   - On the flow canvas, after the Start element, hover over "Add Element," and click "Add Element."
   - Scroll to the Data section and click "Create Records" to open the New Create Records window.

2. **Configure Create Records Element**:
   - **Label**: Enter "Create Draft Contract." The API Name is automatically updated to "Create_Draft_Contract."
   - **Description**: Enter "Create a draft contract when an opportunity is won and is over $25,000."
   - **How Many Records to Create**: Keep "One" selected.
   - **How to Set the Record Fields**: Select "Use separate resources and literal values."

3. **Select Object**:
   - For "Object," select "Contract."

4. **Set Field Values for the Contract**:
   - Associate the new contract with the opportunity’s account:
     - **Field**: AccountId
     - **Value**: $Record > Account > Id (automatically updates to $Record > Account ID > Account ID)
     - Note: Data from the triggering record is stored in the $Record variable. Use $Record to access values from the triggering opportunity.

5. **Add Field**:
   - Click "Add Field."

6. **Set Status of the New Contract**:
   - **Field**: Status
   - **Value**: Draft

By following these steps, you can create a new contract record based on values from the triggering opportunity within your flow.

### Save and Debug:

![TriggerDebugger](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/1884942c-c592-41c9-8027-2a7e224135f6)

After completing the creation of your record-triggered flow, it's crucial to save and debug it to ensure proper functionality and identify any potential issues. Follow these steps:

1. **Save Flow**:
   - Click "Save."
   - For "Flow Label," enter "Closed Won Opportunities." The Flow API Name field is automatically updated to "Closed_Won_Opportunities."
   - For "Description," enter "If a high-value opportunity is closed and won, create a draft contract."
   - Click "Save."

2. **Debug Flow**:
   - Click "Debug" to initiate the debugging process.
   - If there were multiple paths, you can only debug one path at a time.
   - Under "Debug Options," select "Skip start condition requirements."
   - For "Run the Flow As If the Record Is," select "Updated."
   - Find and select an opportunity to run the flow against. Ensure the selected opportunity is "Closed Won" and its amount is over $25,000. For example, use "Grand Hotels Emergency Generators."
   - Verify that the amount in the opportunity is larger than $25,000. If not, enter an amount larger than $25,000.
   - Click "Run" to debug the flow.
   - The flow is debugged, and if it ran successfully, the line connecting elements is dark blue.
   - The Debug screen displays the flow diagram, debugging details, and "Completed" status.

![Complete_trigger](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/60ddd8cd-2884-4d57-b994-f88da7013fdf)
   
3. **View Debug Details**:
   - Click "Expand All" to view more information in the Debug Details panel.

4. **Exit Flow Builder**:
   - Click "Back (←)" to exit Flow Builder.

By saving and debugging your flow, you ensure that it functions correctly and is ready for activation without impacting your data.

## Notes on "Add a Scheduled Task to Your Flow"

### Automation on a Schedule:

By default, a record-triggered flow executes immediately when a record changes in a specified way. However, you can introduce a delay in the automation process by utilizing a scheduled path. Actions on a scheduled path are triggered by the specified record change but run after a designated period of time. This delay can range from minutes to hours, days, or even months after the record change occurs. Additionally, scheduled actions can be set relative to a specific field on the triggering record. 

**Examples include:**
- Initiating actions 6 hours after a case is created.
- Triggering processes 2 days after the renewal date for an account.
- Sending reminders 1 month before the warranty expiration date.

Utilizing scheduled paths allows for more flexible and dynamic automation processes, enabling actions to be executed at optimal times based on specific criteria and timelines.

### Before You Start:

Before creating scheduled paths in your Salesforce org, it's important to define the Default Workflow User. This setting specifies the user who executes the scheduled paths of your flows. While this setting may already be configured in your production org, it's essential to verify and ensure its correctness, especially in the Trailhead Playground used for this badge.

**Steps to Set Default Workflow User:**
1. Navigate to the Process Automation Settings page in Setup.
2. Locate the Default Workflow User setting.
3. Verify that the specified user is appropriate for executing scheduled paths.
4. If necessary, update the Default Workflow User to the desired user.

![DefaultWorkflow](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/a1b2a24f-7f7d-4104-a35d-19ed1b6c6ad7)

By setting the Default Workflow User correctly, you ensure that scheduled paths in your flows are executed by the designated user, aligning with your org's automation requirements and processes.

### Configure Scheduled Paths:

To configure scheduled paths within your flow, follow these steps:

1. **Access Flow Definitions:**
   - Under Flow Definitions, select the Triggered Flows list view.

2. **Open the Flow:**
   - In the Flow Label column, click on "Closed Won Opportunities" to open the flow.

3. **Add Scheduled Paths:**
   - Click "Add Scheduled Paths (Optional)" to initiate the configuration of scheduled paths.
   
![NewScheduledPathView](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/e3f1a4fa-4156-4c4f-9332-78306c9f8ea0)

4. **Configure Scheduled Path Details:**
   - **Path Label**: Enter "5 Days After Close." The API Name field is automatically updated to "X5_Days_After_Close."
   - **Time Source**: Select "Opportunity: Close Date."
   - **Offset Number**: Enter "5."
   - **Offset Options**: Choose "Days After."

By configuring the scheduled path in this manner, you ensure that the specified actions will be triggered to execute 5 days after the close date of the opportunity.

### Add a Task:

To add a task to remind the opportunity owner to follow up with the account owner five days after the opportunity closes, follow these steps:

1. **Add Create Records Element**:
   - On the 5 Days After Close path, hover over "Add Element," and click "Add Element."
   - Scroll to the Data section and click "Create Records" to open the New Create Records window.

2. **Configure Create Records Element**:
   - **Label**: Enter "Personalized Welcome Task." The API Name is automatically updated to "Personalized_Welcome_Task."
   - **Description**: Enter "Creates a task for the Account owner to reach out and welcome a new customer."
   - **How Many Records to Create**: Keep "One" selected.
   - **How to Set the Record Fields**: Select "Use separate resources and literal values."
   - **Object**: Select "Task."

3. **Set Field Values for the Task**:
   - **Subject**: Enter "Personalized Welcome."
   - **Description**: Enter "Reach out with phone call to welcome new customer."
   - **WhatId**: Set to "$Record > Id" to associate the task with the opportunity that triggered the flow.
   - **OwnerID**: Set to "$Record > OwnerId" to match the owner of the opportunity.
   - **Activity Date**: Enter today’s date.

4. **Save the Task**:
   - Click "Save As" > "A New Version" > "Save."

By following these steps, you add a scheduled task to remind the opportunity owner to follow up with the account owner five days after the opportunity closes. Now, proceed to test to ensure the task is functioning correctly.

## Notes on "Meet Flow Trigger Explorer"

Flow Trigger Explorer also allows you to see and reorder record-triggered orchestrations. You can even mix the order of record-triggered flows and record-triggered orchestrations! However, this badge focuses on record-triggered flows, so we’ll just focus on those here.

### Open Flow Trigger Explorer

1. Navigate to the "Closed Won Opportunities" flow.
2. At the bottom of the Start element, click on "Open Flow Trigger Explorer for Opportunity". Note that if the flow's trigger was set to a different object, that object would be listed instead.
3. 
![FlowTriggerExplorerOpener](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/8e4d8e30-71c1-4c7c-8651-bae4d1d63d1d)

**Note:**
You can also access Flow Trigger Explorer from Object Manager or from the Flows list view:
- From Object Manager: Click the object containing the flows you want to manage, then click "Flow Triggers," and finally click "Flow Trigger Explorer."
- From the Flows list view: Click "Flow Trigger Explorer," located next to the "New Flow" button.

### Explore Flow Trigger Explorer

Let’s take a quick tour of the Flow Trigger Explorer. Below are numbered sections of the Flow Trigger Explorer screen along with descriptions:

![FlowTriggerExplorer](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/987dab0a-03f8-4a50-a31b-e0ecfb681fc3)

1. Object/Trigger Manager:

To see flows for a different object, click the down arrow next to the current object (e.g., "Opportunity") and select another object. 
To change the type of record change trigger (e.g., "Updated"), click the down arrow next to it and select another trigger: Created, Updated, or Deleted. 
To filter flows by status, process type, and more, click "Filters."

2. Categorized Flows:

Flow Trigger Explorer lists all record-triggered flows that execute for the selected object and trigger. Flows are grouped by when they run: before the record is saved, after the record is saved, or asynchronously. Within those groups, flows appear in the order in which they run.

Note: 
Within the "Run Asynchronously" group, the exact order of flow execution cannot be guaranteed due to the nature of asynchronous processing.

3. Status and Details Pane:

- The "Flow Details" section displays the following information about the flow:
  - Open Flow button
  - Name and description
  - Version number
  - Status
  - Last modified by
  - Process type
  - Trigger
- The "Versions" section displays all versions of the flow. It includes buttons to open the flow in Flow Builder and to manage its active status (Activate or Deactivate).

### Reorder Your Flows

If you have multiple flows that need to run in a specific order from the same trigger, you can easily reorder them using Flow Trigger Explorer without needing to open each flow individually. Here's how:

1. **Click Edit Order**:
   - Locate the section containing the flows you want to reorder.
   - Click the "Edit Order" button in that section.

2. **Drag and Drop Flows**:
   - Click and hold the drag-and-drop handle next to the flow you want to reorder.
   - Drag the flow to the desired position in the flow order.
   - Flow Trigger Explorer will highlight the flow as you move it, indicating the changes.

3. **Repeat for Other Flows**:
   - If you have more flows to reorder, repeat the dragging process for each flow until you're satisfied with the order.

4. **Click Update**:
   - After arranging the flows, click the "Update" button to save the new order.
   
![ReorderFlows](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/68b248d3-33de-4d79-83e3-e4db651ed504)

Keyboard Shortcuts:

You can utilize keyboard shortcuts for quicker flow reordering:

- **Switch Section Focus**: Press **F6**
- **Select Flow**: Press **Spacebar**
- **Move Flow**: Use the **Arrow keys**
- **Drop Flow**: Press **Spacebar**
- **Cancel Reordering**: Press **Esc**

### Monitor Your Record-Triggered Flows

Once you've configured and activated your record-triggered flows, it's essential to monitor their execution, especially for scheduled paths that run in the future. Salesforce provides the Time-Based Workflow page for this purpose.

![TimeBasedWorkflow](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/16daeefd-2b37-427e-be84-e19c6bb97671)

1. **Access Time-Based Workflow**:
   - From Setup, enter "Time" in the Quick Find box, then select "Time-Based Workflow."

2. **View Pending Actions**:
   - Click "Search" to view all pending actions for active flows.
   - You'll see individual instances of the flow scheduled for future execution.

3. **Filter Pending Actions**:
   - Define filter criteria using filter types, operators, and values.
     - **Workflow Rule, Flow, or Process Name:** Enter the name of the flow.
     - **Object:** The object that triggered the flow.
     - **Scheduled Date:** Date the pending action is scheduled to run.
     - **Created Date:** Date the triggering record was created.
     - **Automation Type:** Type of automation that triggered the flow.
     - **Created By:** User who created the triggering record.
     - **User ID:** ID of the user who created the triggering record.
     - **Record Name:** Name of the triggering record.
   - Click "Search" after defining the filter criteria.

4. **Cancel Pending Actions**:
   - Select the pending actions you want to cancel.
   - Click "Delete" to cancel them.

# Search Solution Basics

## Notes on "Choose the Right Search Solution"

The No. 1 most-used Salesforce feature is search. Users rely on search to find specific records among thousands of data entries. Understanding how Salesforce search functions and determining if a custom search solution is appropriate is crucial.

### How Salesforce Search Works

1. **Data Storage**: All records are stored as data fields in the organization's database.
2. **Indexing**: When a record is updated or created, the search engine creates tokens by breaking down the data

### Why you should use Search Index

- The search index and tokens empower search engines with features like spell correction, lemmatization, and synonym groups.
- These features broaden search results by including variations of the user's query.
- Relevance ranking factors in search term frequency and record activity.
- Custom search solutions are necessary for organizations with unique UI requirements.
- Despite requiring extra effort, custom solutions still leverage advanced search features and APIs for record retrieval.

### Connect to Search with APIs

In Salesforce, there are two main APIs for searching: Salesforce Object Query Language (SOQL) and Salesforce Object Search Language (SOSL). Here's a comparison between the two:

- **SOQL**:
  - Equivalent to a SELECT SQL statement.
  - Searches the org database.
  - Used when you know in which objects or fields the data resides and want to retrieve specific data or perform operations like counting records or sorting results.

- **SOSL**:
  - Programmatic way of performing text-based searches against the search index.
  - Utilizes advanced search index features such as spell correction, nicknames, lemmatization, and synonym groups.
  - Used when you don’t know in which object or field the data resides and want to search for specific terms efficiently across multiple objects and fields.

### Additional APIs for search include:

1. **Suggested Records API**:
   - Provides auto-suggestion, instant results, or type-ahead functionality.
   - Allows users to quickly find relevant records by suggesting search terms as they type.
   - Available through the Search Suggested Records and Search Suggested Articles REST methods.

2. **Salesforce Federated Search**:
   - Enables searching for items stored outside of Salesforce while remaining within Salesforce Classic, Salesforce Console, or Lightning Experience.
   - Makes the global search box an external search engine by transferring the user’s query to the external engine and displaying the results within Salesforce.
   - Implemented using the Salesforce Federated Search connector, built using the OpenSearch specification.
   - Doesn't utilize the Salesforce search index; results are returned based on the external search provider.

### Send Queries with Protocols

To execute search queries in Salesforce, you need to use API protocols such as REST, SOAP, or Apex. Here's how these protocols interact with the different search APIs:

- **REST and SOAP**:
  - Use **query** (REST) and **query()** (SOAP) to execute SOQL queries against specific objects.
  - Use **search** (REST) and **search()** (SOAP) to execute SOSL text string searches against your org's data.
  - These protocols are commonly used to interact with Salesforce data and execute both SOQL and SOSL queries.

- **Parameterized Search in REST**:
  - Offers an alternative to traditional SOSL searches by using parameters in the URL instead of a search string.
  - Useful for more controlled and parameterized searches within Salesforce.

- **Apex**:
  - Allows you to use SOQL or SOSL directly within Apex code.
  - SOQL queries can be executed by enclosing them in square brackets `[SELECT ... FROM ...]`.
  - Dynamic SOSL queries can be performed using the **Search Class** in Apex.
  - The **Search Namespace** provides methods for retrieving search results and suggestion results within Apex.

Additional resources are available for performing common search tasks like auto-suggesting records, articles, and queries. It's essential to review the developer documentation for detailed information and examples on using these protocols effectively.

## Notes on "Build Search for Common Use Cases"

### Search Within a Single Object
- Single object searches are best used when users need a quick way to search through one record type because they don’t want to be bogged down sifting through records that don’t apply to them.
- To search within a single object using SOSL, simply specify that object in the request. It’s just that easy.
  ```sql
      FIND {term} RETURNING ObjectTypeName
  ```
### Search Within Multiple Objects
- There are multiple types of information included: videos, articles, questions, and PDFs. But users don’t really care what type of content is presented. They only care that they get the information they need—and fast. That’s what makes the multiple object search beneficial: You get to search through multiple objects at the same time because the type of record doesn’t matter to the user. Or, perhaps the user actually wants to see multiple object types in results.
- Luckily, adding another object is super simple in SOSL. Just add a comma-separated list.
  ```sql
      FIND {term} RETURNING ObjectTypeName1, ObjectTypeName2, ObjectTypeNameYouGetTheIdea
  ```
- Remember, if you don’t specify an object in the RETURNING element, it doesn’t show up in the results.

### Search Within Custom Object

- For the internal product website you’re building, you’re adding a search box that only searches the Merchandise custom object. There’s no fancy way to specify custom objects in a SOSL search. Include the sObject name like any other standard object, and append a __c suffix.
    ```sql
        FIND {pink hi\-top} RETURNING Merchandise__c
    ```

### SOQL
- You use SOQL for single object searches, when you know the fields to be searched, when the search term is an exact match for the field (no partial or out-of-order matches), when you need number, date, or checkbox field data, and when you’re looking for just a few results. (For a full list of when to use SOQL, refer to the previous unit.)
- Looking at the previous common use cases in this module, you might have noticed some red flags for using SOQL. For example:
  - Single object search—SOQL works when you know which field the data resides in. Because we didn’t specify the field for march 2016 email, a SOSL search is preferred over SOQL. In general, if the query doesn’t specify a target field to search, SOSL works.
  - Multiple object search—Because you can only retrieve data from multiple objects that are related to one another, the multiple object search in SOQL is limited. In our scenario, the objects were unrelated, so SOSL it is.
  - Custom object search—As with a single object search, you could search through custom objects in SOQL fine. However, we didn’t specify in which field the data resides for our search term pink hi-top. In addition, SOQL searches against the database, not the index. So, let’s say that the record actually included pink sneaker hi-top. SOQL wouldn’t be able to return the right results.

## Notes on "Optimize Search Results"

### Create Efficient Text Searches

#### SOSL Syntax
- Search queries can be expensive. The more data you’re searching through and the more results you’re returning, the more you can slow down the entire operation.

- How do you combat sluggish searches? With two basic strategies:

  1. Limit which data you’re searching through
  2. Limit which data you’re returning

- But you don’t want to go too far. If you’re too efficient, the search is less useful. Users don’t like it when they see record not found when they know that the record exists.

- Here are some recommendations to create a happy medium. Let’s start with a SOSL example to understand the basic concepts.

  - To limit which data is searched, use IN SearchGroup. You can search for name, email, phone, sidebar, or all fields. For example, if you want to search only for an email, you search through only email fields.

    ```sosl
    FIND {jsmith@cloudkicks.com} IN EMAIL FIELDS RETURNING Contact
    ```
    
- Turning to SOSL, you can use RETURNING FieldSpec to specify which data is returned. We used it in the last unit, but let’s talk about the more advanced elements it includes.
    - ObjectTypeName — Specifies the object to return.
    - FieldList — Specifies the fields to return.
    - ORDER By — Specifies which fields to order the results by. You can also specify ascending or descending order.
    - LIMIT n — Sets the maximum number of records returned for the given object.
    - OFFSET n — Sets the starting row offset into the result set returned by your query.


| Step | Goal                                 | Example                                                  |
|------|--------------------------------------|----------------------------------------------------------|
| 1    | Specify the object to return.        | FIND {Cloud Kicks} RETURNING Account                     |
| 2    | Specify the field to return.         | FIND {Cloud Kicks} RETURNING Account(Name, Industry)     |
| 3    | Order the results by field in ascending order, which is the default. | FIND {Cloud Kicks} RETURNING Account (Name, Industry ORDER BY Name) |
| 4    | Set the max number of records returned | FIND {Cloud Kicks} RETURNING Account (Name, Industry ORDER BY Name LIMIT 10) |
| 5    | Set the starting row offset into the results. | FIND {Cloud Kicks} RETURNING Account (Name, Industry ORDER BY Name LIMIT 10 OFFSET 25) |

- With statements
    - By prefiltering results, you return fewer results and improve performance
    - WITH DIVISION
    ```
    FIND {Cloud Kicks} RETURNING Account (Name, Industry) WITH DIVISION = 'Global'
    ```
    - WITH DATA CATEGORY
    ```
    FIND {race} RETURNING KnowledgeArticleVersio(Id, Title WHERE PublishStatus='online' and language='en_US') WITH DATA CATEGORY Location__c AT America__c
    ```
    - WITH NETWORK
    ```
    FIND {first place} RETURNING User (Id, Name), FeedItem (id, ParentId WHERE CreatedDate = THIS_YEAR Order by CreatedDate DESC) WITH NETWORK = '00000000000001'
    ```
    - WITH PRICEBOOK
    ```
    Find {shoe} RETURNING Product2 WITH PricebookId = '01sxx0000002MffAAE'
    ```
        
#### SOQL Syntax

| I want to...                        | SOSL                | SOQL                |
|-------------------------------------|---------------------|---------------------|
| Limit the data that is searched    | IN SearchGroup      | WHERE               |
| Specify the data to be returned    | RETURNING FieldSpec | SELECT              |
| Sort results                       | ORDER BY            | ORDER BY            |
|                                    | LIMIT               | LIMIT               |
|                                    | OFFSET              | OFFSET              |
| Filter by data category            | WITH DATA CATEGORY  | WITH DATA CATEGORY  |

### Setting Up Synonym Groups

Optimizing search results is a team sport. The best person for the assist: your MVP admin. The first step is setting up and optimizing synonym groups.

- Synonym groups are words or phrases that are treated equally in searches.
- A search for one term in a synonym group returns results for all terms in the group
    - e.g. a search for USB returns results for all terms in the synonym group, which contains USB, thumb drive, flash stick, and memory stick

**Creating synonym groups is easy**
1. From Setup, enter Synonyms in the Quick Find box, then select Synonyms.
2. Under Custom Synonym Groups, click New to start a synonym group or Edit next to an existing group.
3. Add two to six synonyms per group. A synonym can be any word or phrase. No special characters.

**Note:** Standard synonym groups, which are provided by Salesforce, are enabled by default.

### Promoted Search Terms for Knowledge Articles

Promoted Search Terms are useful for getting more eyeballs on an article that you know is commonly used to resolve a support issue.
Admins find the perfect article and then add terms to the Promoted Search Terms related list.
 
1. Admins find the perfect article and then add terms to the Promoted Search Terms related list.
2. Users who enter those keywords see the chosen article at the top in search results.

**Tips for Fine-tuning Promoted Search Terms:**
- The maximum number of characters per term is 100.
- For best results when matching users’ search terms, limit each promoted term to a few keywords.
- Use promoted terms selectively to avoid affecting relevance ranking for users.
- Your organization can create a maximum of 2,000 promoted terms.

**Note:** Article search matches a promoted term whenever all keywords within the term occur within the user’s search terms, in any sequence. Each keyword must match exactly.

# Apex Basics & Database

## Notes on "Get Started with Apex"

### What is Apex?

Apex is a programming language that uses Java-like syntax and acts like database stored procedures. It enables developers to add business logic to system events, such as button clicks, updates of related records, and Visualforce pages.

### Key Features of Apex:

- **Hosted:** Apex is saved, compiled, and executed on the server—the Lightning Platform.
- **Object Oriented:** Supports classes, interfaces, and inheritance.
- **Strongly Typed:** Validates references to objects at compile time.
- **Multitenant Aware:** Runs in a multitenant platform, enforcing limits to prevent code from monopolizing shared resources.
- **Integrated with the Database:** Provides direct access to records and their fields, and offers statements and query languages to manipulate those records.
- **Data Focused:** Provides transactional access to the database, allowing you to roll back operations.
- **Easy to Use:** Based on familiar Java idioms.
- **Easy to Test:** Built-in support for unit test creation, execution, and code coverage. Salesforce ensures that all custom Apex code works as expected by executing all unit tests prior to any platform upgrades.
- **Versioned:** Custom Apex code can be saved against different versions of the API.

### Supported Language Constructs:

- Classes, interfaces, properties, and collections (including arrays).
- Object and array notation.
- Expressions, variables, and constants.
- Conditional statements (if-then-else) and control flow statements (for loops and while loops).

### Unique Features of Apex:

- **Cloud Development:** Apex is stored, compiled, and executed in the cloud.
- **Triggers:** Similar to triggers in database systems.
- **Database Statements:** Allow direct database calls and query languages to query and search data.
- **Transactions and Rollbacks:** Support for transactions and rollbacks.
- **Global Access Modifier:** More permissive than the public modifier, allowing access across namespaces and applications.
- **Versioning of Custom Code:** Supports versioning of custom code.
- **Case-Insensitive Language:** Apex is case-insensitive.

### Development Tools:

- **Salesforce Extensions for Visual Studio Code:** Allows writing and debugging Apex on the client computer. See [Salesforce Visual Studio Code Extensions](https://developer.salesforce.com/tools/vscode?_ga=2.21094267.1973390464.1710861252-1789462011.1704986675).
- **Salesforce User Interface:** Allows writing Apex and accessing debugging information directly in the browser using the Developer Console.

### Data Types Overview

Apex supports various data types, including a data type specific to Salesforce—the `sObject` data type. Below are the data types supported by Apex:

#### Primitive Data Types:

- Integer
- Double
- Long
- Date
- Datetime
- String
- ID
- Boolean
- And others

#### sObject Data Type:

- A generic `sObject`
- Specific `sObject` types, such as `Account`, `Contact`, or `MyCustomObject__c` (you’ll learn more about sObjects in a later unit.)

#### Collection Data Types:

- List (or array) of primitives, sObjects, user-defined objects, objects created from Apex classes, or collections
- Set of primitives, sObjects, user-defined objects, objects created from Apex classes, or collections
- Map from a primitive to a primitive, sObject, or collection
- Typed list of values, also known as an enum

#### Other Data Types:

- User-defined Apex classes
- System-supplied Apex classes

### Inspect Debug Logs

Debug logs are useful for debugging your code. When Apex methods execute, the calls are logged in the debug log. Additionally, you can write your own debug messages to the log, which helps in debugging your code in case there are errors. 

### Viewing Debug Logs:

1. In the Developer Console, click the Logs tab and double-click the most recent log in the list.
2. Select Debug Only to filter the log so that only log lines for `System.debug()` statements are shown.
3. Filter the debug log in the Developer Console to view debug messages.

# Notes on "Use sObjects"

- Every record in Salesforce is natively represented as an sObject in Apex
- Each Salesforce record is represented as an sObject before it is inserted into Salesforce.
    - Likewise, when persisted records are retrieved from Salesforce, they’re stored in an sObject variable.

# Notes on "Manipulate Records with DML"

- provides a straightforward way to manage records by providing simple statements to insert, update, merge, delete, and restore records.
- has direct access to your data in Salesforce

### DML Statements

The following DML statements are available:

- `insert`
- `update`
- `upsert`
- `delete`
- `undelete`
- `merge`

Each DML statement accepts either a single sObject or a list (or array) of sObjects. Operating on a list of sObjects is a more efficient way for processing records.

All those statements, except a couple, are familiar database operations. The `upsert` and `merge` statements are particular to Salesforce and can be quite handy.

### Upsert Statement

The `upsert` DML operation creates new records and updates sObject records within a single statement, using a specified field to determine the presence of existing objects, or the ID field if no field is specified.

### Merge Statement

The `merge` statement merges up to three records of the same sObject type into one of the records, deleting the others, and re-parenting any related records.

### ID Field Auto-Assigned to New Records

When inserting records, the system assigns an ID for each record. In addition to persisting the ID value in the database, the ID value is also auto-populated on the sObject variable that you used as an argument in the DML call.

```java
    // Create the account sObject 
    Account acct = new Account(Name='Acme', Phone='(415)555-1212', NumberOfEmployees=100);
    // Insert the account by using DML
    insert acct;
    // Get the new ID on the inserted sObject argument
    ID acctID = acct.Id;
    // Display this ID in the debug log
    System.debug('ID = ' + acctID);
    // Debug log result (the ID will be different in your case)
    // DEBUG|ID = 001D000000JmKkeIAF
```

**Note: Beyond the Basics**

Because the sObject variable in the example contains the ID after the DML call, you can reuse this sObject variable to perform further DML operations, such as updates, as the system will be able to map the sObject variable to its corresponding record by matching the ID.

You can retrieve a record from the database to obtain its fields, including the ID field, but this can’t be done with DML. You’ll need to write a query by using SOQL. You’ll learn about SOQL in another unit.

### Bulk DML

You can perform DML operations either on a single sObject, or in bulk on a list of sObjects. Performing bulk DML operations is the recommended way because it helps avoid hitting governor limits, such as the DML limit of 150 statements per Apex transaction. This limit is in place to ensure fair access to shared resources in the Lightning Platform. Performing a DML operation on a list of sObjects counts as one DML statement, not as one statement for each sObject.

This example inserts contacts in bulk by inserting a list of contacts in one call. The sample then updates those contacts in bulk too.

```java
// Create a list of contacts
List<Contact> conList = new List<Contact> {
    new Contact(FirstName='Joe',LastName='Smith',Department='Finance'),
        new Contact(FirstName='Kathy',LastName='Smith',Department='Technology'),
        new Contact(FirstName='Caroline',LastName='Roth',Department='Finance'),
        new Contact(FirstName='Kim',LastName='Shain',Department='Education')};
// Bulk insert all contacts with one DML call
insert conList;
// List to hold the new contacts to update
List<Contact> listToUpdate = new List<Contact>();
// Iterate through the list and add a title only
//   if the department is Finance
for(Contact con : conList) {
    if (con.Department == 'Finance') {
        con.Title = 'Financial analyst';
        // Add updated contact sObject to the list.
        listToUpdate.add(con);
    }
}
// Bulk update all contacts with one DML call
update listToUpdate;
```

### Upsert Records

If you have a list containing a mix of new and existing records, you can process insertions and updates to all records in the list by using the upsert statement. Upsert helps avoid the creation of duplicate records and can save you time as you don’t have to determine which records exist first.

The upsert statement matches the sObjects with existing records by comparing values of one field. If you don’t specify a field when calling this statement, the upsert statement uses the sObject’s ID to match the sObject with existing records in Salesforce. Alternatively, you can specify a field to use for matching. For custom objects, specify a custom field marked as external ID. For standard objects, you can specify any field that has the idLookup property set to true. For example, the Email field of Contact or User has the idLookup property set.

#### Syntax

`upsert sObject | sObject[]`
`upsert sObject | sObject[]` `field`

```java
upsert sObjectList Account.Fields.MyExternalId;
```
Upsert uses the sObject record's primary key (the ID), an idLookup field, or an external ID field to determine whether it should create a new record or update an existing one:
- If the key is not matched, a new object record is created.
- If the key is matched once, the existing object record is updated.
- If the key is matched multiple times, an error is generated and the object record is neither inserted or updated.

```java
// Insert the Josh contact
Contact josh = new Contact(FirstName='Josh',LastName='Kaplan',Department='Finance');       
insert josh;
// Josh's record has been inserted
//   so the variable josh has now an ID
//   which will be used to match the records by upsert
josh.Description = 'Josh\'s record has been updated by the upsert operation.';
// Create the Kathy contact, but don't persist it in the database
Contact kathy = new Contact(FirstName='Kathy',LastName='Brown',Department='Technology');
// List to hold the new contacts to upsert
List<Contact> contacts = new List<Contact> { josh, kathy };
// Call upsert
upsert contacts;
// Result: Josh is updated and Kathy is created.
```

**Note**: The upsert call uses the ID to match the first contact. The josh variable is being reused for the upsert call. This variable has already been populated with the record ID from the previous insert call, so the ID doesn’t need to be set explicitly in this example.

### Delete Records
- You can delete persisted records using the delete statement.
- Deleted records aren’t deleted permanently from Lightning Platform, but they’re placed in the Recycle Bin for 15 days from where they can be restored.

```java
Contact[] contactsDel = [SELECT Id FROM Contact WHERE LastName='Smith']; 
delete contactsDel;
```

### DML Statement Exceptions
- If a DML operation fails, it returns an exception of type DmlException

```java
try {
    // This causes an exception because 
    //   the required Name field is not provided.
    Account acct = new Account();
    // Insert the account 
    insert acct;
} catch (DmlException e) {
    System.debug('A DML exception has occurred: ' +
                e.getMessage());
}
```

### Database Methods

Apex contains the built-in Database class, which provides methods that perform DML operations and mirror the DML statement counterparts.

- Database.insert()
- Database.update()
- Database.upsert()
- Database.delete()
- Database.undelete()
- Database.merge()

Database methods have an optional allOrNone parameter that allows you to specify whether the operation should partially succeed. By default, the allOrNone parameter is true.

```java
// You do not have to pass in a boolean if you allOrNone is true
Database.insert(recordList);

// You can pass in true if you want, though
Database.insert(recordList, true);
```
```java
// Pass in false to allow for partial success
Database.insert(recordList, false);
```

```java
// On a partial success the method returns the objects that were successfull
Database.SaveResult[] results = Database.insert(recordList, false);
```


**Beyond the Basics**: In addition to these methods, the Database class contains methods that aren’t provided as DML statements. For example, methods used for transaction control and rollback, for emptying the Recycle Bin, and methods related to SOQL queries. You’ll learn about SOQL in another unit.

### Example: Insert Records with Partial Success
```java
// Create a list of contacts
List<Contact> conList = new List<Contact> {
        new Contact(FirstName='Joe',LastName='Smith',Department='Finance'),
        new Contact(FirstName='Kathy',LastName='Smith',Department='Technology'),
        new Contact(FirstName='Caroline',LastName='Roth',Department='Finance'),
        new Contact()};
// Bulk insert all contacts with one DML call
Database.SaveResult[] srList = Database.insert(conList, false);
// Iterate through each returned result
for (Database.SaveResult sr : srList) {
    if (sr.isSuccess()) {
        // Operation was successful, so get the ID of the record that was processed
        System.debug('Successfully inserted contact. Contact ID: ' + sr.getId());
    } else {
        // Operation failed, so get all errors
        for(Database.Error err : sr.getErrors()) {
            System.debug('The following error has occurred.');
            System.debug(err.getStatusCode() + ': ' + err.getMessage());
            System.debug('Contact fields that affected this error: ' + err.getFields());
	 }
    }
}
```

### Insert Related Records

You can insert records related to existing records if a relationship has already been defined between the two objects, such as a lookup or master-detail relationship. A record is associated with a related record through a foreign key ID.

```java
Account acct = new Account(Name='SFDC Account');
insert acct;
// Once the account is inserted, the sObject will be 
// populated with an ID.
// Get this ID.
ID acctID = acct.ID;
// Add a contact to this account.
Contact mario = new Contact(
    FirstName='Mario',
    LastName='Ruiz',
    Phone='415.555.1212',
    AccountId=acctID);
insert mario;
```

### Update Releated Records

Fields on related records can't be updated with the same call to the DML operation and require a separate DML call.

```java
// Query for the contact, which has been associated with an account.
Contact queriedContact = [SELECT Account.Name 
                          FROM Contact 
                          WHERE FirstName = 'Mario' AND LastName='Ruiz'
                          LIMIT 1];
// Update the contact's phone number
queriedContact.Phone = '(415)555-1213';
// Update the related account industry
queriedContact.Account.Industry = 'Technology';
// Make two separate calls 
// 1. This call is to update the contact's phone.
update queriedContact;
// 2. This call is to update the related account's Industry field.
update queriedContact.Account; 
```

### Delete Releated Records

The delete operation supports cascading deletions. If you delete a parent object, you delete its children automatically, as long as each child record can be deleted.

```java
Account[] queriedAccounts = [SELECT Id FROM Account WHERE Name='SFDC Account'];
delete queriedAccounts;
```

### Transactions and DML

DML operations execute within a transaction. All DML operations in a transaction either complete successfully, or if an error occurs in one operation, the entire transaction is rolled back and no data is committed to the database.

**Transaction Boundaries**
- a trigger
- a class method
- an anonymous block of code
- an Apex page
- a custom Web service method.

## Notes on "Write SOQL Queries"

Apex has direct access to Salesforce records that are stored in the database, you can embed SOQL queries in your Apex code and get results in a straightforward fashion.
This is referred to as **inline SOQL**.

```java
Account[] accts = [SELECT Name,Phone FROM Account];
```

### Use the Query Editor

The Developer Console provides the Query Editor console, which enables you to run your SOQL queries and view results.

### Basic SOQL Syntax

- Unlike other SQL languages, you can’t specify * for all fields.
    - You must specify every field you want to get explicitly.
    - If you try to access a field you haven’t specified in the SELECT clause, you’ll get an error because the field hasn’t been retrieved.
-  Id field in the query as it is always returned in Apex queries, whether it is specified in the query or not
-  Basic Reserved words:
    - SELECT
    - FROM
    - WHERE
    - ORDERBY
    - LIMIT
    - AND
    - OR
    - LIKE

```sql
SELECT fields FROM ObjectName [WHERE Condition]
```

### Accessing variables
```sql
String targetDepartment = 'Wingo';
Contact[] techContacts = [SELECT FirstName,LastName 
                          FROM Contact WHERE Department=:targetDepartment];
```

### Query Related Records

Records in Salesforce can be linked to each other through relationships: lookup relationships or master-detail relationships. To get child records related to a parent record, add an inner query for the child records.

```sql
SELECT Name, (SELECT LastName FROM Contacts) FROM Account WHERE Name = 'SFDC Computing'
```

You can traverse a relationship from a child object (contact) to a field on its parent (Account.Name) by using dot notation.

```sql
Contact[] cts = [SELECT Account.Name FROM Contact 
                 WHERE FirstName = 'Carol' AND LastName='Ruiz'];
Contact carol = cts[0];
String acctName = carol.Account.Name;
System.debug('Carol\'s account name is ' + acctName);
```

**Note**: Custom objects can also be linked together by using custom relationships. Custom relationship names end with the __r suffix.

### Query Record in Batches By Using SOQL For Loops

With a SOQL for loop, you can include a SOQL query within a for loop. 

- The results of a SOQL query can be iterated over within the loop.
- SOQL for loops use a different method for retrieving records—records are retrieved using efficient chunking with calls to the query and queryMore methods of the SOAP API.
- By using SOQL for loops, you can avoid hitting the heap size limit.

```java
for (variable : [soql_query]) {
    code_block
}

for (variable_list : [soql_query]) {
    code_block
}
```

**Note**: It is preferable to use the sObject list format of the SOQL for loop as the loop executes once for each batch of 200 sObjects. Doing so enables you to work on batches of records and perform DML operations in batch, which helps avoid reaching governor limits.

```java
insert new Account[]{new Account(Name = 'for loop 1'), 
                     new Account(Name = 'for loop 2'), 
                     new Account(Name = 'for loop 3')};
// The sObject list format executes the for loop once per returned batch
// of records
Integer i=0;
Integer j=0;
for (Account[] tmp : [SELECT Id FROM Account WHERE Name LIKE 'for loop _']) {
    j = tmp.size();
    i++;
}
System.assertEquals(3, j); // The list should have contained the three accounts
                       // named 'yyy'
System.assertEquals(1, i); // Since a single batch can hold up to 200 records and,
                       // only three records should have been returned, the 
                       // loop should have executed only once
```

## Notes on "Write SOSL Queries"

Salesforce Object Search Language (SOSL) is a Salesforce search language that is used to perform text searches in records. SOSL is similar to Apache Lucene.

```java
List<List<SObject>> searchList = [FIND 'SFDC' IN ALL FIELDS RETURNING Account(Name), Contact(FirstName,LastName)];
```

### Differences and Similarities Between SOQL and SOSL

Both SOQL and SOSL are querying languages in Salesforce, but they have distinct differences and similarities:

#### Differences:
1. **Scope of Querying:**
   - SOQL: Can only query one standard or custom object at a time.
   - SOSL: Can search across all objects in an organization in a single query.

2. **Matching Criteria:**
   - SOQL: Performs an exact match by default (when not using wildcards).
   - SOSL: Matches fields based on a word match, allowing for more flexible search results.

3. **Syntax:**
   - SOQL: Follows a structured query language syntax similar to SQL.
   - SOSL: Has its own syntax optimized for searching across multiple objects.

#### Similarities:
1. **Purpose:**
   - Both are used to search and retrieve data from Salesforce records.

2. **Usage:**
   - Both can be used to retrieve data based on specified criteria.

#### Use Cases:
- Use SOQL when you need to retrieve records for a single object and require precise matches.
- Use SOSL when you need to search fields across multiple objects, allowing for more flexible and broader search capabilities. SOSL queries are particularly useful for searching text fields across different objects.

### Basic SOSL Syntax

In SOSL, you can specify the following search criteria:

1. **Text Expression:** Specifies the single word or phrase to search for within the records.

2. **Scope of Fields:** Defines the fields to search within for the specified text expression.

3. **List of Objects and Fields:** Specifies the objects and fields from which to retrieve data.

4. **Conditions:** Sets conditions for selecting rows in the source objects based on the search criteria.

### Syntax Example:

**Apex**
```sosl
FIND {search_text} [IN field_list] RETURNING object_list [(field_list)][(condition_list)]
```

**Query Editor**
```sosl
FIND {SearchQuery} [IN SearchGroup] [RETURNING ObjectsAndFields]
```

- SearchQuery is the text to search for (a single word or a phrase).
    - Search terms can be grouped with logical operators (AND, OR) and parentheses.
    - Also, search terms can include wildcard characters (*, ?). The * wildcard matches zero or more characters at the middle or end of the search term. The ? wildcard matches only one character at the middle or end of the search term.
- Text searches are case-insensitive
- `SearchGroup`
    - is optional
    - default scope is all fields
    - **Values**
        - `ALL FIELDS`
        - `NAME FIELDS`
        - `EMAIL FIELDS`
        - `PHONE FIELDS`
        - `SIDEBAR FIELDS`
- `ObjectsAndFields`
    - is optional
    - It is the information to return in the search result.
        - a list of one or more sObjects and, within each sObject, list of one or more fields, with optional values to filter against.
    - If not specified, the search results contain the IDs of all objects found.

### Single words and phrases

A SearchQuery contains two types of text:

1. Single Word
    - single word, such as test or hello.
    - Words in the SearchQuery are delimited by spaces, punctuation, and changes from letters to digits (and vice-versa).
    - Words are always case insensitive.
3. Phrase
    - collection of words and spaces surrounded by double quotes such as "john smith".
    - Multiple words can be combined together with logic and grouping operators to form a more complex query.

### SOSL Apex 

```java
String soslFindClause = 'Wingo OR SFDC';
List<List<sObject>> searchList = [FIND :soslFindClause IN ALL FIELDS
                    RETURNING Account(Name),Contact(FirstName,LastName,Department)];
Account[] searchAccounts = (Account[])searchList[0];
Contact[] searchContacts = (Contact[])searchList[1];
System.debug('Found the following accounts.');
for (Account a : searchAccounts) {
    System.debug(a.Name);
}
System.debug('Found the following contacts.');
for (Contact c : searchContacts) {
    System.debug(c.LastName + ', ' + c.FirstName);
}
```

# Apex Triggers

## Notes on "Get Started With Apex Triggers

### Writing Apex Triggers

**Overview:**
Apex triggers enable you to perform custom actions before or after events to records in Salesforce, such as insertions, updates, or deletions. Just like database systems support triggers, Apex provides trigger support for managing records.

**Purpose:**
Triggers are typically used to perform operations based on specific conditions, to modify related records, or restrict certain operations from happening. You can use triggers to do anything you can do in Apex, including executing SOQL and DML or calling custom Apex methods.

**When to Use:**
Use triggers to perform tasks that can’t be done by using the point-and-click tools in the Salesforce user interface. For example, if validating a field value or updating a field on a record, use validation rules and flows. Use Apex triggers if performance and scale are important, if your logic is too complex for the point-and-click tools, or if you're executing CPU-intensive operations.

**Scope:**
Triggers can be defined for top-level standard objects, such as Account or Contact, custom objects, and some standard child objects. Triggers are active by default when created. Salesforce automatically fires active triggers when the specified database events occur.

### Trigger Syntax

**Structure:**
The syntax of a trigger definition differs from a class definition’s syntax. It starts with the `trigger` keyword, followed by the name of the trigger, the Salesforce object associated with the trigger, and the conditions under which it fires.

**Syntax:**
```apex
trigger TriggerName on ObjectName (trigger_events) {
   code_block
}
```

**Trigger Events:**
To execute a trigger before or after insert, update, delete, and undelete operations, specify multiple trigger events in a comma-separated list. The events you can specify are:

- `before insert`
- `before update`
- `before delete`
- `after insert`
- `after update`
- `after delete`
- `after undelete`

### Trigger Example: HelloWorldTrigger

**Description:**
This simple trigger fires before inserting an account and writes a message to the debug log.

**Steps to Implement:**
1. In the Developer Console, click File | New | Apex Trigger.
2. Enter `HelloWorldTrigger` for the trigger name, and then select `Account` for the sObject. Click Submit.
3. Replace the default code with the following:

```apex
trigger HelloWorldTrigger on Account (before insert) {
    System.debug('Hello World!');
}
```

**Testing the Trigger:**
1. Save the trigger by pressing Ctrl+S.
2. To test the trigger, create an account.
3. Click Debug | Open Execute Anonymous Window.
4. In the new window, add the following and then click Execute:

```apex
Account a = new Account(Name='Test Trigger');
insert a;
```

**Result:**
In the debug log, find the "Hello World!" statement. The log also shows that the trigger has been executed.

### Types of Triggers

**1. Before Triggers:**
   - **Purpose:** Before triggers are used to update or validate record values before they’re saved to the database.
   - **Functionality:** They allow modification or validation of data before it's inserted, updated, or deleted in the database.

**2. After Triggers:**
   - **Purpose:** After triggers are used to access field values that are set by the system (such as a record's Id or LastModifiedDate field), and to affect changes in other records.
   - **Functionality:** They operate on records that have been saved to the database. The records that fire the after trigger are read-only, but you can perform operations such as updating related records based on changes made to the triggering records.

### Using Context Variables in Triggers

**Accessing Records:**
To access the records that caused the trigger to fire, use context variables. For example, `Trigger.new` contains all the records that were inserted in insert or update triggers. `Trigger.old` provides the old version of sObjects before they were updated in update triggers, or a list of deleted sObjects in delete triggers.

**Iterating Over Records:**
Triggers can fire when one record is inserted, or when many records are inserted in bulk via the API or Apex. Therefore, context variables, such as `Trigger.new`, can contain only one record or multiple records. You can iterate over `Trigger.new` to get each individual sObject.

**Example:**
```apex
trigger HelloWorldTrigger on Account (before insert) {
    for(Account a : Trigger.new) {
        a.Description = 'New description';
    }   
}
```

**Note:**
The system saves the records that fired the before trigger after the trigger finishes execution. You can modify the records in the trigger without explicitly calling a DML insert or update operation. If you perform DML statements on those records, you get an error.

**Other Context Variables:**
- `Trigger.isExecuting`
- `Trigger.isInsert`
- `Trigger.isUpdate`
- `Trigger.isDelete`
- `Trigger.isBefore`
- `Trigger.isAfter`
- `Trigger.isUndelete`
- `Trigger.new`
- `Trigger.newMap`
- `Trigger.old`
- `Trigger.oldMap`
- `Trigger.operationType`
- `Trigger.size`

### Calling a Class Method from a Trigger

**Overview:**
You can call public utility methods from a trigger to enable code reuse, reduce trigger size, and improve maintenance of your Apex code. This allows you to leverage object-oriented programming principles.

**Example: EmailManager Class**

```apex
public class EmailManager {
    // Public method
    public static void sendMail(String address, String subject, String body) {
        // Create an email message object
        Messaging.SingleEmailMessage mail = new Messaging.SingleEmailMessage();
        String[] toAddresses = new String[] {address};
        mail.setToAddresses(toAddresses);
        mail.setSubject(subject);
        mail.setPlainTextBody(body);
        // Pass this email message to the built-in sendEmail method 
        // of the Messaging class
        Messaging.SendEmailResult[] results = Messaging.sendEmail(
                                  new Messaging.SingleEmailMessage[] { mail });
        // Call a helper method to inspect the returned results
        inspectResults(results);
    }
    // Helper method
    private static Boolean inspectResults(Messaging.SendEmailResult[] results) {
        Boolean sendResult = true;
        // sendEmail returns an array of result objects.
        // Iterate through the list to inspect results. 
        // In this class, the methods send only one email, 
        // so we should have only one result.
        for (Messaging.SendEmailResult res : results) {
            if (res.isSuccess()) {
                System.debug('Email sent successfully');
            }
            else {
                sendResult = false;
                System.debug('The following errors occurred: ' + res.getErrors());                 
            }
        }
        return sendResult;
    }
}
```

**Trigger Example: ExampleTrigger**

```apex
trigger ExampleTrigger on Contact (after insert, after delete) {
    if (Trigger.isInsert) {
        Integer recordCount = Trigger.new.size();
        // Call a utility method from another class
        EmailManager.sendMail('Your email address', 'Trailhead Trigger Tutorial', 
                    recordCount + ' contact(s) were inserted.');
    }
    else if (Trigger.isDelete) {
        // Process after delete
    }
}
```

**Testing the Trigger:**
1. Create a contact.
2. Execute the trigger.
3. Check the debug log for trigger firing and email confirmation.

With this trigger in place, you receive an email every time you add one or more contacts!

Got it! Let's condense the notes to reduce the use of lists:

### Notes:

#### Adding Related Records

- **Purpose**: Triggers manage and access records related to the trigger context—records that caused the trigger to fire.
  
- **Functionality**: 
  - This trigger adds a related opportunity for each new or updated account if no opportunity is already associated with it.
  - It performs a SOQL query to retrieve child opportunities for the accounts the trigger fired on.
  - Iterates over the list of sObjects in Trigger.new to access each account sObject.
  - If an account lacks related opportunity sObjects, it creates one within the loop.
  - Finally, it inserts any new opportunities created.

#### Trigger Implementation:

- **Developer Console**:
  - Add the trigger using the Developer Console.
  ```apex
	  trigger AddRelatedRecord on Account(after insert, after update) {
		    List<Opportunity> oppList = new List<Opportunity>();
		    // Get the related opportunities for the accounts in this trigger
		    Map<Id,Account> acctsWithOpps = new Map<Id,Account>(
		        [SELECT Id,(SELECT Id FROM Opportunities) FROM Account WHERE Id IN :Trigger.new]);
		    // Add an opportunity for each account if it doesn't already have one.
		    // Iterate through each account.
		    for(Account a : Trigger.new) {
		        System.debug('acctsWithOpps.get(a.Id).Opportunities.size()=' + acctsWithOpps.get(a.Id).Opportunities.size());
		        // Check if the account already has a related opportunity.
		        if (acctsWithOpps.get(a.Id).Opportunities.size() == 0) {
		            // If it doesn't, add a default opportunity
		            oppList.add(new Opportunity(Name=a.Name + ' Opportunity',
		                                       StageName='Prospecting',
		                                       CloseDate=System.today().addMonths(1),
		                                       AccountId=a.Id));
		        }           
		    }
		    if (oppList.size() > 0) {
		        insert oppList;
		    }
	  }
   ```

#### Testing Procedure:

- **Test Scenario**:
  - Create an account named "Apples & Oranges" in the Salesforce user interface.

- **Validation**:
  - In the Opportunities related list on the account’s page, observe the new opportunity added automatically by the trigger.

#### Efficiency Improvement:

- **Current Approach**:
  - The trigger iterates over all records in the trigger context using a for loop on Trigger.new.

- **Enhancement Opportunity**:
  - Optimize the loop to iterate only over accounts without opportunities, making the trigger more efficient.
  - Refer to the Bulk Trigger Design Patterns unit to learn how to modify the SOQL query to fetch only accounts with no opportunities and iterate over those records.

### Using Trigger Exceptions

#### Introduction:

- **Purpose**: Add restrictions on certain database operations, such as preventing records from being saved under certain conditions.
  
#### Method:

- **addError() Method**:
  - Call the addError() method on the sObject in question to prevent saving records in a trigger.
  - Throws a fatal error inside a trigger, displaying the error message in the user interface and logging it.

#### Example Trigger:

- **Account Deletion Trigger**:
  - Prevents the deletion of an account if it has related opportunities.
  - By default, deleting an account causes a cascade delete of all its related records.
  
```apex
trigger AccountDeletion on Account (before delete) {
    // Prevent the deletion of accounts if they have related opportunities.
    for (Account a : [SELECT Id FROM Account
                     WHERE Id IN (SELECT AccountId FROM Opportunity) AND
                     Id IN :Trigger.old]) {
        Trigger.oldMap.get(a.Id).addError(
            'Cannot delete account with related opportunities.');
    }
}
```

#### Test Scenario:

- **Execution**:
  - Navigate to the Apples & Oranges account’s page in the Salesforce user interface and click Delete.
  - In the confirmation popup, click OK.

- **Validation**:
  - Find the validation error with the custom error message "Cannot delete account with related opportunities."

#### Additional Steps:

- **Disabling the Trigger**:
  - Disable the AccountDeletion trigger to enable checking challenges.
  - From Setup, search for Apex Triggers.
  - On the Apex Triggers page, click Edit next to the AccountDeletion trigger.
  - Deselect Is Active.
  - Click Save.

#### Note:

- **Rollback Behavior**:
  - Calling addError() in a trigger causes the entire set of operations to roll back, except when bulk DML is called with partial success.
  - If a bulk DML call in the Lightning Platform API spawns the trigger, the runtime engine sets aside bad records and attempts a partial save of the records that did not generate errors.
  - If a DML statement in Apex spawns the trigger, any error rolls back the entire operation, but the runtime engine still processes every record in the operation to compile a comprehensive list of errors.

### Triggers and Callouts

#### Introduction:

- **Integration with External Web Services**:
  - Apex allows integration with external Web services through callouts.
  - Callouts are Apex calls to external Web services.

#### Callouts in Triggers:

- **Asynchronous Execution**:
  - Callouts from triggers must be asynchronous to prevent blocking the trigger process while waiting for the external service's response.
  - Asynchronous callouts are executed in a background process, and the response is received asynchronously.

#### Future Methods:

- **Definition**:
  - Methods that execute asynchronously are termed as future methods.
  - Annotated with @future(callout=true) to indicate that they make callouts.

#### Example Class:

```apex
public class CalloutClass {
    @future(callout=true)
    public static void makeCallout() {
        HttpRequest request = new HttpRequest();
        // Set the endpoint URL.
        String endpoint = 'http://yourHost/yourService';
        request.setEndPoint(endpoint);
        // Set the HTTP verb to GET.
        request.setMethod('GET');
        // Send the HTTP request and get the response.
        HttpResponse response = new HTTP().send(request);
    }
}
```

#### Trigger Implementation:

- **Trigger CalloutTrigger**:
  - Calls the method in CalloutClass to make a callout asynchronously.
  
```apex
trigger CalloutTrigger on Account (before insert, before update) {
    CalloutClass.makeCallout();
}
```

#### Note:

- **Endpoint URL and Remote Site**:
  - The provided example uses a hypothetical endpoint URL for illustration purposes only.
  - To execute the example, modify the endpoint URL to a valid one and add a remote site in Salesforce for the endpoint.

#### Additional Resources:

- **Further Information**:
  - For more detailed information on callouts, refer to "Invoking Callouts Using Apex" in the Apex Developer Guide.

### Bulk Trigger Design Patterns

#### Introduction:

- **Optimization for Bulk Operations**:
  - Apex triggers are optimized for bulk operations.
  - Utilizing bulk design patterns enhances trigger performance, reduces server resource consumption, and minimizes the risk of exceeding platform limits.

#### Importance of Bulkification:

- **Efficient Processing**:
  - Bulkified code efficiently processes large numbers of records and operates within governor limits on the Lightning Platform.
  - Governor limits prevent runaway code from monopolizing resources on the multitenant platform.

#### Bulk Design Patterns:

- **Operating on All Records**:
  - Process all records in the trigger context instead of individual records.
  - This approach improves efficiency and performance.

- **SOQL and DML Bulk Operations**:
  - Perform SOQL queries and DML operations on collections of sObjects rather than single sObjects at a time.
  - Best practices for SOQL and DML bulk operations apply to any Apex code, including classes.

#### Examples:

- **Trigger Context Variable**:
  - Examples provided are based on triggers and utilize the Trigger.new context variable.

#### Note:

- **General Applicability**:
  - While demonstrated within triggers, the bulk design patterns are applicable to any Apex code.
  - Utilizing these patterns ensures optimal performance and adherence to governor limits.

### Operating on Record Sets

#### Basic Bulk Design Concept:

- **Bulkified Triggers**:
  - Bulkified triggers operate on all sObjects in the trigger context.
  - Triggers typically operate on one record if the action originates from the user interface, but on a record set if the action is bulk DML or through the API.
  - It's a good practice to assume that triggers operate on a collection of records to ensure functionality in all scenarios.

#### Example Triggers:

- **Non-Bulkified Trigger (MyTriggerNotBulk)**:
  - Assumes that only one record caused the trigger to fire.
  - Doesn't work on a full record set when multiple records are inserted in the same transaction.

```apex
trigger MyTriggerNotBulk on Account (before insert) {
    Account a = Trigger.new[0];
    a.Description = 'New description';
}
```

- **Bulkified Trigger (MyTriggerBulk)**:
  - Modified version of MyTriggerNotBulk.
  - Uses a for loop to iterate over all available sObjects in Trigger.new.
  - Works effectively whether Trigger.new contains one sObject or many sObjects.

```apex
trigger MyTriggerBulk on Account (before insert) {
    for(Account a : Trigger.new) {
        a.Description = 'New description';
    }
}
```

#### Note:

- **Programming Practice**:
  - Always assume that triggers operate on a collection of records to ensure functionality in all circumstances.

### Performing Bulk SOQL

#### Introduction:

- **Efficient SOQL Queries**:
  - Utilizing SOQL features can reduce code complexity and database queries, improving performance and avoiding hitting query limits.

#### Inefficient Query Pattern:

- **Non-Bulkified Trigger (SoqlTriggerNotBulk)**:
  - Makes a SOQL query inside a for loop to retrieve related opportunities for each account.
  - This pattern results in multiple SOQL queries if the trigger operates on a large list of accounts, potentially hitting query limits.

```apex
trigger SoqlTriggerNotBulk on Account (after update) {   
    for(Account a : Trigger.new) {
        // Inefficient SOQL query as it runs once for each account!
        Opportunity[] opps = [SELECT Id, Name, CloseDate 
                             FROM Opportunity WHERE AccountId = :a.Id];
        // Do some other processing
    }
}
```

#### Recommended Approach:

- **Bulkified Trigger (SoqlTriggerBulk)**:
  - Uses a single SOQL query outside the loop to retrieve accounts and their related opportunities efficiently.
  - Enhances performance and reduces the number of queries.

```apex
trigger SoqlTriggerBulk on Account (after update) {  
    // Perform SOQL query once.    
    // Get the accounts and their related opportunities.
    List<Account> acctsWithOpps = 
        [SELECT Id, (SELECT Id, Name, CloseDate FROM Opportunities) 
         FROM Account WHERE Id IN :Trigger.new];
    // Iterate over the returned accounts    
    for(Account a : acctsWithOpps) { 
        Opportunity[] relatedOpps = a.Opportunities;  
        // Do some other processing
    }
}
```

#### Alternative Query:

- **Retrieving Only Opportunities**:
  - If only opportunities are needed, the query can be simplified to retrieve related opportunities directly.

```apex
trigger SoqlTriggerBulk on Account (after update) {  
    // Perform SOQL query once.    
    // Get the related opportunities for the accounts in this trigger.
    List<Opportunity> relatedOpps = [SELECT Id, Name, CloseDate FROM Opportunity
    WHERE AccountId IN :Trigger.new];
// Iterate over the related opportunities    
for(Opportunity opp : relatedOpps) { 
    // Do some other processing
}
```

#### Combined Query and Loop:

- **SOQL For Loop**:
  - Combines the SOQL query and the for loop into one statement for conciseness.

```apex
trigger SoqlTriggerBulk on Account (after update) {  
    // Perform SOQL query once.    
    // Get the related opportunities for the accounts in this trigger,
    // and iterate over those records.
    for(Opportunity opp : [SELECT Id, Name, CloseDate FROM Opportunity
        WHERE AccountId IN :Trigger.new]) {
        // Do some other processing
    }
}
```

#### Note:

- **Batch Processing**:
  - Triggers execute on batches of 200 records at a time.
  - The SOQL for loop may be called twice if the trigger operates on more than 200 records, but this is a limitation of trigger batching, not the SOQL for loop.
  - Despite the potential duplicate execution, the SOQL for loop provides more elegant code compared to iterating over a collection variable.


### Performing Bulk DML

#### Introduction:

- **Efficient DML Operations**:
  - Optimize DML calls by performing them on collections of sObjects whenever possible.
  - The Apex runtime allows up to 150 DML calls in one transaction.

#### Inefficient DML Pattern:

- **Non-Bulkified Trigger (DmlTriggerNotBulk)**:
  - Updates each related opportunity individually inside a for loop.
  - Inefficient usage of resources, potentially exceeding DML limits if operating on a large number of records.

```apex
trigger DmlTriggerNotBulk on Account(after update) {   
    // Get the related opportunities for the accounts in this trigger.        
    List<Opportunity> relatedOpps = [SELECT Id,Name,Probability FROM Opportunity
        WHERE AccountId IN :Trigger.new];          
    // Iterate over the related opportunities
    for(Opportunity opp : relatedOpps) {      
        // Update the description when probability is greater 
        // than 50% but less than 100% 
        if ((opp.Probability >= 50) && (opp.Probability < 100)) {
            opp.Description = 'New description for opportunity.';
            // Update once for each opportunity -- not efficient!
            update opp;
        }
    }    
}
```

- **Bulkified Trigger (DmlTriggerBulk)**:
  - shows how to perform DML in bulk efficiently with only one DML call on a list of opportunities
  - the trigger performs the DML call outside the loop on this list after all opportunities have been added to the list.
  - This pattern uses only one DML call regardless of the number of sObjects being updated.

```apex
trigger DmlTriggerBulk on Account(after update) {   
    // Get the related opportunities for the accounts in this trigger.        
    List<Opportunity> relatedOpps = [SELECT Id,Name,Probability FROM Opportunity
        WHERE AccountId IN :Trigger.new];
    List<Opportunity> oppsToUpdate = new List<Opportunity>();
    // Iterate over the related opportunities
    for(Opportunity opp : relatedOpps) {      
        // Update the description when probability is greater 
        // than 50% but less than 100% 
        if ((opp.Probability >= 50) && (opp.Probability < 100)) {
            opp.Description = 'New description for opportunity.';
            oppsToUpdate.add(opp);
        }
    }
    // Perform DML on a collection
    update oppsToUpdate;
}
```

**Bulk Design Pattern in Action: Example of Getting Related Records**

**Objective:**  
Write a trigger to access accounts' related opportunities efficiently.

**Requirements for the AddRelatedRecord Trigger:**
- Trigger fires after accounts are inserted or updated.
- Adds a default opportunity for every account that doesn’t already have one.

**Approach:**
1. **Efficient Processing:**
   - Modify the trigger code and SOQL query to fetch only relevant records.
   - Iterate over these records for efficient processing.

2. **Handling Inserts and Updates:**
   - For newly-inserted accounts, add a default opportunity.
   - For updated accounts, determine if they have a related opportunity.

3. **Implementation:**
   - Use a switch statement on `Trigger.operationType` to differentiate between inserts and updates.
   - Maintain a `toProcess` variable to track accounts needing processing.

**Example Implementation:**
- Modify existing trigger code for `AddRelatedRecord`.
- Use switch statement to handle inserts and updates separately.
- Add necessary logic to determine whether an account already has a related opportunity.

**Note:**  
If the `AddRelatedRecord` trigger hasn't been created yet, it can be developed following this section.

```apex
trigger AddRelatedRecord on Account(after insert, after update) {
    List<Opportunity> oppList = new List<Opportunity>();
    // Add an opportunity for each account if it doesn't already have one.
    // Iterate over accounts that are in this trigger but that don't have opportunities.
    List<Account> toProcess = null;
    switch on Trigger.operationType {
        when AFTER_INSERT {
        // All inserted Accounts will need the Opportunity, so there is no need to perform the query
            toProcess = Trigger.New;
        }
        when AFTER_UPDATE {
            toProcess = [SELECT Id,Name FROM Account
                         WHERE Id IN :Trigger.New AND
                         Id NOT IN (SELECT AccountId FROM Opportunity WHERE AccountId in :Trigger.New)];
        }
    }
    for (Account a : toProcess) {
        // Add a default opportunity for this account
        oppList.add(new Opportunity(Name=a.Name + ' Opportunity',
                                    StageName='Prospecting',
                                    CloseDate=System.today().addMonths(1),
                                    AccountId=a.Id));
    }
    if (oppList.size() > 0) {
        insert oppList;
    }
}
```

# Asynchronous Apex

## Notes on "Asynchronous Processing Basics"

**Asynchronous Processing in Salesforce**

**Scenario:**
Before your weekly Dance Revolution practice, you have multiple tasks to accomplish: your car needs fixing, you require a different color hair gel, and you must retrieve your uniform from your mom’s house. You can either wait for your car to be fixed before completing the rest of your tasks (synchronous processing) or leave it at the mechanic, handle other tasks, and have them notify you when it's done (asynchronous processing). Asynchronous processing enables you to accomplish more within the same timeframe, without unnecessary waiting.

**Key Benefits:**
1. **User Efficiency:**
   - Users can continue their work while background processes handle time-consuming tasks, improving productivity.
   - For example, complex calculations on custom objects triggered by Opportunity creation won't disrupt user workflow with asynchronous processing.

2. **Scalability:**
   - Asynchronous processing allows the platform to manage and scale resources efficiently, handling multiple jobs concurrently.
   - It facilitates parallel processing, enhancing platform scalability.

3. **Higher Limits:**
   - Asynchronous processes operate in separate threads with elevated governor and execution limits, enhancing performance.
   - Higher limits are desirable for resource-intensive operations.

**Types of Asynchronous Apex:**

| Type           | Overview                                                       | Common Scenarios                                        |
|----------------|----------------------------------------------------------------|---------------------------------------------------------|
| Future Methods | Execute in their own thread, starting when resources are free.  | Web service callouts.                                   |
| Batch Apex     | Handle large jobs exceeding normal processing limits.           | Data cleansing, record archiving.                       |
| Queueable Apex | Similar to future methods, with job chaining and complex data.  | Sequential processing with external web services.       |
| Scheduled Apex | Scheduled to run at specified times (daily/weekly).             | Scheduled tasks, recurring processes.                   |

**Note:**
- Different asynchronous operations are not mutually exclusive.
- Example: Triggering a Batch Apex job from a Scheduled Apex job is a common practice.

**Increased Governor and Execution Limits**

- Asynchronous Apex offers higher governor and execution limits compared to synchronous requests.
- For instance, the number of SOQL queries doubles from 100 to 200 in asynchronous calls, and similar increases occur in total heap size and maximum CPU time.
- These limits are independent of the synchronous request that queued the async request, providing more processing capability.
- Useful when nearing governor limits in the current transaction, allowing continued processing asynchronously.

**How Asynchronous Processing Works**

1. **Challenges:**
   - Ensure fairness of processing across customers.
   - Guarantee fault tolerance to prevent loss of asynchronous requests.

2. **Queue-Based Framework:**
   - Requests are managed through a queue-based framework.
   - Lifecycle:
     - **Enqueue:** Requests are placed into the queue with appropriate data.
     - **Persistence:** Enqueued requests are stored for failure recovery and transactional support.
     - **Dequeue:** Requests are removed from the queue and processed, with transaction control ensuring no requests are lost if processing fails.

3. **Handler Execution:**
   - Each request is processed by a handler, which performs functions specific to the request type.
   - Handlers run on finite worker threads on application servers within an instance.
   - Threads request work from the queuing framework and execute specific handlers upon receiving it.

4. **Resource Conservation:**
   - Asynchronous processing has lower priority compared to real-time interaction.
   - The queuing framework monitors system resources like server memory and CPU usage.
   - It reduces asynchronous processing if resource thresholds are exceeded, ensuring fair resource allocation.
   - No guarantee on processing time, but the system self-regulates to maintain equilibrium.

**Note:**
- For detailed information on heap sizes, maximum execution times, and limits, refer to "Execution Governors and Limits".

## Notes on "User Future Methods"
### **Future Apex**

**Overview:**
- Future Apex runs processes in a separate thread, deferring execution until system resources are available.
- Identified by the `@future` annotation, commonly known as "future methods."

**Usage:**
- Future methods allow asynchronous execution, freeing up the main thread for other operations.
- They offer higher governor and execution limits, enhancing performance.

**Common Use Cases:**
1. **Callouts to External Web Services:**
   - Essential when making callouts from triggers or after DML operations.
   - Prevents holding the database connection open, crucial in a multitenant environment.

2. **Resource-Intensive Operations:**
   - Suitable for tasks requiring significant processing power or calculations.
   - Allows running operations in their own thread when system resources permit.

3. **Isolating DML Operations:**
   - Prevents mixed DML errors by executing DML operations on different sObject types separately.
   - Addresses edge cases to ensure data integrity.

**Note:**
- Future methods provide a flexible approach to handle various asynchronous operations efficiently.
- Reference "sObjects That Cannot Be Used Together in DML Operations" for detailed information on isolating DML operations.

### **Future Method Syntax**

- **Type:** Static methods.
- **Return Type:** Void.
- **Parameters:** Limited to primitive data types, arrays of primitives, or collections of primitives.
- **Notable Restriction:** Cannot accept standard or custom objects as arguments.
- **Common Practice:** Passing a List of record IDs for asynchronous processing.

**Note:**
- Passing objects as arguments is prohibited because object values can change between method call and execution, leading to potential errors.
- Future methods execute when system resources become available, potentially resulting in outdated object values.

```apex
public class SomeClass {
  @future
  public static void someFutureMethod(List<Id> recordIds) {
    List<Account> accounts = [Select Id, Name from Account Where Id IN :recordIds];
    // process account records to do awesome stuff
  }
}
```

### **Sample Callout Code**

To perform a Web service callout to an external service or API, you can create an Apex class with a future method marked with `(callout=true)`. Below is an example class with methods for making callouts synchronously and asynchronously where callouts aren't allowed. Additionally, a record is inserted into a custom log object (`SMS_Log__c`) to track the callout status.

**Note:**
- The `SmsMessage` class and `SMS_Log__c` sObject in the example are hypothetical and used for demonstrating the callout pattern.

```java
public class SMSUtils {
    // Call async from triggers, etc, where callouts are not permitted.
    @future(callout=true)
    public static void sendSMSAsync(String fromNbr, String toNbr, String m) {
        String results = sendSMS(fromNbr, toNbr, m);
        System.debug(results);
    }
    // Call from controllers, etc, for immediate processing
    public static String sendSMS(String fromNbr, String toNbr, String m) {
        // Calling 'send' will result in a callout
        String results = SmsMessage.send(fromNbr, toNbr, m);
        insert new SMS_Log__c(to__c=toNbr, from__c=fromNbr, msg__c=results);
        return results;
    }
}
```

**Note:**
- The `sendSmsSync` method performs the callout synchronously.
- The `sendSmsAsync` method is marked as a future method for asynchronous callouts.

### **Test Classes**

Testing future methods in Apex follows a slightly different approach compared to typical testing. To test future methods effectively:

1. **Enclose Test Code:**
   - Wrap your test code between the `startTest()` and `stopTest()` test methods.
   - The system gathers all asynchronous calls made after `startTest()`.
   - When `stopTest()` is executed, these collected asynchronous processes run synchronously.

2. **Assert Results:**
   - After `stopTest()`, you can assert that the asynchronous call operated correctly.

**Note:**
- Test code cannot send actual callouts to external systems. You need to 'mock' the callout for test coverage.
- Refer to the Apex Integration Services module for comprehensive details on mocking callouts for testing.

```apex
@isTest
public class SMSCalloutMock implements HttpCalloutMock {
    public HttpResponse respond(HttpRequest req) {
        // Create a fake response
        HttpResponse res = new HttpResponse();
        res.setHeader('Content-Type', 'application/json');
        res.setBody('{"status":"success"}');
        res.setStatusCode(200);
        return res;
    }
}
```

### **Best Practices for Using Future Methods**

1. **Avoid Large Number of Future Requests:**
   - Each future method invocation adds a request to the asynchronous queue.
   - Design patterns adding 2000+ requests over a short period may cause delays due to flow control.
   - Ensure designs don't overwhelm the asynchronous queue.

2. **Ensure Fast Execution:**
   - Future methods should execute as quickly as possible to avoid delays.

3. **Bundle Web Service Callouts:**
   - If making Web service callouts, bundle them together in the same future method.
   - Avoid using separate future methods for each callout.

4. **Thorough Testing at Scale:**
   - Test trigger enqueuing @future calls to handle 200-record collections.
   - Determine potential delays with current and future volumes.

5. **Consider Batch Apex:**
   - Use Batch Apex for processing large numbers of records asynchronously.
   - More efficient than creating individual future requests for each record.

### **Things to Remember**

1. **Static Methods with Void Return Type:**
   - Methods with `@future` annotation must be static and return `void`.

2. **Primitive Data Type Parameters Only:**
   - Parameters limited to primitive data types, arrays, or collections of primitives.
   - Objects cannot be passed as arguments.

3. **Non-Deterministic Execution:**
   - Future methods may not execute in the same order they're called.
   - Concurrent execution of future methods can lead to record locking.

4. **Restrictions in Visualforce:**
   - Cannot use future methods in Visualforce controllers' `getMethodName()`, `setMethodName()`, or constructor.

5. **No Nested Future Calls:**
   - Cannot call a future method from within another future method.
   - Trigger invoking a future method cannot be called within a future method.

6. **Limitations on Certain Methods:**
   - `getContent()` and `getContentAsPDF()` methods cannot be used in future methods.

7. **Limits on Future Calls:**
   - Limited to 50 future calls per Apex invocation, with additional limits within a 24-hour period.

## Notes on "Use Batch Apex"

### **Batch Apex**

**Overview:**
- Batch Apex is utilized for processing large jobs that exceed normal processing limits, such as thousands or millions of records.
- It allows processing records asynchronously in batches, staying within platform limits.
- Common use cases include data cleansing or archiving.

**Functionality:**
- Each time a batch class is invoked, the job is placed on the Apex job queue and executed as a discrete transaction.
- Execution logic of the batch class is called once for each batch of records being processed.
- Advantages:
  1. Each transaction starts with new governor limits, ensuring code stays within execution limits.
  2. If one batch fails, other successful batch transactions aren't rolled back.

### **Batch Apex Syntax:**
- To write a Batch Apex class:
  - Implement the `Database.Batchable` interface.
  - Include the following methods:
    1. **start:**
       - Collects records or objects to be processed.
       - Called once at the beginning of a Batch Apex job.
       - Returns either a `Database.QueryLocator` object or an `Iterable` containing records or objects.
    2. **execute:**
       - Performs actual processing for each batch of data.
       - Default batch size is 200 records.
       - Batches aren't guaranteed to execute in the order they're received from the start method.
    3. **finish:**
       - Executes post-processing operations after all batches are processed.

### **Invoking a Batch Class**

To invoke a batch class, follow these steps:

1. **Instantiate Batch Class:**
   - Create an instance of the batch class.

```java
MyBatchClass myBatchObject = new MyBatchClass();
```

2. **Execute Batch:**
   - Call `Database.executeBatch` with the batch class instance.

```java
Id batchId = Database.executeBatch(myBatchObject);
```

3. **Optional: Specify Batch Size:**
   - Optionally, specify the number of records passed into the `execute` method for each batch.

```java
Id batchId = Database.executeBatch(myBatchObject, 100);
```

**Note:**
- Limiting batch size can help avoid hitting governor limits.

**Tracking Job Progress:**
- Each batch Apex invocation creates an `AsyncApexJob` record, allowing tracking of job progress.
- Progress can be viewed via SOQL queries or managed in the Apex Job Queue.

**Example Query:**

```java
AsyncApexJob job = [SELECT Id, Status, JobItemsProcessed, TotalJobItems, NumberOfErrors FROM AsyncApexJob WHERE ID = :batchId ];
```

**Using State in Batch Apex:**
- Batch Apex classes can maintain state across transactions using instance member variables.
- State can be helpful for passing information between batch job iterations.
**Note:**
- `QueryLocator` bypasses the governor limit for total records retrieved by SOQL queries, allowing querying up to 50 million records.
- Governor limit for total records retrieved by SOQL queries is still enforced with an `Iterable`.
- For complex scenarios, custom iterators can be explored.

```apex
public class MyBatchClass implements Database.Batchable<sObject> {
    public (Database.QueryLocator | Iterable<sObject>) start(Database.BatchableContext bc) {
        // collect the batches of records or objects to be passed to execute
    }
    public void execute(Database.BatchableContext bc, List<P> records){
        // process each batch of records
    }
    public void finish(Database.BatchableContext bc){
        // execute any post-processing operations
    }
}
```

### **Invoking a Batch Class**

To invoke a batch class, follow these steps:

1. **Instantiate Batch Class:**
   - Create an instance of the batch class.

```java
MyBatchClass myBatchObject = new MyBatchClass();
```

2. **Execute Batch:**
   - Call `Database.executeBatch` with the batch class instance.

```java
Id batchId = Database.executeBatch(myBatchObject);
```

3. **Optional: Specify Batch Size:**
   - Optionally, specify the number of records passed into the `execute` method for each batch.

```java
Id batchId = Database.executeBatch(myBatchObject, 100);
```

**Note:**
- Limiting batch size can help avoid hitting governor limits.

**Tracking Job Progress:**
- Each batch Apex invocation creates an `AsyncApexJob` record, allowing tracking of job progress.
- Progress can be viewed via SOQL queries or managed in the Apex Job Queue.

**Example Query:**

```java
AsyncApexJob job = [SELECT Id, Status, JobItemsProcessed, TotalJobItems, NumberOfErrors FROM AsyncApexJob WHERE ID = :batchId ];
```

### **Using State in Batch Apex**

- By default, Batch Apex is stateless, treating each execution as a discrete transaction.
- Each batch Apex job execution is considered separate transactions, even if processing the same set of records.
- However, you can maintain state across all transactions by using `Database.Stateful` in the class definition.
- With `Database.Stateful`, only instance member variables retain their values between transactions.
- Maintaining state is useful for tasks like counting or summarizing records as they're processed.

### **Sample Batch Apex Code**

**Note:**
- Finds all account records that are passed in by the start() method using a QueryLocator and updates the associated contacts with their account’s mailing address.
- It sends off an email with the results of the bulk job and, since we are using Database.Stateful to track state, the number of records updated.
- The start() method provides the collection of all records that the execute() method will process in individual batches. It returns the list of records to be processed by calling Database.getQueryLocator with a SOQL query. In this case we are simply querying for all Account records with a Billing Country of ‘USA’.
- Each batch of 200 records is passed in the second parameter of the execute() method. The execute() method sets each contact’s mailing address to the accounts’ billing address and increments recordsProcessed to track the number of records processed.
- When the job is complete, the finish method performs a query on the AsyncApexJob object (a table that lists information about batch jobs) to get the status of the job, the submitter’s email address, and some other information. It then sends a notification email to the job submitter that includes the job info and number of contacts updated.

```apex
public class UpdateContactAddresses implements
    Database.Batchable<sObject>, Database.Stateful {
    // instance member to retain state across transactions
    public Integer recordsProcessed = 0;
    public Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator(
            'SELECT ID, BillingStreet, BillingCity, BillingState, ' +
            'BillingPostalCode, (SELECT ID, MailingStreet, MailingCity, ' +
            'MailingState, MailingPostalCode FROM Contacts) FROM Account ' +
            'Where BillingCountry = \'USA\''
        );
    }
    public void execute(Database.BatchableContext bc, List<Account> scope){
        // process each batch of records
        List<Contact> contacts = new List<Contact>();
        for (Account account : scope) {
            for (Contact contact : account.contacts) {
                contact.MailingStreet = account.BillingStreet;
                contact.MailingCity = account.BillingCity;
                contact.MailingState = account.BillingState;
                contact.MailingPostalCode = account.BillingPostalCode;
                // add contact to list to be updated
                contacts.add(contact);
                // increment the instance member counter
                recordsProcessed = recordsProcessed + 1;
            }
        }
        update contacts;
    }
    public void finish(Database.BatchableContext bc){
        System.debug(recordsProcessed + ' records processed. Shazam!');
        AsyncApexJob job = [SELECT Id, Status, NumberOfErrors,
            JobItemsProcessed,
            TotalJobItems, CreatedBy.Email
            FROM AsyncApexJob
            WHERE Id = :bc.getJobId()];
        // call some utility to send email
        EmailUtils.sendMessage(job, recordsProcessed);
    }
}
```

### **Testing Batch Apex**

**Note:**
- Insert some records
- Call the Batch Apex class
- Assert that the records were updated properly with the correct address
- Make sure that the number of records inserted is less than or equal to the batch size of 200 because test methods can execute only one batch.
- Ensure that the Iterable returned by the start() method matches the batch size.

```apex
@isTest
private class UpdateContactAddressesTest {
    @testSetup
    static void setup() {
        List<Account> accounts = new List<Account>();
        List<Contact> contacts = new List<Contact>();
        // insert 10 accounts
        for (Integer i=0;i<10;i++) {
            accounts.add(new Account(name='Account '+i,
                billingcity='New York', billingcountry='USA'));
        }
        insert accounts;
        // find the account just inserted. add contact for each
        for (Account account : [select id from account]) {
            contacts.add(new Contact(firstname='first',
                lastname='last', accountId=account.id));
        }
        insert contacts;
    }
    @isTest static void test() {
        Test.startTest();
        UpdateContactAddresses uca = new UpdateContactAddresses();
        Id batchId = Database.executeBatch(uca);
        Test.stopTest();
        // after the testing stops, assert records were updated properly
        System.assertEquals(10, [select count() from contact where MailingCity = 'New York']);
    }
}
```

### **Best Practices for Batch Apex**

When utilizing Batch Apex, it's crucial to adhere to best practices to ensure efficient and effective execution. Here are some key points to keep in mind:

1. **Suitability of Batch Apex:**
   - Use Batch Apex only when processing multiple batches of records.
   - If you have insufficient records to generate multiple batches, consider Queueable Apex instead.

2. **Optimize SOQL Queries:**
   - Tune your SOQL queries to retrieve records quickly and efficiently.
   - Optimize query performance to minimize execution time.

3. **Minimize Asynchronous Requests:**
   - Reduce the number of asynchronous requests created to minimize delays.
   - Be mindful of resource consumption and governor limits.

4. **Caution with Trigger Invocations:**
   - Exercise caution when invoking a batch job from a trigger.
   - Ensure that trigger actions won't exceed the batch job limit.
   - Guarantee that trigger actions won't overwhelm the system with batch job requests.

By following these best practices, you can maximize the efficiency and effectiveness of your Batch Apex jobs while minimizing potential issues and delays.

## Notes on "Control Processes with Queueable Apex"

### **Queueable Apex**

Queueable Apex, introduced in Winter '15, combines the simplicity of future methods with the robustness of Batch Apex. It offers a streamlined class structure, non-primitive argument support, and monitoring capabilities, making it a versatile tool for asynchronous processing.

**Key Features:**

1. **Class Structure:**
   - Queueable Apex provides a class structure that the platform serializes for execution.
   - This structure eliminates the need for start and finish methods, simplifying development.

2. **Non-Primitive Types:**
   - Queueable classes can include member variables of non-primitive data types, such as sObjects or custom Apex types.
   - These objects remain accessible during job execution, enhancing flexibility in processing.

3. **Monitoring:**
   - Upon submitting a job using `System.enqueueJob()`, the method returns the ID of the `AsyncApexJob` record.
   - This ID enables job identification and progress monitoring through the Salesforce UI or programmatically via queries.

4. **Chaining Jobs:**
   - Queueable Apex supports chaining, allowing one job to trigger another job during execution.
   - Chaining is beneficial for sequential processing scenarios, enhancing job orchestration and flexibility.

**Benefits:**
- **Flexibility:** Queueable Apex supports complex data types and chaining, accommodating diverse processing requirements.
- **Visibility:** Monitoring capabilities provide insights into job progress, facilitating troubleshooting and optimization.
- **Simplicity:** The streamlined class structure simplifies development and maintenance, improving code readability and efficiency.

Queueable Apex stands as a versatile and powerful tool for asynchronous processing in Salesforce, offering developers enhanced capabilities and flexibility for handling complex business requirements.

### **Queueable vs. Future Methods**

**Queueable Apex:**
- Functionally equivalent to future methods but offers additional features and flexibility.
- Provides a streamlined class structure and supports non-primitive data types.
- Allows chaining of jobs for sequential processing.
- Ideal for most asynchronous processing needs due to its versatility and monitoring capabilities.

**Future Methods:**
- Simple and straightforward for basic asynchronous processing.
- May be preferable in scenarios where functionality is sometimes executed synchronously and sometimes asynchronously.
- Easy to refactor from synchronous to asynchronous execution when needed.

**Considerations:**
- **Refactoring Existing Code:**
  - It may not be necessary to refactor all future methods to queueable immediately.
  - Refactoring to queueable may offer additional benefits but requires more effort.

- **Synchronous and Asynchronous Execution:**
  - Future methods are suitable when functionality needs to be executed synchronously or asynchronously based on requirements.
  - They offer flexibility in transitioning between synchronous and asynchronous execution.

**Conclusion:**
- While queueable methods offer more features and flexibility, including monitoring and chaining, future methods remain valuable, especially in scenarios where synchronous and asynchronous execution coexist.
- Consider the specific requirements of your application when choosing between queueable and future methods, and refactor existing code as needed to align with your long-term goals and performance considerations.

### **Queueable Syntax**

**Note:**
- To use Queueable Apex, simply implement the Queueable interface.

```apex
public class SomeClass implements Queueable {
    public void execute(QueueableContext context) {
        // awesome code here
    }
}
```
```apex
public class UpdateParentAccount implements Queueable {
    private List<Account> accounts;
    private ID parent;
    public UpdateParentAccount(List<Account> records, ID id) {
        this.accounts = records;
        this.parent = id;
    }
    public void execute(QueueableContext context) {
        for (Account account : accounts) {
          account.parentId = parent;
          // perform other processing or callout
        }
        update accounts;
    }
}
```

### **Testing Queueable Apex**

Testing Queueable apex is very similar to testing batch apex testing.

```apex
@isTest
public class UpdateParentAccountTest {
    @testSetup
    static void setup() {
        List<Account> accounts = new List<Account>();
        // add a parent account
        accounts.add(new Account(name='Parent'));
        // add 100 child accounts
        for (Integer i = 0; i < 100; i++) {
            accounts.add(new Account(
                name='Test Account'+i
            ));
        }
        insert accounts;
    }
    static testmethod void testQueueable() {
        // query for test data to pass to queueable class
        Id parentId = [select id from account where name = 'Parent'][0].Id;
        List<Account> accounts = [select id, name from account where name like 'Test Account%'];
        // Create our Queueable instance
        UpdateParentAccount updater = new UpdateParentAccount(accounts, parentId);
        // startTest/stopTest block to force async processes to run
        Test.startTest();
        System.enqueueJob(updater);
        Test.stopTest();
        // Validate the job ran. Check if record have correct parentId now
        System.assertEquals(100, [select count() from account where parentId = :parentId]);
    }
}
```

### **Chaining Jobs**
- Queuable Apex allows for job chaining that allows jobs to be run sequentially. In order to chain a job to another one, simply submit the second job in the `execute()` method of the first job.

```apex
public class FirstJob implements Queueable {
    public void execute(QueueableContext context) {
        // Awesome processing logic here
        // Chain this job to next job by submitting the next job
        System.enqueueJob(new SecondJob());
    }
}
```

### **Testing Chained Jobs**
- Has a slightly different pattern.
- can’t chain queueable jobs in an Apex test and will result in an error
-  To avoid nasty errors, you can check if Apex is running in test context by calling Test.isRunningTest() before chaining jobs.

### **Things to Remember with Queueable Apex:**

1. **Shared Limit:**
   - The execution of a queued job counts once against the shared limit for asynchronous Apex method executions.
  
2. **Limitations on Queueable Jobs:**
   - You can add up to 50 jobs to the queue with `System.enqueueJob` in a single transaction.

3. **Job Chaining:**
   - When chaining jobs, you can add only one job from an executing job with `System.enqueueJob`, meaning that only one child job can exist for each parent queueable job.
   - Starting multiple child jobs from the same queueable job is not permitted.

4. **Depth of Chained Jobs:**
   - There is no limit enforced on the depth of chained jobs, allowing you to chain one job to another indefinitely.
   - However, for Developer Edition and Trial orgs, the maximum stack depth for chained jobs is 5. This means you can chain jobs four times, and the maximum number of jobs in the chain is 5, including the initial parent queueable job.

### **Conclusion:**
Queueable Apex offers powerful capabilities for asynchronous processing, but it's essential to be mindful of its limitations and considerations to ensure efficient and effective job execution. Understanding these aspects will help you leverage Queueable Apex effectively in your Salesforce development.

## Notes on "Schedule Jobs Using the Apex Scheduler"

### **Scheduled Apex**

Scheduled Apex allows you to defer the execution of Apex classes to specific times, making it ideal for automating daily or weekly maintenance tasks using Batch Apex. Here's how you can implement and schedule Scheduled Apex:

### **Scheduled Apex Syntax:**
```java
public class SomeClass implements Schedulable {
    public void execute(SchedulableContext ctx) {
        // Your logic here
    }
}
```

**Explanation:**
- Implement the `Schedulable` interface in your Apex class.
- Define the `execute` method within the class. This method contains the logic that you want to execute at the scheduled time.
- The `execute` method accepts a `SchedulableContext` parameter, allowing you to access context information about the scheduled job.

### **Scheduling:**
To schedule the execution of your Apex class, you'll use the `System.schedule()` method. Here's an example of how to schedule the `SomeClass` for execution:

```java
String jobId = System.schedule('Scheduled Job Name', '0 0 0 * * ?', new SomeClass());
```

- The first parameter is the name of the scheduled job.
- The second parameter is a cron expression specifying when the job should run. In this example, `0 0 0 * * ?` represents midnight every day.
- The third parameter is an instance of your Apex class.

### **Sample Code**
This class queries for open opportunities that should have closed by the current date, and creates a task on each one to remind the owner to update the opportunity.

```apex
public class RemindOpptyOwners implements Schedulable {
    public void execute(SchedulableContext ctx) {
        List<Opportunity> opptys = [SELECT Id, Name, OwnerId, CloseDate
            FROM Opportunity
            WHERE IsClosed = False AND
            CloseDate < TODAY];
        // Create a task for each opportunity in the list
        TaskUtils.remindOwners(opptys);
    }
}
```

### **Using the System.Schedule Method**

After implementing a class with the `Schedulable` interface, you can use the `System.schedule()` method to execute it. Here's how to use the `System.schedule()` method effectively:

1. **Method Overview:**
   - `System.schedule()` schedules the execution of a class implementing the `Schedulable` interface.

2. **Method Signature:**
   ```java
   String jobID = System.schedule(String jobName, String cronExpression, Schedulable schedulableInstance);
   ```
   - `jobName`: A unique name for the scheduled job.
   - `cronExpression`: A CRON expression representing the time and date the job is scheduled to run.
   - `schedulableInstance`: An instance of a class implementing the `Schedulable` interface.

3. **Usage Example:**
   ```java
   RemindOpptyOwners reminder = new RemindOpptyOwners();
   String cronExpression = '20 30 8 10 2 ?'; // Seconds Minutes Hours Day_of_month Month Day_of_week optional_year
   String jobID = System.schedule('Remind Opp Owners', cronExpression, reminder);
   ```

4. **CRON Expression:**
   - The CRON expression defines the schedule for the job. It specifies when the job should run in terms of seconds, minutes, hours, days, months, and days of the week.
   - For more information on CRON expressions, refer to the "Using the System.Schedule Method" section in the Apex Scheduler documentation.

### **Scheduling a Job from the UI:**
You can also schedule a class using the Salesforce user interface:
1. Navigate to Setup and enter "Apex" in the Quick Find box.
2. Select "Apex Classes" and click on "Schedule Apex".
3. Enter a job name and select the desired Apex class.
4. Choose the frequency (weekly or monthly) and set the start and end dates, along with the preferred start time.
5. Click "Save".

**Note:**
- Be cautious when scheduling a class from a trigger to avoid exceeding the limit on scheduled job classes.
- Ensure proper permissions for executing the scheduled class, as it runs in system mode.

### **Testing Scheduled Apex**
- Just like with the other async methods we’ve covered so far, with Scheduled Apex you must also ensure that the scheduled job is finished before testing against the results.
- To do this, use startTest() and stopTest() again around the System.schedule() method, to ensure processing finishes before continuing your test.

```apex
@IsTest
private class RemindOppyOwnersTest {
    // Dummy CRON expression: midnight on March 15.
    // Because this is a test, job executes
    // immediately after Test.stopTest().
    public static String CRON_EXP = '0 0 0 15 3 ? 2042';
    @IsTest
    static void testScheduledJob() {
        // Create some out of date Opportunity records
        List<Opportunity> opptys = new List<Opportunity>();
        Date closeDate = Date.today().addDays(-7);
        for (Integer i=0; i<10; i++) {
            Opportunity o = new Opportunity(
                Name = 'Opportunity ' + i,
                CloseDate = closeDate,
                StageName = 'Prospecting'
            );
            opptys.add(o);
        }
        insert opptys;
        // Get the IDs of the opportunities we just inserted
        Map<Id, Opportunity> opptyMap = new Map<Id, Opportunity>(opptys);
        List<Id> opptyIds = new List<Id>(opptyMap.keySet());
        Test.startTest();
        // Schedule the test job
        String jobId = System.schedule('ScheduledApexTest',
            CRON_EXP,
            new RemindOpptyOwners());
        // Verify the scheduled job has not run yet.
        List<Task> lt = [SELECT Id
            FROM Task
            WHERE WhatId IN :opptyIds];
        System.assertEquals(0, lt.size(), 'Tasks exist before job has run');
        // Stopping the test will run the job synchronously
        Test.stopTest();
        // Now that the scheduled job has executed,
        // check that our tasks were created
        lt = [SELECT Id
            FROM Task
            WHERE WhatId IN :opptyIds];
        System.assertEquals(opptyIds.size(),
            lt.size(),
            'Tasks were not created');
    }
}
```

### **Things to Remember**

Scheduled Apex comes with several considerations to keep in mind:

1. **Limits:**
   - You can only have a maximum of 100 scheduled Apex jobs at one time.
   - There are limits on the maximum number of scheduled Apex executions per 24-hour period. Refer to "Execution Governors and Limits" in the Resources section for detailed information.

2. **Trigger Caution:**
   - Be cautious when scheduling a class from a trigger. Ensure that the trigger won't exceed the limit on scheduled jobs.

3. **Web Service Callouts:**
   - Synchronous Web service callouts are not supported from scheduled Apex.
   - To perform callouts, use asynchronous callouts by annotating a method with `@future(callout=true)` and call this method from scheduled Apex.
   - However, if your scheduled Apex executes a batch job, callouts are supported from the batch class.

## Notes on "Monitor Asynchronous Apex"

### **Monitoring Asynchronous Jobs**

Async jobs operate quietly in the background, but monitoring them is essential for managing your processes effectively. Here are a few methods to keep an eye on your asynchronous jobs:

1. **Salesforce User Interface:**
   - Navigate to Setup and enter "Jobs" in the Quick Find box.
   - Select "Apex Jobs" to access the Apex Jobs page.
   - This page displays all asynchronous Apex jobs along with execution details.

2. **Apex Jobs Page:**
   - The Apex Jobs page provides information about each job's execution status.
   - You can view details such as job ID, job type (e.g., future method, batch job), start time, end time, and status.

3. **Apex Jobs Screenshot:**
   - Below is an example screenshot showing one future method job and two completed batch jobs for the same Batch Apex class.
   
   ![ApexJobsMonitoring-0](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/73ae73ef-4cbd-48f1-a846-dbaf6a9cc811)
  
4. **Apex Batch Jobs Screenshot:**
   To access the Apex Batch Jobs page, follow these steps:
    - Navigate to the Apex Jobs page by entering "Jobs" in the Quick Find box in Setup and selecting "Apex Jobs."
    - At the top of the Apex Jobs page, click on the link labeled "Apex Batch Jobs."
    - Once on the Apex Batch Jobs page, you can use the slider to select a specific date range, allowing you to narrow down the list of batch jobs displayed. This feature enables you to view batch jobs executed within a particular timeframe.
     - Furthermore, on the Batch Jobs page, jobs are grouped by the batch class, providing a structured overview of batch jobs based on their respective classes. This organization facilitates easier navigation and management of batch jobs.
     - Click More Info next to a class ID you’re interested in to find out details about the jobs executed for that class.

### **Monitoring Future Jobs**
Monitoring future jobs requires some additional steps due to their unique characteristics. Here's how you can track future jobs:

1. **Apex Jobs Page:**
   - Future jobs appear on the Apex Jobs page alongside other types of asynchronous jobs.
   - Navigate to Setup, enter "Jobs" in the Quick Find box, and select "Apex Jobs."
   - Look for future jobs in the list displayed on this page.

2. **Querying AsyncApexJob:**
   - Since future jobs do not return an ID upon initiation, querying the AsyncApexJob object is necessary to find them.
   - You can filter the AsyncApexJob records based on fields like MethodName or JobType to identify future jobs.
   - Refer to sample SOQL queries available in resources like Stack Exchange to construct your query effectively.

**Note:** Future jobs are not currently part of the flex queue. Therefore, querying the AsyncApexJob object is the primary method to find and monitor future jobs.

### **Monitoring Queued Jobs with SOQL**

To query information about a queued job, you can use SOQL to retrieve details from the AsyncApexJob object. Here's an example of how to perform a SOQL query to obtain information about a submitted job:

```apex
AsyncApexJob jobInfo = [SELECT Status, NumberOfErrors
                        FROM AsyncApexJob 
                        WHERE Id = :jobID];
```

In this query:
- `AsyncApexJob` is the object representing asynchronous Apex jobs.
- `Status` and `NumberOfErrors` are fields that provide information about the job's execution status and any errors encountered.
- `WHERE Id = :jobID` filters the query results based on the specific job ID returned by the `System.enqueueJob()` method.

After executing this query, the `jobInfo` variable will contain details about the queued job, such as its current status and the number of errors, allowing you to monitor its progress and troubleshoot any issues as needed.

### **Monitoring Queue Jobs with the Flex Queue**
The Apex Flex queue manages the submission and execution of batch jobs, allowing up to 100 batch jobs to be in holding status. Here's how it works:

1. **Submitting Jobs**: When batch jobs are submitted for execution, they are initially placed in holding status and added to the Apex Flex queue. Up to 100 batch jobs can be in holding status at a time.

2. **Processing Order**: Jobs in the Apex Flex queue are processed in a first-in, first-out (FIFO) order, based on the order in which they were submitted.

3. **Resource Availability**: As system resources become available, the system picks up the next job from the top of the Apex Flex queue and moves it to the batch job queue for execution. The system can process up to five queued or active jobs simultaneously for each organization.

4. **Status Update**: The status of jobs moved from holding status to the batch job queue changes from "Holding" to "Queued". Queued jobs are executed when the system is ready to process new jobs.

5. **Monitoring**: You can monitor queued jobs, as well as active and completed jobs, in the Apex Jobs page.

### **Monitoring Scheduled Jobs**
For scheduled jobs, you can obtain additional information by querying the `CronTrigger` object. Here's how you can do it:

```apex
CronTrigger ct = [SELECT TimesTriggered, NextFireTime FROM CronTrigger WHERE Id = :jobID];
```

If you're querying from within the execute method of your schedulable class, you can obtain the ID of the current job by calling `getTriggerId()` on the `SchedulableContext` argument variable.

Additionally, you can retrieve the job's name and type from the `CronJobDetail` record associated with the `CronTrigger` record. Here's how you can query it:

```apex
CronTrigger job = [SELECT Id, CronJobDetail.Id, CronJobDetail.Name, CronJobDetail.JobType FROM CronTrigger ORDER BY CreatedDate DESC LIMIT 1];
```

You can also directly query `CronJobDetail` to get the job's name and type:

```apex
CronJobDetail ctd = [SELECT Id, Name, JobType FROM CronJobDetail WHERE Id = :job.CronJobDetail.Id];
```

Finally, to obtain the total count of all Apex scheduled jobs, you can perform the following query:

```apex
SELECT COUNT() FROM CronTrigger WHERE CronJobDetail.JobType = '7'
```

This query retrieves the count of all scheduled Apex jobs, excluding other scheduled job types.

## Notes on "Visualforce Basics"
Visualforce is a web development framework that enables developers to build sophisticated, custom user interfaces for mobile and desktop apps that can be hosted on the Lightning Platform.

You can display Visualforce pages:
- Open a Visualforce Page from the App Launcher
- Add a Visualforce Page to the Navigation Bar
- Display a Visualforce Page within a Standard Page Layout
- Add a Visualforce Page as a Component in the Lightning App Builder
- Launch a Visualforce Page as a Quick Action
- Display a Visualforce Page by Overriding Standard Buttons or Links
- Display a Visualforce Page Using Custom Buttons or Links

## Notes on "Use Simple Variables and Formulas"
### **Global Variables:**
- Access and display system values and resources in Visualforce markup.
- Example: Use $User global variable for logged-in user info: {! $User.FirstName }.

### **Formula Expressions:**
- Combine global variables and formulas for dynamic content manipulation.
- Example: Concatenate first and last name: {! $User.FirstName & ' ' & $User.LastName }.

### **Function Usage:**
- Functions are built-in calculations identifiable by parentheses after their name.
- Example Functions:
  - TODAY(): Get current date.
  - DAY(): Extract day from a date.
  - MAX(): Find maximum among given values.
  - SQRT(): Calculate square root.
  - CONTAINS(): Check if one string contains another.
  - YEAR(): Extract year from a date.

### **Conditional Expressions:**
- Use conditional expressions like IF() to display content based on conditions.
- IF() Syntax:
  - First argument: Boolean condition.
  - Second argument: Value if condition is true.
  - Third argument: Value if condition is false.
- Example:
  - Display "Yep" if 'force.com' is contained within 'salesforce.com', else "Nope".
  - Display "Before the 15th" if today's date is before the 15th, else "The 15th or after".

## Notes on "Use Standard Controllers"

**Notes on the Visualforce Standard Controller**

**Introduction:**
- Visualforce follows the MVC paradigm for separating view, logic, and data.
- Standard controllers provided by Visualforce handle standard actions and data access, facilitating integration with the Lightning Platform database.

**MVC Design Pattern:**
- View (Visualforce page) interacts with controller, which provides functionality.
- Controller interacts with model (database), making data available to view and managing changes.

**Standard Controllers:**
- Most standard and custom objects have standard controllers.
- Standard controllers can be extended for additional functionality or custom controllers can be created from scratch.
- Specify the standard controllers in the <apex:page> tag

**Record Identification:**
- To use a standard controller with a specific record, provide its record identifier (ID) in the request URL.
- ID is necessary for data retrieval and saving changes to the database.

**Displaying Data from a Single Record:**
- Include standard controller attribute in Visualforce page markup: `standardController="ObjectName"`.
- Fields from the record can be referenced in the page using syntax like `{! ObjectName.FieldName }`.
- Example: `{! Account.Name }`, `{! Account.Phone }`, etc.

## Notes on "Display Records, Fields, and Tables"
### **Introduction to Output Components**

- Visualforce offers approximately 150 built-in components for page development.
- Components are translated into HTML, CSS, and JavaScript upon page request.
- Components are categorized into coarse-grained (providing significant functionality) and fine-grained (focused functionality) types.

### **Output Components Overview:**
- Output components display data from records, enabling the creation of view-only interfaces.

### **Creating a Visualforce Page with a Standard Controller:**
- Utilize output components with a standard controller to access and display record details.
- Example: Create a Visualforce page named AccountDetail with {! Account.Name }.

**Displaying Record Details:**
- Use `<apex:detail>` to quickly incorporate record details on a page.
- `<apex:detail>` is a coarse-grained component that reproduces the standard view page for an object.

### **Displaying Related Lists:**
- Employ `<apex:relatedList>` to display lists of records related to the current record.
- Related lists are collections of similar data elements associated with the current record.
- Customize related lists independently using attributes on `<apex:relatedList>`.

```apex
<apex:relatedList list="Opportunities" pageSize="5"/><apex:relatedList list="Contacts"/>
```

### **Displaying Individual Fields:**
- Utilize `<apex:outputField>` to display individual fields from a record.
- When wrapped within `<apex:pageBlock>` and `<apex:pageBlockSection>`, `<apex:outputField>` adopts platform styling and formatting.

```apex
<apex:pageBlock title="Account Details">
    <apex:pageBlockSection>
        <apex:outputField value="{! Account.Name }"/>
        <apex:outputField value="{! Account.Phone }"/>
        <apex:outputField value="{! Account.Industry }"/>
        <apex:outputField value="{! Account.AnnualRevenue }"/>
    </apex:pageBlockSection>
</apex:pageBlock>
```

### **Displaying a Table:**
- Employ `<apex:pageBlockTable>` to add a table of data to a page.
- `<apex:pageBlockTable>` is an iteration component generating a table with platform styling.
- Use value attribute to specify the data source and var attribute to define a variable for iteration.

```apex
<apex:pageBlock title="Contacts">
   <apex:pageBlockTable value="{!Account.contacts}" var="contact">
      <apex:column value="{!contact.Name}"/>
      <apex:column value="{!contact.Title}"/>
      <apex:column value="{!contact.Phone}"/>
   </apex:pageBlockTable>
</apex:pageBlock>
```

### **Further Exploration:**
- Coarse-grained components offer extensive functionality, while fine-grained components provide more control over page details.
- Explore `<apex:enhancedList>`, `<apex:listViews>`, and other components for varied functionality.
- Experiment with iteration components like `<apex:dataTable>`, `<apex:dataList>`, and `<apex:repeat>` for diverse data presentation options.

## Notes on "Input Data Using Forms"

**Introduction to Visualforce Forms**

- Visualforce facilitates the creation of pages for data creation and editing.
- The combination of standard controller, `<apex:form>`, and form elements allows easy creation of record editing pages.
- Key aspects of such a form include record lookup, data presentation, form submission, validation, and message display.

**Creating a Basic Form:**
- Utilize `<apex:form>` and `<apex:inputField>` to create a basic data editing page.
- Employ `<apex:commandButton>` with the save action to save changes to a record or create a new one.

**Add Field Labels and Platform Styling:**
- Organize form elements within `<apex:pageBlock>` and `<apex:pageBlockSection>` for platform styling and grouping.
- Utilize `<apex:inputField>` to render input widgets based on field types with respect to field metadata.

**Display Form Errors and Messages:**
- Use `<apex:pageMessages>` to display form handling errors or messages.
- Messages provide feedback on validation errors or other issues during form submission.

**Edit Related Records:**
- Enable users to edit related information by providing links to related records.
- Utilize `<apex:outputLink>` within `<apex:pageBlockTable>` to offer actions like edit or delete on related records.

**Further Exploration:**
- Explore other input components beyond `<apex:inputField>` for varied data input scenarios.
- Investigate standard actions provided by the standard controller for core functionalities.
- Consider custom controller code for complex actions like creating new related records requiring relationship establishment.

```apex
<apex:page standardController=”Account”>
	<apex:form>
		<apex:pageBlock title=”Edit Account”>
			<apex:pageBlockSection>
				<apex:inputField value=”{! Account.Name }”/>
			</apex:pageBlockSection>
			<apex:pageBlockButtons>
				<apex:commandButton action=”{! save }” value=”Save” />
			</apex:pageBlockButtons>
		</apex:pageBlock>
	</apex:form>
</apex:page>
```

```apex
<apex:pageBlockSection columns="1">
    <apex:inputField value="{! Account.Name }"/>
    <apex:inputField value="{! Account.Phone }"/>
    <apex:inputField value="{! Account.Industry }"/>
    <apex:inputField value="{! Account.AnnualRevenue }"/>
</apex:pageBlockSection>
```

```apex
<apex:pageBlock title="Contacts">
    <apex:pageBlockTable value="{!Account.contacts}" var="contact">
        <apex:column>
            <apex:outputLink
                value="{! URLFOR($Action.Contact.Edit, contact.Id) }">
                Edit
            </apex:outputLink>
            <apex:outputLink
                value="{! URLFOR($Action.Contact.Delete, contact.Id) }">
                Del
            </apex:outputLink>
        </apex:column>
        <apex:column value="{!contact.Name}"/>
        <apex:column value="{!contact.Title}"/>
        <apex:column value="{!contact.Phone}"/>
    </apex:pageBlockTable>
</apex:pageBlock>
```

## Notes on "Use Static Resources"

Here's an overview of static resources and how to use them effectively in Visualforce:

### **Static Resources Overview:**
- **Definition:** Static resources are files that you can upload to Salesforce and reference in Visualforce pages. These files include archives (e.g., .zip, .jar), images, stylesheets, JavaScript, and more.
- **Managed Distribution:** Salesforce Lightning Platform acts as a content distribution network (CDN) for static resources, handling caching and distribution automatically.
- **Global Variable:** Static resources are referenced using the `$Resource` global variable, which can be used directly in Visualforce expressions or as a parameter in functions like `URLFOR()`.

### **Creating and Uploading Simple Static Resources:**
1. **Create Resource:** Download the desired content (e.g., jQuery library) and upload it as a static resource from Setup > Static Resources.
2. **Usage:** Reference the static resource in Visualforce using the `$Resource` global variable (e.g., `{!$Resource.jQuery}`).

### **Adding Static Resources to Visualforce Pages:**
- Use Visualforce tags like `<apex:includeScript>` or `<apex:stylesheet>` to include static resources in your page's markup.
- For example, you can include jQuery in your Visualforce page using:
  ```html
  <apex:includeScript value="{!$Resource.jQuery}"/>
  ```

### **Creating and Uploading Zipped Static Resources:**
1. **Create Zip File:** Group related files into a zip archive.
2. **Upload:** Upload the zip file as a static resource.
3. **Usage:** Reference individual files within the zipped resource using `URLFOR()` along with the static resource name.
   ```html
   {! URLFOR($Resource.ResourceName, 'path/to/file.js')}
   ```
## Notes on "Create & Use Custom Controllers"

Here's a breakdown of how to create and utilize custom controllers in Visualforce:

### **Introduction to Custom Controllers:**
- **Purpose:** Custom controllers in Visualforce allow you to define custom logic and data manipulation for your pages.
- **Functionality:** They can retrieve data, make callouts to external services, validate and insert data, and more.
- **Role in MVC:** Controllers play a crucial role in the Model-View-Controller (MVC) design pattern, where they retrieve data to be displayed and handle page actions.

### **Creating a Visualforce Page with a Custom Controller:**
1. **Page Creation:** Create a new Visualforce page and specify the custom controller class in the `controller` attribute of the `<apex:page>` tag.
2. **Initial Markup:** Replace placeholder markup with basic structure.
3. **Error Handling:** Initially, errors may occur if the controller class doesn't exist yet.

### **Creating a Custom Controller Apex Class:**
1. **Class Creation:** Create a new Apex class in the Developer Console.
2. **Basic Structure:** Define the class with the desired name, leaving the body empty initially.
3. **Error Handling:** Save the class to resolve errors in the Visualforce page.

### **Adding Methods to Retrieve Records:**
1. **Getter Method:** Create a method in the custom controller to retrieve records.
2. **SOQL Query:** Use SOQL to query records from the database.
3. **Return Results:** Make the retrieved records available to the Visualforce page by returning them from the method.

### **Adding Action Methods:**
1. **Custom Actions:** Define action methods in the custom controller to respond to user interactions.
2. **Logic Implementation:** Implement logic in action methods to perform specific actions, such as sorting records.
3. **Markup Integration:** Integrate action methods with Visualforce markup using components like `<apex:commandLink>` to trigger actions.

### **Finalizing the Visualforce Page:**
1. **Markup Enhancement:** Enhance the Visualforce page markup to include interactive components like clickable headers.
2. **Method Invocation:** Connect action methods to interactive components to enable user interaction.
3. **Testing:** Preview the page to ensure functionality and verify that actions trigger expected behaviors.

### **Beyond the Basics:**
- **Localization:** Utilize dynamic references to field labels for multilingual support.
- **Apex Properties:** Explore the use of properties as an alternative to getter and setter methods for enhanced readability and functionality.
- **Lifecycle Considerations:** Understand the lifecycle of Visualforce requests and responses to avoid dependencies between controller methods.

```apex
<apex:page controller="ContactsListWithController">
    <apex:form>
        <apex:pageBlock title="Contacts List" id="contacts_list">
            <!-- Contacts List goes here -->
        </apex:pageBlock>
    </apex:form>
</apex:page>
```

```apex
public class ContactsListWithController {
	private String sortOrder = 'LastName';
	public List<Contact> getContacts() {
	    List<Contact> results = Database.query(
	        'SELECT Id, FirstName, LastName, Title, Email ' +
	        'FROM Contact ' +
	        'ORDER BY ' + sortOrder + ' ASC ' +
	        'LIMIT 10'
	    );
	    return results;
	}
}
```
# Notes on "Lightning Web Components Basics"
## Discover Lightning Web Components

### Learning Objectives
- Explain the Lightning Web Components programming model.
- List the benefits of using Lightning web components.
- Find what you need to get started developing Lightning web components.

### An Open Door to Programming with Web Standards
- Combines Salesforce knowledge with standard technologies like HTML, JavaScript, and CSS.
- Maintains compatibility with existing Aura components.

**Note**:
- Lightning Web Components programming model capitalizes all words.
- Components are referred to as Lightning web components.

### Before You Go Further
- Requires basic understanding of Salesforce DX projects and Salesforce CLI.
- Utilize properly configured org in Trailhead account and VS Code with Salesforce Extension Pack.
- Quick Start: Lightning Web Components provides necessary information.

### Why Lightning Web Components?
- Utilizes modern browser standards.
- Lightweight and delivers exceptional performance.
- Easier to find solutions, developers, and experiences.
- Faster development.
- Provides full encapsulation for versatility.

### Simple Component Creation
- Components created using HTML, JavaScript, and CSS.
- Creation involves (1) JavaScript file, (2) HTML file, and optionally (3) CSS file.
- HTML provides structure, JavaScript defines logic, and CSS provides appearance.

### Example:
HTML:
```html
<template>
  <input value={message}></input>
</template>
```

JavaScript:
```javascript
import { LightningElement } from 'lwc'; 
export default class App extends LightningElement { 
  message = 'Hello World'; 
}
```

CSS:
```css
input {
  color: blue;
}
```

### Lightning Web Components and Aura Components Do Work Together
- Lightning web components can coexist with existing Aura components.
- Aura components can contain Lightning web components.
- Full encapsulation and adherence to common standards provided by Lightning web components.

### Cool Stuff You Can Use

To develop Lightning web components efficiently, use the following tools and environments.

**Dev Hub and Scratch Orgs**
- **Scratch Orgs:** Disposable Salesforce orgs for development and testing.
- **Dev Hub:** Manages scratch orgs, part of Salesforce DX tool set.

**Salesforce Command Line Interface (CLI)**
- Provides quick operations for creating, configuring scratch orgs, and deploying components.
- Part of Salesforce DX tool set.

**Lightning Component Library**
- Reference for Aura and Lightning web components: [Component Library](https://developer.salesforce.com/docs/component-library/overview/components).
- View through org's instance: [Instance Library](http://<MyDomainName>.lightning.force.com/docs/component-library).
- Custom components appear in the library.

**GitHub**
- Share extensions, samples, and more through GitHub repos.
- Get a GitHub account to access offerings.

**Visual Studio Code Salesforce Extension Pack**
- Provides code-hinting, lint warnings, and built-in commands.
- [Salesforce Extension Pack](https://marketplace.visualstudio.com/items?itemName=salesforce.salesforcedx-vscode).

**Lightning Web Components Recipes**
- GitHub repo with mix of samples to understand how Lightning web components work.
- Clone, tinker, and publish to your own scratch org: [LWC Recipes](https://github.com/trailheadapps/lwc-recipes).

**E-Bikes Demo**
- GitHub repo demonstrating end-to-end implementation of Lightning web components to create an app.
- Try in your own scratch org: [E-Bikes Demo](https://github.com/trailheadapps/ebikes-lwc).

**Lightning Data Service (LDS)**
- Access data and metadata from Salesforce via Lightning Data Service.
- Base Lightning components working with data are built on LDS.

**Lightning Locker**
- Secure Lightning web components belonging to one namespace from components in a different namespace through Security with Lightning Locker.
- Promotes best practices, improves supportability of code by restricting access to supported APIs and eliminating access to nonpublished framework internals.

## Create Lightning WebComponents
### A Look Into the HTML File

Lightning web component HTML files include the template tag, defining the component's structure. Below is a simplified version of the productCard component from the ebikes repo.

### Steps:
1. **Create Project:**
   - Select "SFDX: Create Project" from the Command Palette in VS Code.
   - Accept the standard template and name the project "bikeCard".

2. **Create Lightning Web Component:**
   - Right-click the `lwc` folder under `force-app/main/default`.
   - Select "SFDX: Create Lightning Web Component".
   - Name the component "app".

3. **HTML (app.html):**
```html
<template>
  <div>
    <div>Name: {name}</div>
    <div>Description: {description}</div>
    <div>Category: {category}</div>
    <div>Material: {material}</div>
    <div>Price: {price}</div>
    <div><img src={pictureUrl}/></div>
  </div>
</template>
```

4. **JavaScript (app.js):**
```javascript
import { LightningElement } from 'lwc';
export default class App extends LightningElement {
  name = 'Electra X4';
  description = 'A sweet bike built for comfort.';
  category = 'Mountain';
  material = 'Steel';
  price = '$2,700';
  pictureUrl = 'https://s3-us-west-1.amazonaws.com/sfdc-demo/ebikes/electrax4.jpg';
}
```

5. **Real-world Example with Conditional Rendering:**
   - Use `lwc:if` and `lwc:else` conditional directives to manage rendering based on data readiness.

### HTML (app.html):
```html
<template>
  <template lwc:if={ready}>
    <div id="display">
      <div>Name: {name}</div>
      <div>Description: {description}</div>
      <div>Category: {category}</div>
      <div>Material: {material}</div>
      <div>Price: {price}</div>
      <div><img src={pictureUrl}/></div>
    </div>
  </template>
  <template lwc:else>
    <div id="waiting">Loading…</div>
  </template>
</template>
```

### JavaScript (app.js):
```javascript
import { LightningElement } from 'lwc';
export default class App extends LightningElement {
  name = 'Electra X4';
  description = 'A sweet bike built for comfort.';
  category = 'Mountain';
  material = 'Steel';
  price = '$2,700';
  pictureUrl = 'https://s3-us-west-1.amazonaws.com/sfdc-demo/ebikes/electrax4.jpg';
  ready = false;
  connectedCallback() {
    setTimeout(() => {
      this.ready = true;
    }, 3000);
  }
}
```

6. **Save the files.**

### Base Lightning Web Components

To utilize base Lightning web components, you can easily integrate them into your project. Let's enhance the bike details display by using a lightning-badge component.

**HTML (app.html):**
```html
<template>
  <template lwc:if={ready}>
    <div id="display">
      <div>Name: {name}</div>
      <div>Description: {description}</div>
      <lightning-badge label={material}></lightning-badge>
      <lightning-badge label={category}></lightning-badge>
      <div>Price: {price}</div>
      <div><img src={pictureUrl}/></div>
    </div>
  </template>
  <template lwc:else> 
    <div id="waiting">Loading…</div>
  </template>
</template>
```
- Replace the div tags for material and category with lightning-badge components.

**Note**:
- The words Steel and Mountain appear as badges, enhancing the presentation.

**Component Build Structure:**
- Each component needs a folder and files with the same name, automatically linked by name and location.
  
**Component files in a folder:**
- All Lightning web components have a namespace separated from the folder name by a hyphen. For example, `<c-app>` for the component in the default namespace `c`.
- Salesforce platform doesn't allow hyphens in the component folder or file names. Use camel case to name your component like `myComponent`.
- Camel case component folder names map to kebab-case in markup. For example, `<c-my-component>`.
- Example: The `viewSource` component in the `LWC Samples` repo is referenced as `c-view-source` in HTML.

-- App
  -- app.css
  -- app.html
  -- app.js

  Here's a breakdown of working with JavaScript in Lightning web components:

### JavaScript Structure:
- JavaScript methods define component behavior, handling input, data, events, and state changes.

#### Base Class and Module:
- The JavaScript file must import `LightningElement` from the `lwc` module and extend it with the component's class.
- Use of ECMAScript 6 modules bundles core functionality, making it accessible to component JavaScript.
- Example:
  ```javascript
  import { LightningElement } from 'lwc';
  export default class MyComponent extends LightningElement {
    // Component functionality goes here
  }
  ```

#### Lifecycle Hooks:
- Lightning web components provide methods, called lifecycle hooks, to respond to critical events in a component's lifecycle.
- Examples include `connectedCallback()` for component insertion in the DOM.
- Lifecycle methods allow automatic execution of code at specific lifecycle stages.
- Example:
  ```javascript
  import { LightningElement } from 'lwc';
  export default class MyComponent extends LightningElement {
    ready = false;
    connectedCallback() {
      setTimeout(() => {
        this.ready = true;
      }, 3000);
    }
  }
  ```

#### Decorators:
- Decorators modify the behavior of properties or functions.
- Imported from the `lwc` module and placed before the property or function.
- Examples include `@api`, `@track`, and `@wire` decorators.
- Example:
  ```javascript
  import { LightningElement, api, track } from 'lwc';
  export default class MyComponent extends LightningElement {
    @api message;
    @track data;
    // More component code...
  }
  ```

**Note:**
- Decorators like `@api` mark fields as public, allowing access from parent components.
- `@track` observes changes to object properties or array elements, triggering component rerendering.
- `@wire` simplifies data retrieval and binding from a Salesforce org.

## Notes on "Deploy Lightning Web Component Files"
### Component Configuration File
The component configuration file with the extension .js-meta.xml provides metadata for Salesforce, including design configuration for components intended for use in Lightning App Builder. Here's an example:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
  <apiVersion>57.0</apiVersion>
  <isExposed>true</isExposed>
  <masterLabel>Product Card</masterLabel>
  <targets>
    <target>lightning__AppPage</target>
    <target>lightning__RecordPage</target>
    <target>lightning__HomePage</target>
    <target>lightningCommunity__Page</target>
  </targets>
  <targetConfigs>
    <targetConfig targets="lightning__RecordPage">
      <objects>
        <object>Product__c</object>
      </objects>
    </targetConfig>
  </targetConfigs>
</LightningComponentBundle>
```

#### Required Elements:
- `apiVersion`: Binds the component to a Salesforce API version.
- `isExposed`: Indicates whether the component is exposed to Lightning App Builder or Experience Builder.

#### Optional Elements:
- `targets`: Specifies which types of Lightning pages the component can be added to in Lightning App Builder.
- `targetConfigs`: Allows specifying behavior specific to each type of Lightning page, including supported objects.

### Displaying a Component in an Org:
- Two options:
  1. Set the component to support various flexipage types and add it to a flexipage using the Lightning App Builder.
  2. Create a tab pointing to an Aura component containing the Lightning web component.

### Deploying Your Files:
- Authenticate with your org using SFDX: Authorize an Org from the Command Palette in VS Code.
- Right-click on the force-app/main/default folder and select SFDX: Deploy this Source to Org.

### Creating a New Page for Your Component:
- Open your org using SFDX: Open Default Org from the Command Palette in VS Code.
- In Setup, go to Lightning App Builder.
- Click New, select App Page, and follow the steps to create a new page.
- Drag your component onto the page, save, and activate it.

### Viewing Your Component:
- Access your component in the UI by navigating to the page where it's added, such as through the App Launcher.

## Notes on "Handle Events in Lightning Web Components"

### Events Up, Properties Down
In a complex component (one that contains several parent and child components), the components can communicate up and down.
![EventsupPropertiesDown](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/91c262da-7571-4847-86f1-3e7cce952bfa)

### Passing Information Upwards:
- To pass information upwards from a child component to a parent component, events and event listeners are used.
- The child component dispatches an event, and the parent component listens for it.
- Example:
  - In the child component (`c-todo-item`), a `nextHandler()` method dispatches a custom event named `'next'` when the user clicks a Next button.
    ```javascript
    // todoItem.js
    import { LightningElement } from 'lwc';
    
    export default class TodoItem extends LightningElement {
        nextHandler() {
            this.dispatchEvent(new CustomEvent('next'));
        }
    }
    ```
  - In the parent component's template (`todoApp.html`), the child component (`c-todo-item`) is added with an event listener (`onnext`) that calls the `nextHandler()` method in the parent component.
    ```html
    <!-- todoApp.html -->
    <template>
        <c-todo-item onnext={nextHandler}></c-todo-item>
    </template>
    ```
  - In the parent component's JavaScript (`todoApp.js`), a method named `nextHandler()` handles the event and updates the state accordingly.
    ```javascript
    // todoApp.js
    import { LightningElement } from 'lwc';
    
    export default class TodoApp extends LightningElement {
        nextHandler() {
            // Update state or perform actions
        }
    }
    ```

### Passing Information Downwards:
- To pass information downwards from a parent component to a child component, public properties and public methods are used.
- Public properties are defined in the child component using the `@api` decorator, allowing them to be set by the parent component.
- Example:
  - In the child component (`c-todo-item`), a public property named `itemName` is defined with the `@api` decorator.
    ```javascript
    // todoItem.js
    import { LightningElement, api } from 'lwc';
    
    export default class TodoItem extends LightningElement {
        @api itemName;
    }
    ```
  - In the parent component's template (`todoApp.html`), the child component (`c-todo-item`) is added with the `item-name` attribute set to a value, which sets the `itemName` property in the child component.
    ```html
    <!-- todoApp.html -->
    <template>
        <c-todo-item item-name="Milk"></c-todo-item>
    </template>
    ```

### Handling Events in HTML
In the provided code snippet, the tile component is configured to handle click events directly in the HTML template using an `onclick` event listener. Here's how it works:

```html
<template>
  <div class="container">
    <a onclick={tileClick}>
      <div class="title">{product.fields.Name.value}</div>
      <img class="product-img" src={product.fields.Picture_URL__c.value}></img>
    </a>
  </div>
</template>
```

In this template:
- An anchor `<a>` tag is used to wrap the tile content.
- The `onclick` attribute is added to the anchor tag, and its value is set to `{tileClick}`. This means that when the anchor element is clicked, the `tileClick` method in the JavaScript file will be invoked.

In the JavaScript file (`tile.js`), the `tileClick` method is defined as follows:

```javascript
import { LightningElement, api } from 'lwc';

export default class Tile extends LightningElement {
  @api product;

  tileClick() {
    const event = new CustomEvent('tileclick', {
      detail: this.product.fields.Id.value // Passes the ID of the clicked tile as detail
    });

    // Fire the custom event from the tile component
    this.dispatchEvent(event);
  }
}
```

Explanation:
- When the anchor element is clicked, the `tileClick` method is called.
- Inside `tileClick`, a new `CustomEvent` is created with the event type `'tileclick'`. The `detail` property of the event object is set to `this.product.fields.Id.value`, which contains the ID of the clicked tile. The `detail` property can contain any information that needs to be passed along with the event.
- Finally, the custom event is dispatched from the tile component using `this.dispatchEvent(event)`.


### Selector App Event Pattern
The event pattern described in the selector app involves propagating events up through the component hierarchy so that parent components can respond to child events. Here's a breakdown of how the event handling works in the app:

![SelectorAppEventPattern](https://github.com/jeudy100/PlatformDeveloperNotes/assets/19577027/0f5f0f4a-05de-40e9-a59f-5347b6f454b6)

1. **Tile Component (`tile.html` and `tile.js`)**:
   - `tile.html` contains an `onclick` event listener that calls the `tileClick` handler when a tile is clicked.
   - `tile.js` defines the `tileClick` method, which creates a new CustomEvent with the event type `'tileclick'` and an object containing detail data, such as the ID of the clicked tile (`this.product.fields.Id.value`).

2. **List Component (`list.html` and `list.js`)**:
   - `list.html` includes an `ontileclick` event listener that calls the `handleTileClick` handler.
   - `list.js` defines the `handleTileClick` method, which receives the event (`evt`) and creates another CustomEvent with the event type `'productselected'` and an object containing detail data extracted from the original event (`evt.detail`). This event is then dispatched from the `c-list` component.

3. **Selector Component (`selector.html` and `selector.js`)**:
   - `selector.html` contains the `onproductselected` event listener that calls the `handleProductSelected` handler.
   - `selector.js` defines the `handleProductSelected` method, which sets the `selectedProductId` variable to the `evt.detail` value passed into it. The `selectedProductId` variable is then passed from the selector component to the detail component in `selector.html` using the property binding `product-id={selectedProductId}`.

4. **Detail Component (`detail.html` and `detail.js`)**:
   - `detail.html` utilizes a conditional directive `<template lwc:if={product}>` to conditionally render content based on the existence of the `product` data.
   - `detail.js` creates a private variable `_productId` to track the state of the `productId` value. It uses a getter and setter pattern to process the value every time it's requested, allowing for logic and conditions to be applied to property assignments.

# Secure Server-Side Development
## Notes on "Write Secure Apex Controllers"
### Apex Security and Sharing
  - Apex code security is crucial.
  - Apex classes have default access to all data.
  - Enforcing sharing rules, object and field permissions, and protecting against CRUD and FLS violations are essential.

### Enforcing Sharing Rules
  - Apex generally runs in system context.
  - `with sharing` and `without sharing` keywords determine sharing rule enforcement.
  - `inherited sharing` ensures clarity in sharing context.
  - Exceptions: `executeAnonymous` and Chatter in Apex.
  - **Update Permission**
    - Check if the logged-in user can update the email field of a contact.
    - Example:
      ```apex
      if (Schema.sObjectType.Contact.fields.Email.isUpdateable()) {
         // Update contact email address
      }
      ```

  - **Create Permission**
    - Verify if the user can create a new contact with an email field.
    - Example:
      ```apex
      if (Schema.sObjectType.Contact.fields.Email.isCreateable()) {
         // Create new contact
      }
      ```

  - **Read Permission**
    - Ensure the user has access to read the email field of a contact.
    - Example:
      ```apex
      if (Schema.sObjectType.Contact.fields.Email.isAccessible()) {
         Contact c = [SELECT Email FROM Contact WHERE Id= :Id];
      }
      ```

  - **Object-Level Permission for Contact**
    - Confirm if the user can delete a contact.
    - Example:
      ```apex
      if (Schema.sObjectType.Contact.isDeletable()) {
         // Delete contact
      }
      ```

### Protect Against CRUD and FLS Violations
  - CRUD access: create, read, update, delete.
  - Use `WITH SECURITY_ENFORCED` clause in SOQL queries.
  - Check object and field permissions with `Schema.DescribeSObjectResult` methods.
  - Helper functions: `isCreateable()`, `isAccessible()`, `isUpdateable()`, `isDeletable()`.

### Field-Level Security
  - FLS restricts access to fields.
  - Use `stripInaccessible` method to enforce field- and object-level data protection.
  - Access checks based on current user's permissions.
  - `isSet` method identifies inaccessible fields.

### Conclusion
  - Understanding sharing rules, object and field permissions, CRUD and FLS protections ensures Apex code security.
  - Next step: Protect against injection vulnerabilities.

## Mitigate SOQL Injection

### Salesforce Object Query Language (SOQL) vs. Structured Query Language (SQL)

- **Differences:**
  - SOQL is for querying data only, unlike SQL which also modifies data.
  - Missing functionalities in SOQL:
    - INSERT, UPDATE, DELETE statements.
    - Command execution.
    - JOIN statement (although parent object information can be included).
    - UNION operator.
    - Ability to chain queries.
    - Disallows wild card

- **SOQL Injection Vulnerability:**
  - SOQL injection is still possible due to incorrect trust in user input.
  - Example scenario: Searching personnel by title in a developer org.
  - User input directly inserted into SOQL query without validation.
  - Example exploit: Adding a condition to the query via input manipulation.

- **Prevention Techniques:**
  - **Static Query with Bind Variables:**
    - Recommended method.
    - User input treated as a variable.
    - Bind variables can be used in specific clauses.
   
   **Note:**
  	- This code allows for query injections because we are directly using the user input
   ```apex
   String query = ‘select id from contact where firstname =\’’+var+’\’’;
   queryResult = Database.execute(query);
   ```

   **Note:**
  	- This code doesn't use the user input directly because it is treated as a variable and not an executable element of the query (bind variable).
   ```apex
   queryResult = [select id from contact where firstname =:var]
   ```
  
  - **Typecasting:**
    - Cast variables to specific types to prevent unexpected input.
    - By casting all variables as strings, user input can drift outside of expectation
    - By typecasting variables as integers or Booleans, when applicable, erroneous user input is not permitted
    - Transform variables back to string for query insertion.
    - Only good against non-string input

   **Note:**
        - Age__c is a integer
   	- Getting the string value here prevents unexpected user input
   	- If you submitted your SOQL injection payload “1 limit 1” in the search area again, you would see an error rather than a SOQL injection. “1 limit 1” is not considered an integer, so the SOQL injection is 	prevented.
   ```apex
   whereClause+='Age__c >'+string.valueOf(textualAge)+'';
   ```
  
  - **Escaping Single Quotes:**
    - Escape single quote marks in user input using `String.escapeSingleQuotes()`.
    - Prevents input from being treated as code.
    - applies only to strings
    - Not all variables are strings, and not all SOQL injection attacks require the use of a single quote character
  
  - **Replacing Characters:**
    - Blocklist approach to remove "bad characters" from user input.
    - Less effective than allowlisting.
    	- it is far easier to predict a few good inputs than it is to predict all possible bad inputs
     -  
  
  - **Allowlisting:**
    - Create a list of allowed values.
    - Reject input that doesn't match the list.

- **Conclusion:**
  - SOQL injection can be prevented using various techniques discussed.
  - Developers should be aware of vulnerabilities and apply appropriate safeguards.
  - Next, Cross-Site Request Forgery (CSRF) attack prevention will be discussed.

## Mitigate Cross-Site Request Forgery
A web application vulnerability where a malicious application causes a user’s client to perform an unwanted action on a trusted site for which the user is authenticated.

- **Scenario:**
  - Example: Clicking a hyperlink on an external site unknowingly performs an action (e.g., promoting a student to the honor roll) on a trusted site (e.g., School District Management) where the user is authenticated.

- **Prevention Techniques:**
  - **Token-Based Prevention:**
    - Include a unique token with all sensitive state-changing requests.
    - Token must be:
      - Unique to the request or user’s session.
      - Difficult to predict (long and random).
      - Validated by the server to ensure request origin.
      
  - **Salesforce Platform Protections:**
    - Salesforce resources have CSRF tokens attached by default.
    - Prevent reuse and distribution of hyperlinks to protect privileged accounts.
    
  - **Development Best Practices:**
    - Avoid state-changing HTTP GET requests.
    - Prefer POST or PUT requests for state changes.
    - Validate origin header in API endpoints.
    
  - **Custom Anti-CSRF Tokens:**
    - Add anti-CSRF tokens to XMLHttpRequests within Lightning.
    - Example: Using setRequestHeader() method.

- **Implementation Considerations:**
  - Proper implementation requires:
    - Inclusion of tokens in all sensitive requests.
    - Token uniqueness and unpredictability.
    - Server-side validation of tokens.
    
- **Conclusion:**
  - Understanding CSRF vulnerabilities is crucial for building secure web applications.
  - Utilizing token-based prevention and adhering to development best practices help mitigate CSRF risks.
  - Salesforce provides built-in protections, but developers should still be vigilant in implementing additional safeguards.

# Developer Console Basics
## Notes on "Get Started with the developer Console"
The Developer Console in Salesforce is an integrated development environment (IDE) where developers can perform various development tasks within their Salesforce org.

**Features of the Developer Console:**
1. **Code Editing:** Create, edit, and debug Apex classes and triggers, Aura components, and Visualforce pages and components.
2. **Package Browsing:** Browse packages created within your org.
3. **Logging:** Generate logs for debugging purposes and analyze them using different perspectives.
4. **Testing:** Test Apex code to ensure it's error-free.
5. **Debugging:** Identify and resolve errors by setting checkpoints in your Apex code.
6. **SOQL and SOSL Queries:** Write and execute SOQL and SOSL queries to interact with records in your org.

**Usage Scenarios:**
- Use the Developer Console for immediate changes without installing anything on your computer.
- Connect to one org and perform development tasks directly within the browser.
- Access logs, errors, and write queries to interact with records.
- It's suitable for individual developers or small teams working on one org.

**Accessing the Developer Console:**
1. After logging in to your org, click on "Developer Console" under the quick access menu or your name.
2. The main pane serves as the source code editor, while the tabs pane displays logs, errors, and other information.

**Workspace Setup:**
- A workspace is a collection of resources (tabs) in the Developer Console that helps organize information.
- Create different workspaces for different projects or tasks to reduce clutter and improve navigation.
- To set up a workspace, go to Workspace > New Workspace, give it a name, and start organizing your resources.
- Switch between workspaces by selecting Workspace > Switch Workspace.

## Notes on "Navigate and Edit Source Code"
### Creating and Executing Apex Code:

1. **Create an Apex Class:**
   - In the Developer Console, select `File | New | Apex Class`.
   - Write your Apex code for sending an email to the Mission Specialist.
   - Save your code using `File | Save`.

2. **Execute an Apex Class:**
   - Select `Debug | Open Execute Anonymous Window`.
   - Copy the provided Apex code and paste it into the window.
   - Replace `'Enter your email address'` with your email address.
   - Click `Execute`.
   - Check your email to see if the message was sent successfully.

### What Are Lightning Components?

Lightning Components is a framework for building responsive user interfaces for both mobile and desktop applications on the Salesforce Lightning Platform. It allows developers to create interactive and visually appealing interfaces using a component-based architecture.

- **Aura Components:** The original model for building Lightning Components. They are based on the Aura framework and support event-driven architecture.
- **Lightning Web Components:** Introduced in the Spring '19 release, this model offers a modern, standards-based programming model for building Lightning Components using web standards like HTML, JavaScript, and CSS. Lightning Web Components can coexist and interact with Aura Components.

### Using Developer Console for Lightning Components:

- Developer Console allows you to create Aura components and manage component bundles.
- You can create a component bundle, which acts as a container for all related resources such as controllers, style sheets, and design files.
- With Developer Console, you can develop, edit, and test your Aura components seamlessly within the Salesforce environment.

### Creating an Aura Component:

1. **Create a New Aura Component:**
   - In the Developer Console, select `File | New | Lightning Component`.
   - Name your component "meetGreet" and click Submit.
   - Click on the tab labeled "meetGreet.cmp" to open the file.

2. **Explore Component Resources:**
   - On the right-hand side of the window, you'll see a list of resources in the component bundle.
   - These resources include Style, Helper, Controller, Renderer, and Documentation.
   - You can open any resource by clicking on it to start building different parts of the component.

3. **Open Saved Aura Components or Resources:**
   - Select `File | Open Lightning Resources`.
   - Type your component's name in the search box or select its folder from the list.
   - Click the arrow next to the folder to see its resources.
   - Select the resource you want to work on and click `Open Selected`.

### Create a Visualforce Page:

1. **Create a New Visualforce Page:**
   - Select `File | New | Visualforce Page`.
   - Name your page "FlightSystemsChecklist" and click Save.
   - Click on `File | Save` to save the Visualforce page.

2. **Preview the Visualforce Page:**
   - In the top left corner, click `Preview` to open a preview of your Visualforce page in your browser.

3. **Open Saved Visualforce Pages:**
   - Select `File | Open`.
   - Under Entity Type, click `Pages`.
   - Double-click the page you want to open from the list of entities.

## Notes on "Generate and Analyze Logs"
To view debug logs in the text editor using the Developer Console, follow these steps:

1. **Execute Apex Code:**
   - Select `Debug | Open Execute Anonymous Window`.
   - Ensure that the Enter Apex Code window is displaying the code you want to execute. If not, paste the correct code.
   - Replace 'Enter your email address' with your actual email address.
   - Click `Execute`.

2. **View Logs:**
   - Before executing the code, enable the option to `Open Log` in the Enter Apex Code window.
   - After execution, the log will open automatically, displaying the details of the execution.

### View Logs in the Text Editor
To read your log data in the Developer Console, follow these steps:

1. **Execute Code with Error:**
   - Select `Debug | Open Execute Anonymous Window`.
   - Provide an invalid email address, such as 'testingemail'.
   - Execute the code.

2. **View the Log:**
   - After the code execution, a dialog box will appear indicating the error.
   - Click `OK`.
   - In the `Logs` tab, find and double-click the new log.
   - If unsure which log is the newest, click the heading for the `Time` column to sort the logs by the time they were generated.

3. **Understanding the Log:**
   - Each log entry displays a timestamp, event, and details about the code execution.
   - The `Timestamp` column shows when the event occurred.
   - The `Event` column indicates the type of event, such as `FATAL_ERROR` for errors.
   - You can filter the log by selecting different options like `This Frame`, `Executable`, or `Debug Only`.
   - Use the `Filter` option to search for specific text or method names.
   - You can also view the log as a raw log by selecting `File | Open Raw Log`, which provides more detailed information including nanosecond timestamps.

4. **Using System.debug():**
   - In your Apex code, you can use `System.debug()` to log messages, variable values, or labeled values for debugging.
   - Syntax examples:
     - `System.debug('Your Message');`
     - `System.debug(yourVariable);`
     - `System.debug('Your Label: ' + yourVariable);`

### Use the Log Inspector
The Log Inspector in the Developer Console provides various panel views to offer different perspectives of your code execution. Here's an overview of the log panels available in the Log Inspector:

1. **Stack Tree:**
   - Displays log entries within the hierarchy of their objects and their execution using a top-down tree view.
   - For example, if one class calls a second class, the second class is shown as the child of the first.

2. **Execution Stack:**
   - Presents a bottom-up view of the selected item.
   - It displays the log entry, followed by the operation that called it.

3. **Execution Log:**
   - Lists every action that occurred during the execution of your code.

4. **Source:**
   - Shows the contents of the source file, indicating the line of code being run when the selected log entry was generated.

5. **Source List:**
   - Provides the context of the code being executed when the event was logged.
   - For example, if you select the log entry generated when the faulty email address value was entered, the Source List shows `execute_anonymous_apex`.

6. **Variables:**
   - Displays the variables and their assigned values that were in scope when the code that generated the selected log entry was run.

7. **Execution Overview:**
   - Presents statistics for the code being executed, including the execution time and heap size.

### What Is the Perspective Manager and How Can You Switch Perspectives?
The Perspective Manager in the Developer Console allows you to customize your workspace layout by grouping panels into different perspectives. Here's how you can switch perspectives and create your own:

1. **Switch Perspectives:**
   - To switch between predefined perspectives, you can select **Debug | Switch Perspectives** or **Debug | Perspective Manager**.
   - This action opens a menu where you can choose from available perspectives, such as Debug or Analysis.
   - For example, the Debug perspective may display the Execution Log, Source, and Variables panels, while the Analysis perspective may include the Stack Tree, Execution Log, Execution Stack, and Execution Overview panels.

2. **Create Your Own Perspective:**
   - If the predefined perspectives don't meet your needs, you can create your own custom perspective.
   - First, configure your preferred combination of panels to display in the Developer Console.
   - Then, select **Debug | Save Perspective As**.
   - Enter a name for your custom perspective and click OK to save it.
   **Note: **
   - The Debug | View Log Panels menu option is available only when you’re viewing a debug log tab. If, for example, you’re viewing the raw log tab, the option is grayed out.

### Log Categories
1. **ApexCode:**
   - This log category captures events related to Apex code execution. It includes information about the start and end of Apex methods, as well as any debug statements or system-generated messages within the Apex code.
   - Examples of events logged under the ApexCode category include method entry and exit points, variable assignments, exception handling, and debug statements.
   - When troubleshooting Apex code behavior or performance issues, examining logs under the ApexCode category can provide insights into the sequence of method calls, variable values, and potential errors encountered during execution.

2. **Database:**
   - The Database log category logs events related to database operations within Salesforce, such as Database Manipulation Language (DML) statements, SOSL (Salesforce Object Search Language) queries, and SOQL (Salesforce Object Query Language) queries.
   - DML statements include operations like insert, update, delete, and undelete performed on Salesforce records.
   - SOSL and SOQL queries retrieve data from Salesforce objects based on specific search criteria or conditions.
   - Monitoring logs under the Database category can help identify database-related issues, such as inefficient queries, data manipulation errors, or governor limit violations affecting database operations.

### Log Levels and How to Change Them
Log levels control the amount of detail logged for each log category in the Developer Console. Here's an overview of the available log levels and how to change them:

1. **Log Levels:**
   - **NONE:** No log data is recorded.
   - **ERROR:** Only error messages are logged.
   - **WARN:** Warning messages and error messages are logged.
   - **INFO:** Informational messages, warning messages, and error messages are logged.
   - **DEBUG:** Debug messages, informational messages, warning messages, and error messages are logged.
   - **FINE:** Fine-grained debug messages, debug messages, informational messages, warning messages, and error messages are logged.
   - **FINER:** Even more detailed debug messages in addition to all the messages logged at the FINE level.
   - **FINEST:** The most detailed logging level, including all debug messages, informational messages, warning messages, and error messages.

2. **Changing Log Levels:**
   - To modify the log levels for different events, select **Debug | Change Log Levels** in the Developer Console.
   - On the **General Trace Settings for You** tab, click **Add/Change**.
   - In the **Change DebugLevel** window, choose the desired log level for each log category.
   - Remember to use log levels judiciously, as setting excessively detailed logging levels (e.g., FINEST) can lead to hitting log limits and longer execution times for your code.

# Command-Line Interface
## Notes on "Learn About the Command-Line Interface"

The command-line interface (CLI) offers a text-based way to interact with your computer or software program directly. Here's a summary of its uses and how to access it on different operating systems:

#### Command-Line Uses:
- **Quick Execution:** Perform complex actions quickly.
- **Customization:** Customize development workspace tools with plug-ins and packages.
- **Script Execution:** Execute scripts.
- **Directory Management:** Create and manage directories.
- **Version Control:** Interact with version control systems like Git.
- **Development:** Build and work on source-driven development projects or CI integrations.

### Accessing the Command-Line Tool:
#### macOS/Linux:
- **Terminal:** Open Finder and search for "Terminal" or click the terminal icon to open it.

#### Windows:
- **Command Prompt:** Click Start, type "command" or "cmd" in the search field, and press Enter. Then click the Command Prompt shortcut to open the tool.

#### Code Editor Terminal Window:
- **Visual Studio Code (VS Code):** Code editors like VS Code have built-in terminal windows. Use VS Code's terminal window to run commands and work with the Salesforce CLI. It provides the ability to code your project and access the terminal window simultaneously.

#### Shell:
- A shell interprets and executes the commands entered in the command-line interface.
- On macOS, Zsh (Z shell) is commonly used, while on Windows, PowerShell is the default.
- Each shell has its syntax and capabilities, with BASH (Bourne Again Shell) being a common choice for macOS and Linux.

#### Differences Between PowerShell and Zsh:
- **Output Handling:** Zsh treats outputs as strings, while PowerShell treats the output as objects.
- **Syntax:** Commands are written differently for each OS, and features vary between shells.

### Explore Command Structure and Navigation

All commands have three main parts:

1. **Command:** The action you want the tool to take.
2. **Flag:** Specifies a value that triggers a process or tells the command what other variables to include.
3. **Argument:** Tells the system what variable you want to set or which process you want to invoke.

For example:

```
$ sf project generate -n MyProject
```

- **Command:** `sf project generate`
- **Flag:** `-n` (used to specify the project name)
- **Argument:** `MyProject` (the name of the project)

#### Flags, Switches, and Arguments

- **Flags:** Provide additional information or specify options for the command.
- **Switches:** Similar to flags but don't require arguments.
- **Arguments:** Directly influence the behavior of the command.

For instance, in `sf org create scratch -f project-scratch-def.json -d`:

- `-f` specifies the scratch org definition file.
- `-d` sets the newly created scratch org as default.

#### Navigation Commands

- `cd` (Change Directory): Navigate to a specified directory.
- `mkdir` (Make Directory): Create a new directory.
- `cd ..`: Navigate one directory level up.
- `pwd` (Print Working Directory): View the current directory path.

#### Running Commands

- Type the command in the command-line window and press Enter.
- Ensure correct capitalization.
- Observe the prompt symbol (`$` for macOS/Linux, `>` for Windows) to confirm command execution.

#### Viewing Command-Line History

- Type `history` or use the shortcut `F7` in Windows to view command history.
- Rerun a command by typing `!` followed by the command number. For example, `!499` will rerun the command with number 499.

# Apex Testing
## Notes on "Get Started with Apex Unit Tests"
The Apex testing framework in Salesforce is crucial for ensuring the quality and reliability of your Apex code. Here's a summary of key points about Apex unit tests:

1. **Purpose**: Apex unit tests verify that your Apex classes and triggers function correctly and meet requirements for deployment.
2. **Development Environment**: Apex code can only be written in sandbox environments or Developer orgs, not in production.
3. **Deployment**: Apex code can be deployed to production orgs from sandboxes, and developers can distribute Apex code to customers via packages on the AppExchange.
4. **Benefits of Apex Unit Tests**:
   - Ensuring Apex code functions as expected.
   - Creating a suite of regression tests to prevent future updates from breaking existing functionality.
   - Meeting code coverage requirements for deployment or distribution.
   - Delivering high-quality apps to production users and package subscribers, enhancing trust and productivity.
5. **Code Coverage Requirement**: Before deployment, at least 75% of Apex code must be covered by tests, and all tests must pass. Triggers must also have some coverage.
6. **Test Method Syntax**: Test methods are defined using the `@isTest` annotation. They have the following syntax:

```apex
@isTest
static void testName() {
  // code_block
}
```
The `@isTest` annotation denotes a test method. Visibility modifiers (`public`, `private`) are omitted as they don't affect the testing framework's access to test methods.
7. **Test Classes**: Test methods must be defined within test classes, which are annotated with `@isTest`. Test classes can be private or public, depending on their purpose.
   
```apex
@isTest
private class MyTestClass {
  @isTest static void myTest() {
    // code_block
  }
}
```
Private test classes are for unit testing, while public test classes are typically used for test data factory classes.
Apex unit tests are essential for maintaining the quality and reliability of your Salesforce applications, ensuring they perform as expected and meet the needs of users and stakeholders.

**Note:**
  - Whenever you modify your Apex code, rerun your tests to refresh code coverage results.
  - A known issue with the Developer Console prevents it from updating code coverage correctly when running a subset of tests. To update your code coverage results, use Test | Run All rather than Test | New Run.

 ## Notes on "Test Apex Triggers"
 - The test method contains the Test.startTest() and Test.stopTest() method pair, which delimits a block of code that gets a fresh set of governor limits.
 - In this test, test-data setup uses two DML statements before the test is performed.
 - To test that Apex code runs within governor limits, isolate data setup’s limit usage from your test’s.
 - To isolate the data setup process’s limit usage, enclose the test call within the Test.startTest() and Test.stopTest() block.
 - Also use this test block when testing asynchronous Apex. For more information, see Using Limits, startTest, and stopTest.

 ```apex
@isTest
private class TestAccountDeletion {
  @isTest
  static void TestDeleteAccountWithOneOpportunity() {
    // Test data setup
    // Create an account with an opportunity, and then try to delete it
    Account acct = new Account(Name='Test Account');
    insert acct;
    Opportunity opp = new Opportunity(
      Name=acct.Name + ' Opportunity',
      StageName='Prospecting',
      CloseDate=System.today().addMonths(1),
      AccountId=acct.Id);
    insert opp;
    // Perform test
    Test.startTest();
      Database.DeleteResult result = Database.delete(acct, false);
    Test.stopTest();
    // Verify
    // In this case the deletion should have been stopped by the trigger,
    // so verify that we got back an error.
    System.assert(!result.isSuccess());
    System.assert(result.getErrors().size() > 0);
    System.assertEquals('Cannot delete account with related opportunities.',
      result.getErrors()[0].getMessage());
  }
}
```
## Notes on "Create Test Data for Apex Tests"

To refactor the previous test method and utilize the `TestDataFactory` class for test data creation, follow these steps:

1. **Utility test Class**:
   - In the Developer Console, navigate to File | New | Apex Class.
   - Enter "TestDataFactory" for the class name and click OK.
   - Replace the default class body with the provided code snippet, which defines a static method `createAccountsWithOpps()` for generating test account and opportunity records.

```apex
@isTest
public class TestDataFactory {
  public static List<Account> createAccountsWithOpps(Integer numAccts, Integer numOppsPerAcct) {
    List<Account> accts = new List<Account>();
    for(Integer i=0;i<numAccts;i++) {
      Account a = new Account(Name='TestAccount' + i);
      accts.add(a);
    }
    insert accts;
    List<Opportunity> opps = new List<Opportunity>();
    for(Integer j=0;j<numAccts;j++) {
      Account acct = accts[j];
      // For each account just inserted, add opportunities
      for(Integer k=0;k<numOppsPerAcct;k++) {
        opps.add(new Opportunity(Name=acct.Name + ' Opportunity ' + k,
          StageName='Prospecting',
          CloseDate=System.today().addMonths(1),
          AccountId=acct.Id));
      }
    }
    // Insert all opportunities for all accounts.
    insert opps;
    return accts;
  }
}
```

# Org Development Model
## Notes on "Plan for Changes to Your Org"

Moving to org development is a strategic decision that can greatly enhance the development process by introducing better collaboration, version control, and deployment practices. Here's a breakdown of the steps and considerations involved in transitioning to org development:

### Prepare the Release Environments
As a team, you can choose to use sandboxes for each step of the application lifecycle.

1. **Develop and Test**:
   - **Environment**: Developer Sandbox
   - **Purpose**: Individual team members work on their assigned customizations in separate Developer sandboxes.
   - **Characteristics**: Developer sandboxes are isolated environments where developers can experiment and iterate without affecting each other's work. They contain no production data, providing a clean slate for development and testing.

2. **Build Release**:
   - **Environment**: Developer Pro Sandbox
   - **Purpose**: Integration of customizations from individual Developer sandboxes into a shared environment.
   - **Characteristics**: Developer Pro sandboxes are used for integrating and testing customizations from multiple developers. They provide a collaborative environment for consolidating changes and resolving any conflicts. While they don't contain production data by default, testing data can be seeded to simulate real-world scenarios.

3. **Test Release**:
   - **Environment**: Partial Sandbox
   - **Purpose**: User-acceptance testing (UAT) to validate customizations before deployment to production.
   - **Characteristics**: Partial sandboxes replicate the production environment without including actual production data. They are ideal for conducting comprehensive testing and ensuring that customizations meet business requirements and user expectations. UAT helps identify any issues or discrepancies that need to be addressed before the release.

4. **Release**:
   - **Environment**: Full Sandbox
   - **Purpose**: Training users and final validation before deployment to production.
   - **Characteristics**: Full sandboxes provide an exact replica of the production environment, including a copy of production data. They are used for training users on new features and functionalities, as well as conducting final validation to ensure a smooth transition to production. Full sandboxes minimize the risk of data loss or disruption by allowing thorough testing in a controlled environment.

## Notes on "Test and Deploy Changes"
Steps to test and deploy:

1. **Pull the Changes from the Repo**
2. **Authorize the Developer Pro Sandbox**
  - `SFDX: Authorize and Org`
  - Select Sandbox
  - Enter Sandbox alias
3. **Build the Release Artifact**
    - `sf project generate manifest`
      - generates the package.xml
        - Contains all metadata for the deployment of the current changes. 
      - `--help` will give the format for commands
      - `--metadata` will deploy only the new specified metadata components
        - Examples: 
     	  - `--metadata CustomObject:Language_Course_Instructor__c`
          - `--metadata CustomField:Language_Course__c.Course_Instructor__c` 
          - `--metadata ApexTrigger:LanguageCourseTrigger`
          - `--metadata ApexClass:TestLanguageCourseClass` 
      - `--type` generates a destructive manifest file that lists the components to delete 
4. **Test (Partial) the Release Artifact**
    -  `sf project deploy start`
      -  `--help` shows format for command arguments
      -  `--manifest` the path to the release artifact
      -  `--target-org` takes the name of the org sandbox you want to deploy the package.xml to
      -  `--test-level` runs one or more tests depending on the specified level
          -  `RunSpecifiedTests` is the test level that lets you run sepcific tests specified in the argument
            -  `--tests` takes the name of a test to be ran
      -  `sf project deploy start --manifest package.xml --target-org partial-sandbox --test-level RunSpecifiedTests --tests TestLanguageCourseTrigger`
5. **Release to Production**
    - `sf project deploy validate`
      -  `--manifest` the path to the release artifact
      -  `--test-level RunLocalTests` will run all local (regression) tests
    -  `sf project deploy quick  --target-org full-sandbox`
      -  `--job-id <jobID>` the id generated by running `sf project deploy validate`
      -  `--target-org` takes the name of the org sandbox you want to deploy the package.xml to
