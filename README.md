# Fetch Data from DynamoDB with Lambda

## Introducing this Project!

In this project, I set up the data layer of a three-tier web app.

### Services Used

- DynamoDB
- Lambda
- IAM

### Concepts Learnt

- Creating a DynamoDB table and adding table items
- Creating a Lambda function, implementing the logic, and writing a test
- Granting Lambda the access to read from DynamoDB

### Architecture Diagram

![Image](https://github.com/sumeet15n/fetch-from-DynamoDB-with-Lambda/blob/master/Screenshots/SS0.png)

---

## Project Guide

### Step 1: Set up DynamoDB

First, I navigated to DynamoDB and set my region as the one closest to my geographical location (ap-south-1) to minimize the latency (best practice).

I created a table named 'UserData' with partition key 'userId' of type String.

### Step 2: Add an Item in the DynamoDB Table

After the status of the created table became 'Active', I created a table item, switched to JSON view, toggled off DynamoDB JSON, and pasted the below into the editor.

```
{
  "userId": "1",
  "name": "Test User",
  "email": "test@example.com"
}
```

A screenshot is also shown below.

![Image](https://github.com/sumeet15n/fetch-from-DynamoDB-with-Lambda/blob/master/Screenshots/SS1.png)

### Step 3: Create a Lambda Function

I navigated to Lambda and created a Lambda function with 'RetrieveUserData' as the function name and Node.js as the runtime. 

### Step 4: Implement the Lambda Function Logic

In the code editor of the created Lambda function, I entered the below and deployed the Lambda function.

```
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, GetCommand } from "@aws-sdk/lib-dynamodb";

const ddbClient = new DynamoDBClient({ region: 'ap-south-1' }); // Make sure to replace this with your actual AWS region
const ddb = DynamoDBDocumentClient.from(ddbClient);

async function handler(event) {
const userId = String(event.userId);  // Make sure to extract userId from the event
const params = {
        TableName: 'UserData',
        Key: { userId }
    };

    try {
        const command = new GetCommand(params);
        const data = await ddb.send(command);  // Log the raw response from DynamoDB
        console.log("DynamoDB Response:", JSON.stringify(data));
        const { Item } = data;
        if (Item) {
            console.log("User data retrieved:", Item);
            return Item;
        } else {
            console.log("No user data found for userId:", userId);
            return null;
        }
    } catch (err) {
        console.error("Unable to retrieve data:", err);
        return err;
    }
}

export { handler };
```

A screenshot is also shown below.

![Image](https://github.com/sumeet15n/fetch-from-DynamoDB-with-Lambda/blob/master/Screenshots/SS2.png)

### Step 5: Write a Lambda Function Test

I navigated to the 'Test' tab of my created Lambda function and entered the below in the Event JSON.

```
{
  "userId": "1"
}
```

Upon clicking on 'Test', the test ran successfully, but I encountered an error in what the function was expected to achieve, as shown in the below screenshot.

![Image](https://github.com/sumeet15n/fetch-from-DynamoDB-with-Lambda/blob/master/Screenshots/SS3.png)

Although an execution role was created for my Lambda function, I hadn't given it explicit permission to access my DynamoDB table, so DynamoDB did not let Lambda access it, because of which this error was encountered.

### Step 6: Grant Lambda the Read Access to DynamoDB

I navigated to the 'Permissions' tab of my Lambda function, clicked on execution role that was created when I created the Lambda function, and I was redirected to IAM with the role page open. In the role, I attached the policy 'AmazonDynamoDBReadOnlyAccess', as shown in the below screenshot, as this policy gives the access to read from DynamoDB (contains the GetItem permission).

![Image](https://github.com/sumeet15n/fetch-from-DynamoDB-with-Lambda/blob/master/Screenshots/SS4.png)

### Step 7: Retest the Lambda Function

I then ran the Lambda function test again, and I noticed that the error was resolved.

A screenshot of the success message and the successfully retrieved data from DynamoDB is shown below.

![Image](https://github.com/sumeet15n/fetch-from-DynamoDB-with-Lambda/blob/master/Screenshots/SS5.png)

### Step 8: Delete Resources

Finally, I deleted all the created resources after completing this project to avoid any further charges on AWS.

---

## Project Reflection

This project took me approximately 1.5 hours to complete. The most challenging part was troubleshooting the error, and the most rewarding part was see the Lambda test function run successfully.

Big thanks to NextWork (https://www.nextwork.org/) for this project! I highly recommend this platform to anyone who wants to learn DevOps concepts and complete more projects like this one. Happy learning!