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

## Introduction to Database Triggers in Salesforce

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
