# AWS Organization Setup Guide

This guide walks you through setting up an AWS account structure where the management account is used solely for organization-level management, while another account is used for applying Service Control Policies (SCPs), tag policies, and account-specific policies.

## Steps

### 1. Create a New AWS Account
- **Objective**: Set up a new AWS account separate from the management account. The management account will only manage organization-level restrictions.
- **Steps**:
  1. Sign in to the AWS Management Console using your management account.
  2. Navigate to the AWS Organizations console.
  3. Under the **Accounts** tab, select **Create Account**.
  4. Provide the necessary details (email address, account name) for the new account.
  5. Follow the prompts to complete the account creation process.

### 2. Attach SCPs and Tag Policies to the New Account
- **Objective**: Apply Service Control Policies (SCPs) and tag policies to the new account so they are enforced for all users under this account.
- **Steps**:
  1. In the AWS Organizations console, select the newly created account.
  2. Go to the **Policies** section.
  3. Attach relevant SCPs and tag policies to the new account.
  4. Confirm that the policies are successfully attached.

### 3. Switch Role to the New Account
- **Objective**: Switch to the new account to create and manage account-specific policies.
- **Steps**:
  1. In the AWS Management Console, go to the **IAM** (Identity and Access Management) section.
  2. Choose **Roles** and then select **Switch Role**.
  3. Provide the account ID of the new account and the role name to switch to.
  4. After switching, proceed to create and manage account-level policies as needed.

### 4. Apply SCP to Restrict Actions Based on the Absence of Tags
- **Objective**: Restrict the creation of certain resources if they are not tagged appropriately using an SCP.
- **Steps**:
  1. Use the following SCP JSON to deny the creation of an RDS instance if the `Project` tag is not present:
  
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Sid": "DenyCreateRDSWithoutProjectTag",
           "Effect": "Deny",
           "Action": "rds:CreateDBInstance",
           "Resource": "*",
           "Condition": {
             "StringNotLike": {
               "aws:RequestTag/Project": "*"
             }
           }
         }
       ]
     }
     ```
  2. Attach this SCP to the new account using the AWS Organizations console under the **Policies** section.

### 5. Use Lambda to Enforce 'Created By' Tag on Resources
- **Objective**: Automatically tag resources with a 'Created By' tag using a Lambda function triggered by CloudTrail logs.
- **Steps**:
  1. Set up a CloudTrail to log all resource creation events.
  2. Create a Lambda function that is triggered by CloudTrail logs.
  3. In the Lambda function, implement logic to add the 'Created By' tag to resources that lack this tag.
  4. Deploy and test the Lambda function to ensure it is correctly tagging resources.

---

This setup ensures that the management account remains focused on organization-level restrictions while the new account handles specific policy implementations and resource management.
