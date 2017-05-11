# Cisco cloud corner on AWS DynamoDB
Amazon DynamoDB is a fully managed NoSQL database service provided by AWS

This workshop has been inspired by the aCloudGuru course [Amazon DynamoDB - From Beginner to Pro](https://acloud.guru/course/aws-dynamodb/dashboard)

The GIT repository of **Amazon DynamoDB - From Beginner to Pro** course is available for free:
https://github.com/acantril/aCloudGuru-DynamoDB

See also:
- [What Is Amazon DynamoDB?](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)
- [Working with Tables in DynamoDB](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithTables.html)
- [Working with Items in DynamoDB](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithItems.html#WorkingWithItems)

## DynamoDB Consistency Model
- SSD Storage
- Consistent, reliable low latency reads and writes
- Every data block is stored three times
- Support **Eventual consistency** (default) or **Strong consistency** for read operations

## Prerequisites:
- You must have a valid AWS account
- You must [have downloaded and configured the AWS CLI](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Tools.CLI.html#Tools.CLI.DownloadingAndRunning)

## NoSQL Fundamentals
- Schema-less
- No relationships (except for Graph DB)
- No Structured Query Language (SQL)
- [ACID (**A**tomicity, **C**onsistency, **I**solation, **D**urability) vs BASE (**B**asically **A**vailable, **S**oft state, **E**ventual consistency)](http://www.dummies.com/programming/big-data/hadoop/acid-versus-base-data-stores/)
- Horizontal Scalability
- Different categories of NoSQL databases:
  - key/value
  - Document DB (CouchDB, MongoDB)
  - Column Family (For analytics)
  - Graph Style DB (Importance of Relationships)

## Creating your first DynamoDB Table with the AWS Console
DynamoDB supports two different kinds of primary keys:

- Partition Key
- Partition Key and Sort Key


1. From the AWS console, go to **Database** -> **DynamoDB**
1. Select **Create Table**
  - Table name: **CloudCorner**
  - Primary key: **UserID** **Number**
  - Click **Create**

  Wait until the **CloudCorner** table has been created

1. Select the **CloudCorner** table
1. Go to folder **Items** and select **Create Item**
1. Enter:
    - UserId: **1**
1. Click on + -> Append -> String. Enter:
    - LastName: **Enter your last name**
1. Click **Save**

  Congratulations: You have inserted your first item in a DynamoDB table
1. Select **Create Item**
1. Enter:
    - UserId: **2**
1. Click on + -> Append -> String. Enter:
    - FirstName: **Enter the first name of your colleague**
1. Click **Save**

  You have now created a second item with a different field name
1. Select item 1 and select **Actions** -> **Edit**
1. Click on + -> Append -> String. Enter:
    - FirstName: **Enter your first name**
1. Click **Save**
1. Select item 2 and select **Actions** -> **Edit**
1. Click on + -> Append -> String. Enter:
    - LastName: **Enter the last name of your colleague**
1. Click **Save**

  You have now added the missing fields into the previous records

## Creating a DynamoDB Table with CloudFormation
1. From the AWS console, go to **Management Tools** -> **CloudFormation**
1. Select **Create New Stack**
1. Click on **Choose file** into the **Choose a template** section and select the [weatherstationtable.json](resources/weatherstationtable.json) file in the resource directory of this repository
1. Click **Next**
1. Enter **weatherstation** in the Stack Name and Click **Next**
1. Click **Next**
1. Click **Create**
1. From the AWS console, go to **Database** -> **DynamoDB**
  Wait until the **weatherstation_data** table has been created1.
1. Select the **weatherstation_data** table and check that it contains fields **station_id** and **dateandtime**

## Use the DynamoDB CLI to interact with your tables
1. Open a console
1. Try the different commands available in the [./resources/COMMANDS_USED.TXT](resources/COMMANDS_USED.TXT) available in this repository

## Controlling Table performance
- Read Capacity Units (RCU): up to 4KB data
- Write Capacity Units (WCU) : up to 1KB data
- Both are allocated on a PER SECOND basis
- Operations are always rounded to a multiple of 1KB or 4KB minimum
- 3KB Read per second = 3KB / 4KB = 0.75 = 1 RCU
- 1.5KB Write per second = 1.5KB / 1KB = 1.5 = 2 WCU
- Eventually consistent read use HALF of the above RCU

Capacities can be modified through :
- The Capacity tab
- The CLI - Update-table
```bash
  aws dynamodb update-table --table-name weatherstation_data --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1
```
- SDK API - Update-Table

## Conditional and Update expressions
### Update expressions
Perform server side database manipulations
Update expressions consists of 4 operations:
- REMOVE
- SET
- ADD
- DELETE

### Conditional expressions
Available on write operations (PUT, UPDATE and DELETE)
Perform an action depending on a condition:
- Attribute **Exists** or **Not_Exists**
- Attribute has a specific **Type**
- Attribute **begins_with**, **contains** or has **size X**
Use traditional comparators: =, <>, <, >, <=, >=
And keywords **BETWEEN** or **IN**
Can be combined with **Update Expressions**

## Introduction to Partitions
- Underlying **storage** and **processing** nodes of dynamodb
- Initially **One table** equals **One partition**
- You can't  **directly see** the number of partitions
- You don't **directly control** the number of partitions
- A partition can store **10 GB** of data

### Advanced Key Design
- Partition keys need to be **unique and non null**
- Partition keys should have **many unique** values
- Don't use the **sort key to fake this** !!
- Uniform **read** and **write** across **all partition key values**
- Uniform **temporal** read and write pattern
- If you don't have a suitable key - **make one**
- Aim not to mix **HOT** and **COLD** data
