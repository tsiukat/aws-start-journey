# Lab: Automation with CloudFormation

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Infrastructure as Code — CloudFormation Stacks, VPC, S3, EC2  

---

## Business Context

Deploying infrastructure manually is error-prone and hard to repeat consistently. **AWS CloudFormation** solves this by defining the entire infrastructure as code in a YAML/JSON template, which can be version-controlled, reviewed, and deployed automatically — even on a schedule.

This lab covers the full lifecycle of a CloudFormation stack:

```
Deploy stack (VPC + SG)
  → Add S3 bucket (stack update)
    → Add EC2 instance (stack update)
      → Delete stack (all resources removed automatically)
```

---

## Task 1 — Deploy a CloudFormation Stack (VPC + Security Group)

Launched a CloudFormation stack from `task1.yaml`, which provisions:

- A **Virtual Private Cloud (VPC)** with a defined CIDR range
- A **Public Subnet** inside the VPC
- A **Security Group** (`AppSecurityGroup`) attached to the VPC

### Template structure overview

| Section | Purpose |
|---|---|
| `Parameters` | Prompts for CIDR ranges (VPC + Subnet); defaults provided |
| `Resources` | Defines the VPC, Subnet, Security Group, Route Table, etc. |
| `Outputs` | Exports the default Security Group ID for reference |

### Steps

1. Downloaded `task1.yaml` and reviewed its structure in a text editor
2. In CloudFormation console → **Create stack** → uploaded the template file
3. Stack name: `Lab`; left CIDR parameters at default values
4. Confirmed custom name acknowledgement → clicked **Create stack**
5. Monitored the **Events** tab until status reached `CREATE_COMPLETE`

> **Screenshot:** CloudFormation console — Events tab showing CREATE_COMPLETE
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/6fa6d6e5-bc3c-4ce9-9b12-327f913bf28c" />


> **Screenshot:** Resources tab — VPC, Subnet, Security Group listed
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/4ae9dc47-fccd-40f6-aaf0-5ab2533b8e40" />



---

## Task 2 — Add an Amazon S3 Bucket to the Stack

Updated the existing template to add an S3 bucket under the `Resources:` section — no properties required, just two lines:

```yaml
Resources:

  # ... existing resources ...

  S3Bucket:
    Type: AWS::S3::Bucket
```

### Update steps

1. Edited `task1.yaml` to include the S3 bucket resource
2. CloudFormation console → selected `Lab` → **Update**
3. Chose **Replace current template** → uploaded the modified file
4. Reviewed the **Change Set** — CloudFormation showed **Add** for the bucket only; all other resources remained unchanged

> **Screenshot:** Change Set preview — S3 bucket marked as "Add", other resources unchanged
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/40c0db3f-b36c-4a5e-b794-e468299b41c9" />



5. Clicked **Update stack** → waited for `UPDATE_COMPLETE`
6. Verified the bucket appeared in the **Resources** tab with a randomly assigned name (avoids naming conflicts)

> **Screenshot:** Resources tab — S3 bucket now listed alongside VPC resources


---

## Task 3 — Add an Amazon EC2 Instance to the Stack

### Step 1: Add the AMI parameter

Added a new parameter to `Parameters:` that dynamically fetches the latest Amazon Linux 2 AMI via SSM Parameter Store:

```yaml
  AmazonLinuxAMIID:
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
```

This approach makes the template **region-agnostic** — no hardcoded AMI IDs needed.

### Step 2: Add the EC2 instance resource

Added the following block under `Resources:`, using `!Ref` to reference resources already defined in the same template:

```yaml
  AppServer:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !Ref AmazonLinuxAMIID
      InstanceType: t3.micro
      SecurityGroupIds:
        - !Ref AppSecurityGroup
      SubnetId: !Ref PublicSubnet
      Tags:
        - Key: Name
          Value: App Server
```

### Update steps

1. Updated `task1.yaml` with both changes above
2. CloudFormation console → `Lab` → **Update** → uploaded revised template
3. Reviewed Change Set — EC2 instance shown as **Add**
4. Clicked **Update stack** → waited for `UPDATE_COMPLETE`

> **Screenshot:** Change Set preview — EC2 instance marked as "Add"

![changeset-ec2](screenshots/05-changeset-ec2.png)

> **Screenshot:** Resources tab — App Server instance listed with Physical ID

![resources-ec2](screenshots/06-resources-ec2.png)

> **Screenshot:** EC2 console — App Server in running state

![ec2-running](screenshots/07-ec2-running.png)

---

## Task 4 — Delete the Stack

CloudFormation tracks all resources it created, so deleting the stack removes everything automatically — no manual cleanup needed.

1. CloudFormation console → selected `Lab` → **Delete** → confirmed **Delete stack**
2. Status changed to `DELETE_IN_PROGRESS`
3. After a few minutes — stack disappeared from the list

> **Screenshot:** CloudFormation console — stack no longer present after deletion

![stack-deleted](screenshots/08-stack-deleted.png)

Verified in respective consoles that the **S3 bucket**, **EC2 instance**, and **VPC** were all removed. ✅

---

## Key Concepts: `!Ref` and Resource Dependencies

CloudFormation uses `!Ref` to link resources within the same template:

```yaml
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC   # ← references the VPC defined above
```

CloudFormation automatically determines the **correct creation order** based on these references — it will never try to create the Route Table before the VPC exists.

---

## Infrastructure Lifecycle Summary

| Task | Action | Result |
|---|---|---|
| Task 1 | Deploy `task1.yaml` | VPC + Subnet + Security Group created |
| Task 2 | Update stack — add S3 | Bucket added; existing resources unchanged |
| Task 3 | Update stack — add EC2 | App Server launched with correct AMI, SG, Subnet |
| Task 4 | Delete stack | All resources automatically removed |

---

## Reflection

In this lab I learned how to:

- **Define infrastructure as code using YAML** — the structure of a CloudFormation template (Parameters → Resources → Outputs) provides a clear and readable way to describe an entire environment; understanding indentation and `!Ref` syntax is essential for writing valid templates
- **Perform incremental stack updates safely** — updating a stack with a new template doesn't redeploy everything from scratch; CloudFormation calculates a Change Set and only modifies what changed, which minimizes risk and downtime during infrastructure updates
- **Use SSM Parameter Store for dynamic AMI resolution** — hardcoding AMI IDs into templates breaks cross-region portability; referencing `/aws/service/ami-amazon-linux-latest/...` makes the template reusable in any AWS region without modification
- **Appreciate the value of automated cleanup** — deleting a single CloudFormation stack tears down every resource it created in the correct dependency order; this eliminates the risk of orphaned resources that accumulate cost and clutter

> Key takeaway: CloudFormation transforms infrastructure from a manual, error-prone process into a repeatable, auditable workflow. The ability to version-control templates, preview changes before applying them, and automatically roll back on failure makes CloudFormation a foundational tool for any team practicing Infrastructure as Code (IaC) on AWS.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
