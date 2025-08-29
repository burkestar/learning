# AWS Identity and Access Management (IAM)

[IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) (Identity and Access Management) provides a way to securely control access to AWS resources - who is authenticated (signed in) and authorized (has permissions) to use resources.

- IAM is a **free service**, so you are not charged to use it with your AWS account.
    
- Logging into the Web Console uses the IAM username (email) and password.
    
    - Can be protected using **Multi-factor Authentication** (MFA). Users need to possess an MFA device that gives them a time-expiring code that is used in addition to their password when logging in.
        
    - When authenticating using the command line (CLI) or SDK (used by applications), **access tokens** are used instead. The access tokens are tied to the IAM identity and consist of an **Access Key** and **Secret Key**.
        
- The **root user** is created with a new AWS account and has access to everything. Best practice is to use this only to create your first IAM user, and then securely store it somewhere for future.
    
- **Granular permissions** - different permissions for different people to different resources
    
- **Audit logs** to see who accessed which resources are tied to the IAM identity, and searchable in **CloudTrail**.
    
- IAM is **eventually consistent**, which is important to keep in mind when making changes to ensure they are propagated when used by production applications.
    
- **Federation**
    
    - Allows access to AWS resources using other **Identity Providers (IdP)** such as Active Directory or a **Single-Sign On (SSO) provider** like Okta.
        
    - AWS provides an [AWS SSO](https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.html) service:
        
        - SSO access to your AWS accounts and cloud applications.
            
            - Users can login to 3rd party services (e.g. Github) using AWS SSO and their AWS user authentication.
                
            - Users can login to AWS Console using their 3rd party SSO service (e.g. OKTA) instead of AWS IAM username and password.
                
                - SAML (Security Assertion Markup Language) is used to authenticate against an external identify provider.
                    
        - Create and manage users and groups in AWS SSO (or sync from a 3rd party Identity Provider).
            
            - Since SAML doesn’t provide the ability to discover or sync user identities, the SCIM (System for Cross-domain Identity Management) protocol is used by AWS SSO to sync your identities (users and groups) with Identity Providers.
                
- For _cross-account permissions,_ you can use **IAM Roles**, **Resource-based Policies** or **Access Control Lists (ACL)**.
    
    - [AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html) provides support for managing IAM permissions across multiple accounts.
        

---

## Concepts

|   |   |
|---|---|
|**Term**|**Definition**|
|**Resources**|IAM manages the following objects:<br><br>- **User**<br>    <br>- **Group**<br>    <br>- **Role**<br>    <br>- **Policy**<br>    <br>- **Identity Provider** (IdP)|
|**Identities**|The IAM **Resource** object corresponding to **Users**.<br><br>- Users can have one or more **Policies** attached.<br>    <br>- Users can belong to one or more **Groups**.<br>    <br>- The Identity has a username and password for authentication.<br>    <br>- The IAM User is associated with one and only one AWS **Account**.<br>    <br>- If the IAM User is used by an application, then its called a **Service Account**.<br>    <br><br>Sample Amazon Resource Name (ARN):  <br>arn:aws:iam::account-ID-without-hyphens:user/Richard|
|**Groups**|A set of **Users**. Can have one or more **Policies** attached.|
|**Entities**|IAM resource object used for authentication.<br><br>- **Users**<br>    <br>    - IAM Users<br>        <br>    - **Federated Users**<br>        <br>- Assumed **Roles**|
|**Federated Users**|An IAM **Resource** for an **Entity** corresponding to some user _that is not an IAM User_, but an **Identity** managed (_and authenticated_) using a 3rd party **Identity Provider**. The Federated User can _assume_ one or more **Roles** that have an attached **Policy**.|
|**Roles**|A type of **Entity** that can have permissions granted by an attached **Policy** and which can be _assumed_ by multiple users.<br><br>- The Role does not have any authentication information itself (so it is not an **Identity**)<br>    <br>- Roles can be assumed by IAM Users from multiple accounts, AWS Services, and **Federated Users** that authenticate with a 3rd party Identity Provider.<br>    <br>- **Role Chaining** refers to a user that assumes Role A which allows then assuming another Role B.<br>    <br>- **Service-linked Roles** are automatically created by some AWS Services.|
|**Action**|The set of _**operations**_ that an AWS **Service** provides for a given **Resource**.|
|**Principals**|A person or application _that is making a **Request**_ for an **Action** on an AWS **Resource**.<br><br>Authenticated as the AWS account root user, or an IAM **Entity**.|
|**Policy**|Used for authorizing **Requests**.<br><br>Stored as JSON documents in AWS and specifies the permissions _granted_ to **Principal** **Entities**.<br><br>- **Identity-based Policies** are used for controlling what Actions can be performed on AWS Resources within in an account by one or more **Users**, **Groups** or **Roles** that have the policy _attached_.<br>    <br>    - **Managed Policies** can be attached to _multiple_ Users, Groups and Roles. They are either **AWS Managed Policies** or **Customer Managed Policies** that are defined by the Organization.<br>        <br>    - **Inline Policies** are embedded directly into a single User, Group or Role. Not recommended to use.<br>        <br>- **Resource-based Policies** control what **Actions** a specified **Principal** can perform on a given **resource** under certain conditions.<br>    <br>    - They are all Inline Policies and not Managed policies since they are policies specific to an individual Resource.<br>        <br>    - Can be used for _granting cross-account access_ by specifying an **entire Account** (based on its ARN identifier) or a set of IAM **Entities** from the other account as the **Principals** within the **Policy**.<br>        <br>    - The IAM Service provides a **Trust Policy** attached to an IAM **Role** that defines which **Principal** **Entities** (Accounts, Users, Roles, Federated Users) can _assume_ the **Role**.<br>        <br>        - Since IAM Roles are _both an Identity as well as a Resource that supports resource-based policies_, _both a **Trust Policy** and an **identity-based policy** must be attached to an IAM role_.|
|**Request**|An object used to evaluate and authorize a **Principal** to perform an action on a **Resource**.<br><br>The **Principal** must be _**authenticated**_ (using one of the authentication methods) and _**authorized**_ to perform the action, where authorization is determined using **Policies** _attached_ to the **Principal**.<br><br>Requests are _denied by default_, so only authorized if every part of request is allowed by permissions **policy**.<br><br>To make a request in a different account, a **Policy** in the other account must allow access to the **Resource** and the IAM **Entity** making the **Request** must have an **Identity-based Policy** that allows it.<br><br>Consists of:<br><br>- **Actions** or operations to be performed<br>    <br>- **Resources** that will be acted upon<br>    <br>- **Principal** making the request and has attached **Policies** granting access to **Resources**.<br>    <br>- **Environment data** where the Request originated from (for auditing)<br>    <br>- **Resource data** to identify the specific resource (e.g. the name of the database table)|
|**Explicit Deny**|If a single permission policy includes a denied action, the entire request is denied.|
|**Attribute-based Access Control (ABAC)**|Authorization strategy that defines permission based on attributes (“tags”). Tags are attached to IAM Principals and to AWS Resources.<br><br>IAM traditionally uses _Role-based Access Control (RBAC)_ with Entities that are assigned permissions by an attached Policy. If a person’s job function changes, you assign their IAM user Identity to a new Role.<br><br>Instead, attribute-based access control uses tags to manage access. It makes it easy to grant access to an individual Resource by assigning a Tag, rather than needing to update the Policies for the associated Roles. As a result, fewer policies are needed and teams can manage access to resources themselves.|

---

## Policies

### Example

Allows the user to perform all Amazon DynamoDB actions (dynamodb:*) on the Books table in the 123456789012 account within the us-east-2 Region

---

## Best Practices

- Granting Least Privilege
    
- Only use the Root Account to create IAM users.
    
- Use Groups to assign permissions to sets of users, to simplify policy management.
    
- Use Roles for applications that run on EC2 instances
    
- Use Roles to delegate permissions
    
- Do not share access keys
    
- Rotate credentials regularly
    

See AWS IAM [**Security Best Practices**](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html).