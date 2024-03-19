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
    - WITH Dvision
        ```sosl
            FIND {Cloud Kicks} RETURNING Account (Name, Industry) WITH DIVISION = 'Global'
        ```
      - WITH DATA CATEGORY
        ```sosl
            FIND {race} RETURNING KnowledgeArticleVersio(Id, Title WHERE PublishStatus='online' and language='en_US') WITH DATA CATEGORY Location__c AT America__c
        ```
      - WITH NETWORK
        ```sosl
            FIND {first place} RETURNING User (Id, Name), FeedItem (id, ParentId WHERE CreatedDate = THIS_YEAR Order by CreatedDate DESC) WITH NETWORK = '00000000000001'
        ```
      - WITH PRICEBOOK
        ```sosl
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
