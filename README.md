<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Encrypt Data with AWS KMS

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-kms)

**Author:** siddharthpk nextwork  
**Email:** siddharthpknextwork@gmail.com

---

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-security-kms_w0x1y2z3)

---

## Introducing Today's Project!

In this project, I will demonstrate how to use AWS Key Management Service (KMS) to create encryption keys and apply them to encrypt a DynamoDB database. The goal is to:

Create a Customer Master Key (CMK) using AWS KMS.

Configure DynamoDB to use the KMS key for encryption at rest.

Add and retrieve data from the DynamoDB table to validate encryption is working as expected.

Observe how AWS restricts unauthorized access by attempting access from users or roles without encryption permissions.

Grant a specific IAM user access to use the encryption key and perform allowed operations on the DynamoDB table.

By the end of this project, we will have a working, encrypted DynamoDB solution that demonstrates AWS’s native encryption capabilities and access control using KMS and IAM.

### Tools and concepts

Services I used include AWS Key Management Service (KMS) for creating and managing encryption keys, DynamoDB as the encrypted NoSQL database, and IAM for managing user permissions and access controls.

Key concepts I learnt include encryption and decryption processes, transparent data encryption in DynamoDB, the difference between symmetric and asymmetric keys, the importance of customer-managed keys for fine-grained control, how KMS key policies work alongside IAM policies to enforce security, and how encryption adds an essential layer of protection beyond traditional access controls.

### Project reflection

This project took me approximately a few hours to complete. The most challenging part was configuring the KMS key policies correctly to balance security and usability, especially ensuring the right permissions were granted without overexposing access. It was most rewarding to see the encryption in action—specifically, how AWS KMS seamlessly protects data while allowing authorized users to access it transparently, giving me confidence in building secure applications.

I did this project today because I wanted to gain hands-on experience with AWS encryption and key management, especially how KMS works with DynamoDB to protect data securely. This project helped me understand the practical aspects of setting up encryption, managing access, and testing security controls in a real environment.

Yes, the project met my goals by allowing me to successfully create a customer-managed key, encrypt data at rest in DynamoDB, control user permissions through IAM and KMS policies, and verify how encryption prevents unauthorized access. It gave me confidence in applying these concepts to real-world cloud security challenges.

---

## Encryption and KMS

Encryption is the process of transforming readable data (plaintext) into an unreadable format (ciphertext) using mathematical algorithms and encryption keys. Companies and developers do this to protect sensitive information from unauthorized access, especially when data is stored (at rest) or transmitted (in transit).

Encryption keys are secret values used in the encryption and decryption process—only someone with the correct key can convert the encrypted data back into its original form. These keys can be symmetric (same key for encryption and decryption) or asymmetric (a public key to encrypt and a private key to decrypt). Proper key management, such as through AWS KMS, is essential to ensure the security and control of encrypted data.

AWS KMS is Amazon Web Services' Key Management Service, a fully managed service that allows you to create, manage, and control encryption keys used to protect your data across AWS services and applications. It supports both symmetric and asymmetric encryption and integrates seamlessly with services like DynamoDB, S3, EBS, and Lambda.

Key management systems are important because they ensure secure handling of encryption keys, including generation, storage, rotation, and access control. Without proper key management, even strong encryption can be compromised. AWS KMS provides centralized control, fine-grained permissions using IAM, automatic key rotation, audit logging with CloudTrail, and built-in redundancy—all of which help maintain data confidentiality, integrity, and compliance with security standards.

Encryption keys are broadly categorized as symmetric and asymmetric. In symmetric encryption, the same key is used to encrypt and decrypt data, whereas in asymmetric encryption, a public key encrypts the data and a private key decrypts it.

I set up a symmetric key because it is simpler, faster, and more efficient for encrypting large volumes of data, such as that stored in a DynamoDB table. AWS services like DynamoDB natively support symmetric keys through AWS KMS, making integration seamless. Symmetric keys are also easier to manage in scenarios where encryption and decryption are handled within the same environment, which fits our use case.

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-security-kms_a2b3c4d5)

---

## Encrypting Data

My encryption key will safeguard data in DynamoDB, which is a fully managed NoSQL database service provided by AWS. DynamoDB is designed for high performance, scalability, and low-latency data access, making it ideal for applications that require fast and predictable performance at any scale.

It stores data in key-value and document formats and supports features like automatic scaling, backup and restore, in-memory caching (with DynamoDB Accelerator), and fine-grained access control. By integrating it with AWS KMS, we ensure that all data stored in the table is encrypted at rest, protecting sensitive information from unauthorized access and meeting security best practice.

The different encryption options in DynamoDB include:

AWS owned key – Managed entirely by AWS, with no visibility or control for the customer. It’s the default option and requires no setup.

AWS managed key (AWS-managed CMK) – Created and managed by AWS on your behalf, but offers limited visibility and control. You can view usage in CloudTrail but can’t manage key policies or rotation.

Customer managed key (CMK) – Created and fully managed by you using AWS KMS. You have complete control over the key, including its permissions, rotation, and usage tracking.

Their differences are based on the level of control, visibility, and customization available to the customer.

I selected the Customer managed key named nextwork-kms-key because it allows me to define key policies, enable key rotation, audit usage, and control who can encrypt and decrypt data. This gives us greater security, flexibility, and accountability compared to the other options.

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-security-kms_q8r9s0t1)

---

## Data Visibility

Rather than controlling who has access to the key, KMS manages user permissions by using key policies and IAM policies to define exactly who can perform specific actions with the key, such as encrypting, decrypting, or managing the key itself.

Key policies are the primary mechanism and act as a resource-based policy attached directly to the KMS key, specifying which principals (users, roles, or services) have what kind of access. Additionally, IAM policies can grant or restrict permissions to use the key, but access is only allowed if both the key policy and the IAM policy permit the action.

This layered permission model ensures fine-grained control over who can use or administer encryption keys, preventing unauthorized access to sensitive data even if users have broader service-level permissions.

Despite encrypting my DynamoDB table, I could still see the table's items because I have the necessary permissions to use the KMS key (nextwork-kms-key) for decryption.

DynamoDB uses transparent data encryption, which means that data is automatically encrypted when written to disk and automatically decrypted when accessed by an authorized user or application.

As a result, the encryption process is seamless—you don’t need to manually decrypt the data, and it appears in plain text to users with the correct permissions. However, if an unauthorized user without access to the encryption key tries to access the table, they will be denied, even if they have read access to the table itself.

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-security-kms_c0d1e2f3)

---

## Denying Access

I configured a new IAM user to test access restrictions by allowing full interaction with the DynamoDB table while denying encryption key usage. The permission policies I granted this user are AmazonDynamoDBFullAccess to allow full access to the DynamoDB table, but not any permissions to use the KMS key (nextwork-kms-key) for encryption or decryption operations.

After accessing the DynamoDB table as the test user, I encountered an "Access denied" error related to KMS because the user does not have permission to use the nextwork-kms-key to decrypt the table's items. This was confirmed when I saw the "Access Denied" banner appear in the "Explore items" tab while attempting to view the data.

This confirmed that even though the user has full DynamoDB access, AWS KMS prevents them from reading encrypted data without explicit key permissions, effectively enforcing strict encryption-based access control.

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-security-kms_w0x1y2z3)

---

## EXTRA: Granting Access

To let my test user use the encryption key, I added the test user as a key administrator. My key's policy was updated to grant the test user the same privileges as the administrator, allowing them to perform cryptographic operations like encrypting and decrypting data with the key. This change ensured the test user had the necessary permissions to access encrypted items in the DynamoDB table.

Using the test user, I retried accessing the DynamoDB table data. I observed that I could now see the data in the table, which the test user was not authorized to see before. This confirmed that granting the test user permissions to use the KMS key successfully enabled them to decrypt and access the encrypted data, proving the key policy update worked as intended.

ncryption secures data instead of just controlling who can access it by making the data unreadable without the proper decryption key, protecting it even if someone bypasses other security measures. I could combine encryption with other access control tools like IAM policies, resource-based policies, and network controls to create multiple layers of defense, ensuring that only authorized users can access and understand sensitive data while blocking unauthorized access at both the data and service levels.

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-security-kms_feffb2fb8)

---

---
