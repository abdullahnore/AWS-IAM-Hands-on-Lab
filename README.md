
Objective

This lab demonstrates how AWS IAM policies can be designed to enforce least-privilege access using resource tags, a common real-world cloud security pattern.

Instead of granting broad EC2 permissions, access is restricted based on environment type (development vs production).

Lab Scenario

Two EC2 instances were created:

Production (Env=production)

Development (Env=development)

An IAM user represents an intern / junior engineer

Access is granted only to development resources

Production resources remain protected

This mirrors how enterprises safely onboard interns and junior staff.

Architecture

AWS Services Used

IAM (Users, Groups, Policies)

EC2

Resource Tags

Region

Asia Pacific (Mumbai)

📸 [Add architecture or EC2 overview screenshot here]

EC2 Setup

Two EC2 instances were launched:

prod-ec2 → tagged Env=production

dev-ec2 → tagged Env=development

Tags are used as the security control point, not instance names.

📸 [Add EC2 instances + tags screenshot here]

IAM Design
User & Group

IAM User: intern-ganesh

IAM Group: intern-lab

Permissions are assigned to the group, not directly to the user

This follows AWS best practices and scales cleanly.

📸 [Add IAM user & group screenshot here]

Custom IAM Policy – Dev-limits
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:*",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/Env": "development"
        }
      }
    },
    {
      "Effect": "Allow",
      "Action": "ec2:Describe*",
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": [
        "ec2:DeleteTags",
        "ec2:CreateTags"
      ],
      "Resource": "*"
    }
  ]
}

Policy Logic

Full EC2 access only for Env=development

Read-only visibility across EC2

Explicit deny prevents tag manipulation (no privilege escalation)

📸 [Add policy JSON screenshot here]

Validation & Errors (Expected Behavior)
Production Instance

❌ ec2:StopInstances → Access Denied

Reason: instance tagged as production

S3 & Service Catalog

❌ Access denied due to no permissions assigned

These failures confirm the policy is working as intended.

📸 [Add access denied error screenshot here]

Security Takeaways

Tag-based IAM conditions enable fine-grained control

Explicit denies are critical for security

Groups > individual user permissions

Access-denied errors often mean good security, not misconfiguration
