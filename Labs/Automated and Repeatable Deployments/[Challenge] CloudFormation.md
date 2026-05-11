# Challenge Lab: AWS CloudFormation — VPC + EC2 Instance

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Infrastructure as Code — Writing a CloudFormation Template from Scratch  
**Duration:** ~45 min

---

## Challenge

Write a CloudFormation YAML template that deploys the following stack **without errors**:

| Resource | Details |
|---|---|
| Amazon VPC | Custom CIDR block |
| Internet Gateway | Attached to the VPC |
| Security Group | SSH (port 22) from anywhere (`0.0.0.0/0`) |
| Public Subnet | Within the VPC |
| EC2 Instance | `t3.micro`, Amazon Linux, in the public subnet |

No step-by-step guidance — template written from scratch and iterated until `CREATE_COMPLETE`.

---

## The CloudFormation Template

Created `vpc-ec2-stack.yaml`:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: VPC with Internet Gateway, Security Group, Subnet, and EC2 instance

Parameters:
  AmiId:
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
    Description: Amazon Linux 2 AMI (resolved automatically per region)

Resources:

  # ── VPC ──────────────────────────────────────────────────────────────
  LabVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
      Tags:
        - Key: Name
          Value: Lab VPC

  # ── Internet Gateway ──────────────────────────────────────────────────
  LabIGW:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: Lab IGW

  AttachIGW:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref LabVPC
      InternetGatewayId: !Ref LabIGW

  # ── Public Subnet ─────────────────────────────────────────────────────
  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref LabVPC
      CidrBlock: 10.0.0.0/24
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: Public Subnet

  # ── Route Table ───────────────────────────────────────────────────────
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref LabVPC
      Tags:
        - Key: Name
          Value: Public Route Table

  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn: AttachIGW
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref LabIGW

  SubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable

  # ── Security Group ────────────────────────────────────────────────────
  SSHSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH from anywhere
      VpcId: !Ref LabVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: SSH Security Group

  # ── EC2 Instance ──────────────────────────────────────────────────────
  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t3.micro
      ImageId: !Ref AmiId
      SubnetId: !Ref PublicSubnet
      SecurityGroupIds:
        - !Ref SSHSecurityGroup
      Tags:
        - Key: Name
          Value: Web Server

Outputs:
  VPCId:
    Description: VPC ID
    Value: !Ref LabVPC

  PublicSubnetId:
    Description: Public Subnet ID
    Value: !Ref PublicSubnet

  EC2InstanceId:
    Description: EC2 Instance ID
    Value: !Ref WebServerInstance

  EC2PublicIP:
    Description: EC2 Public IP Address
    Value: !GetAtt WebServerInstance.PublicIp
```

---

## Deploy and Validate via AWS CLI

```bash
# Validate template syntax before deploying
aws cloudformation validate-template \
  --template-body file://vpc-ec2-stack.yaml

# Create the stack
aws cloudformation create-stack \
  --stack-name vpc-ec2-challenge \
  --template-body file://vpc-ec2-stack.yaml \
  --capabilities CAPABILITY_NAMED_IAM

# Monitor resource creation
watch -n 5 \
  aws cloudformation describe-stack-resources \
  --stack-name vpc-ec2-challenge \
  --query 'StackResources[*].[ResourceType,ResourceStatus]' \
  --output table

# Confirm stack status
aws cloudformation describe-stacks \
  --stack-name vpc-ec2-challenge \
  --query 'Stacks[*].[StackName,StackStatus]' \
  --output table
```

Stack status: **`CREATE_COMPLETE`** ✅

> **Screenshot:** Terminal — all resources showing `CREATE_COMPLETE`

![stack-create-complete](screenshots/01-stack-create-complete.png)

> **Screenshot:** CloudFormation Console → stack Resources tab — all 8 resources created

![cfn-resources](screenshots/02-cfn-resources.png)

---

## Resource Dependency Graph

```
LabVPC
  ├── LabIGW
  │     └── AttachIGW (VPCGatewayAttachment)
  │               └── PublicRoute (DependsOn: AttachIGW)
  ├── PublicSubnet
  │     ├── PublicRouteTable
  │     │     └── SubnetRouteTableAssociation
  │     └── WebServerInstance
  └── SSHSecurityGroup
        └── WebServerInstance
```

`DependsOn: AttachIGW` is required on `PublicRoute` because the IGW must be attached to the VPC before a route to it can be created — CloudFormation does not infer this dependency automatically.

---

## Template Design Decisions

| Decision | Reason |
|---|---|
| `AWS::SSM::Parameter::Value<>` for AMI | Resolves the latest Amazon Linux 2 AMI ID automatically per region — no hardcoded AMI IDs |
| `MapPublicIpOnLaunch: true` | EC2 instance gets a public IP without needing an Elastic IP |
| `DependsOn: AttachIGW` on route | Explicit dependency — route creation would fail if IGW is not yet attached |
| `!Ref` vs `!GetAtt` | `!Ref` returns resource ID; `!GetAtt` retrieves a specific attribute (e.g. PublicIp) |
| Outputs section | Exposes VPC ID, Subnet ID, Instance ID, and Public IP for easy retrieval post-deployment |

---

## Reflection

In this challenge lab I learned how to:

- **Write a CloudFormation template from scratch** — understanding the required sections (`AWSTemplateFormatVersion`, `Resources`, `Outputs`) and the correct resource types for each networking component
- **Manage explicit dependencies with `DependsOn`** — CloudFormation builds resources in parallel where possible, but some sequences must be enforced; `PublicRoute` cannot exist before `AttachIGW` completes, and this dependency is not automatically inferred from `!Ref`
- **Use SSM Parameter Store for dynamic AMI resolution** — hardcoding AMI IDs makes templates region-specific and breaks over time as AMIs are deprecated; the `AWS::SSM::Parameter::Value` type resolves the correct ID at deploy time in any region
- **Iterate on a template using `validate-template`** — running validation before `create-stack` catches YAML syntax errors and missing required properties before any AWS resources are touched

> Key takeaway: CloudFormation templates are version-controlled, repeatable, and self-documenting infrastructure. Writing one from scratch — rather than using the wizard — forces a precise understanding of how each AWS resource relates to the others, which is the same mental model needed for architecture design, troubleshooting, and the CLF-C02 exam.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
