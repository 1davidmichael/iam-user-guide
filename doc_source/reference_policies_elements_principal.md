# IAM JSON Policy Elements: Principal<a name="reference_policies_elements_principal"></a>

Use the `Principal` element to specify the user \(IAM user, federated user, or assumed\-role user\), AWS account, AWS service, or other principal entity that is allowed or denied access to a resource\. You use the `Principal` element in the trust policies for IAM roles and in resource\-based policies—that is, in policies that you embed directly in a resource\. For example, you can embed such policies in an Amazon S3 bucket, an Amazon Glacier vault, an Amazon SNS topic, an Amazon SQS queue, or an AWS KMS customer master key \(CMK\)\.

Use the `Principal` element in these ways:

+ In IAM roles, use the `Principal` element in the role's trust policy to specify who can assume the role\. For cross\-account access, you typically specify the identifier of the trusted account\. 
**Note**  
You cannot specify anything other than a 12\-digit account ID when you create a cross\-account role\. However, you can change it to "\*" in the policy editor after you create the role\. If you do this, we strongly recommend that you limit who can access the role through other means, such as a `Condition` element that limits access to only certain IP addresses\. Do not leave your role accessible to everyone\!

+ In resource\-based policies, use the `Principal` element to specify the accounts or users who are allowed to access the resource\. 

Do not use the `Principal` element in policies that you attach to IAM users and groups\. Similarly, you do not specify a principal in the permission policy for an IAM role\. In those cases, the principal is implicitly the user that the policy is attached to \(for IAM users\) or the user who assumes the role \(for role access policies\)\. When the policy is attached to an IAM group, the principal is the IAM user in that group who is making the request\. 

## Specifying a Principal<a name="Principal_specifying"></a>

You specify a principal using the *Amazon Resource Name* \(ARN\) of the AWS account, IAM user, IAM role, federated user, or assumed\-role user\. You cannot specify IAM groups as principals\. When you specify an AWS account, you can use a shortened form that consists of the `AWS:` prefix followed by the account ID, instead of using the account's full ARN\. 

The following examples show various ways in which principals can be specified\.

**Everyone \(anonymous users\)** 

The following are equivalent:

```
"Principal": "*"
```

```
"Principal" : { "AWS" : "*" }
```

**Note**  
In these examples, the asterisk \(\*\) is used as a placeholder for Everyone/Anonymous\. You cannot use it as a wildcard to match part of a name or an ARN\. We also strongly recommend that you do not use a wildcard in the `Principal` element in a role's trust policy unless you otherwise restrict access through a `Condition` element in the policy\. Otherwise, any IAM user in any account can access the role\.

**Specific AWS accounts**

When you use an AWS account identifier as the principal in a policy, the permissions in the policy statement can be granted to all identities contained in that account\. This includes IAM users and roles in that account\. The following examples show different ways to specify an AWS account as a principal\. 

```
"Principal": { "AWS": "arn:aws:iam::AWS-account-ID:root" }
```

```
"Principal": { "AWS": "AWS-account-ID" }
```

You can specify more than one AWS account as a principal, as shown in the following example\.

```
"Principal": { 
  "AWS": [
    "arn:aws:iam::AWS-account-ID:root",
    "arn:aws:iam::AWS-account-ID:root"
  ]
}
```

**Individual IAM user or users**

You can specify an individual IAM user \(or array of users\) as the principal, as in the following examples\. 

**Note**  
In a `Principal` element, the user name is case sensitive\.

```
"Principal": { "AWS": "arn:aws:iam::AWS-account-ID:user/user-name" }
```

```
"Principal": {
  "AWS": [
    "arn:aws:iam::AWS-account-ID:user/user-name-1", 
    "arn:aws:iam::AWS-account-ID:user/UserName2"
  ]
}
```

When you specify users in a `Principal` element, you cannot use a wildcard \(`*`\) to mean "all users"\. Principals must always name a specific user or users\. 

**Important**  
If your `Principal` element in a role trust policy contains an ARN that points to a specific IAM user, then that ARN is transformed to the user's unique principal ID when the policy is saved\. This helps mitigate the risk of someone escalating their privileges by removing and recreating the user\. You don't normally see this ID in the console, because there is also a reverse transformation back to the user's ARN when the trust policy is displayed\. However, if you delete the user, then the relationship is broken\. The policy no longer applies, even if you recreate the user\. That's because the new user has a new principal ID that does not match the ID stored in the trust policy\. When this happens, the principal ID shows up in the console because AWS can no longer map it back to a valid ARN\. The result is that if you delete and recreate a user referenced in a trust policy's `Principal` element, you must edit the role to replace the now incorrect principal ID with the correct ARN\. The ARN is once again transformed into the user's new principal ID when you save the policy\.

**Federated users \(using web identity federation\)** 

```
"Principal": { "Federated": "cognito-identity.amazonaws.com" }
```

```
"Principal": { "Federated": "www.amazon.com" }
```

```
"Principal": { "Federated": "graph.facebook.com" }
```

```
"Principal": { "Federated": "accounts.google.com" }
```

**Federated users \(using a SAML identity provider\)**

```
"Principal": { "Federated": "arn:aws:iam::AWS-account-ID:saml-provider/provider-name" }
```

**IAM role**

```
"Principal": { "AWS": "arn:aws:iam::AWS-account-ID:role/role-name" }
```

**Important**  
If your `Principal` element in a role trust policy contains an ARN that points to a specific IAM role, then that ARN is transformed to the role's unique principal ID when the policy is saved\. This helps mitigate the risk of someone escalating their privileges by removing and recreating the role\. You don't normally see this ID in the console, because there is also a reverse transformation back to the role's ARN when the trust policy is displayed\. However, if you delete the role, then the relationship is broken\. The policy no longer applies, even if you recreate the role because the new role has a new principal ID that does not match the ID stored in the trust policy\. When this happens, the principal ID shows up in the console because AWS can no longer map it back to a valid ARN\. The end result is that if you delete and recreate a role referenced in a trust policy's `Principal` element, you must edit the role to replace the now incorrect principal ID with the correct ARN\. The ARN will once again be transformed into the role's new principal ID when you save the policy\.

**Specific assumed\-role user**

```
"Principal": { "AWS": "arn:aws:sts::AWS-account-ID:assumed-role/role-name/role-session-name" }
```

**AWS service**

When you create a trust policy for an IAM role that will be assumed by an AWS service, you typically specify the principal using a friendly name for that service, as in the following example\.

```
"Principal": {
  "Service": [
    "ec2.amazonaws.com",
    "datapipeline.amazonaws.com" 
  ]
}
```

Some AWS services support additional options for specifying a principal\. For example, Amazon S3 lets you use a canonical user in a format like this:

```
"Principal": { "CanonicalUser": "79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be" }
```

## More Information<a name="Principal_more-info"></a>

For more information, see the following:

+ [Bucket Policy Examples](http://alpha-docs-aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html) in the *Amazon Simple Storage Service Developer Guide*

+ [Example Policies for Amazon SNS](http://alpha-docs-aws.amazon.com/sns/latest/dg/UsingIAMwithSNS.html#ExamplePolicies_SNS) in the *Amazon Simple Notification Service Developer Guide*

+ [Amazon SQS Policy Examples](http://alpha-docs-aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/SQSExamples.html) in the *Amazon Simple Queue Service Developer Guide*

+ [Key Policies](http://alpha-docs-aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*

+ [Account Identifiers](http://alpha-docs-aws.amazon.com/general/latest/gr/acct-identifiers.html) in the *AWS General Reference*

+ [About Web Identity Federation](id_roles_providers_oidc.md)