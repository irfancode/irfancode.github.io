---
title: "How I Use AI to Design and Implement Cloud Infrastructure with Terraform"
date: 2026-04-06
categories:
  - DevOps
  - AI
  - Terraform
  - Cloud
---

## The Problem

Migrating to the cloud is hard. Doing it fast without breaking production is even harder. Most teams either:
- Write Terraform by hand, making mistakes along the way
- Use blueprints that don't fit their specific needs
- Skip security hardening to save time

## The AI Solution

I use AI as my "junior DevOps engineer" - it generates initial infrastructure code that I then harden and refine. Here's how it works.

## The Workflow

```
1. Assessment    → Understand requirements, existing setup
2. Design        → Plan VPC, subnets, security
3. AI Generation → Prompt AI for Terraform code
4. Manual Refinement → Apply security hardening
5. Testing       → Plan/validate in non-prod
6. Implementation → Apply to production
```

## AI in Action: Example Prompts

### Prompt 1: VPC Setup
```
"Create Terraform for AWS VPC with:
- 10.0.0.0/16 CIDR block
- 2 public subnets in different AZs
- 2 private subnets in different AZs
- Internet gateway
- Proper route tables"
```

**AI Output**: Complete VPC, subnets, IGW, route tables - about 80% done.

**Manual Refinement Needed**:
- Add proper tags for cost tracking
- Enable DNS hostnames/support

### Prompt 2: Security Groups
```
"Create security group for Flask API allowing HTTP, HTTPS, SSH from VPC only"
```

**AI Output**: Basic rules but allows SSH from anywhere.

**Manual Refinement**:
```hcl
# Restrict SSH to admin network only
ingress {
  from_port   = 22
  to_port     = 22
  protocol    = "tcp"
  cidr_blocks = ["10.0.0.0/16"]  # VPC only, not 0.0.0.0/0
}
```

### Prompt 3: Database
```
"Add RDS PostgreSQL with private subnets, encryption, daily backups"
```

**AI Output**: Basic database definition.

**Manual Refinement**:
- Enable `storage_encrypted = true`
- Set `backup_retention_period = 7`
- Add to DB subnet group

## Code Example: AI vs Manual

From [POC #1: Terraform API](https://github.com/irfancode/poc-terraform-api):

```hcl
# AI-generated (base structure)
resource "aws_instance" "api" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.small"
  subnet_id     = aws_subnet.public[0].id
}

# After manual hardening
resource "aws_instance" "api" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  subnet_id     = aws_subnet.public[0].id
  
  vpc_security_group_ids = [aws_security_group.api.id]
  iam_instance_profile   = aws_iam_instance_profile.api.name
  
  root_block_device {
    encrypted = true
  }
  
  user_data = <<-EOF
    #!/bin/bash
    apt-get update && apt-get install -y python3-pip nginx
    # ... app setup
  EOF
}
```

## Results

| Metric | Before | After |
|--------|--------|-------|
| Time to initial Terraform | 2 days | 30 minutes |
| Security findings | 15 critical | 0 critical |
| Cost tracking | None | Tags on all resources |

## What This Means for Your Organization

- **Faster Time-to-Market**: AI generates infrastructure in minutes, not days
- **Better Security**: Manual hardening ensures production-grade security
- **Lower Costs**: Tags enable accurate cost allocation
- **Reproducible**: Code can be version-controlled and reviewed

Ready to accelerate your cloud migration? [Contact me](/#contact) for a free 30-minute assessment.
