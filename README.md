# Secure CI/CD Role Design

## Overview 

This project defines a secure, enterprise-grade IAM role architecture for CI/CD pipelines in AWS. The design follows DevSecOps and Zero Trust principles, ensuring that automated pipelines can deploy safely with minimal privileges, strong identity verification, and strict environment separation.

The architecture eliminates static credentials, enforces identity federation, and introduces tag-based authorization to control production deployments. Development environments deploy automatically, while production deployments are cryptographically gated through tagging and role-based conditions.

This model is suitable for regulated environments and enterprise cloud governance programs.

## Objective

Design a secure IAM role for CI/CD pipeline that:

1. Enable automatic deployment to Development environments.
2. Restrict Production deployments to approved, tagged releases only.
3. Minimize blast radius across environments

## Role Architecture Overview

The CI/CD security model uses three roles:

1. CICD-AssumeRole
    
    Entry role assumed by the CI/CD system via OIDC.
    
2. CICD-Dev-DeployRole
    
    Role used for automatic deployments to Development environments.
    
3. CICD-Prod-DeployRole
    
    Role used for Production deployments, gated by release tagging.
    

### Role Flow:

CI/CD System

→ CICD-AssumeRole (OIDC Identity Federation)

→ CICD-Dev-DeployRole (Auto Dev Deploy)

→ CICD-Prod-DeployRole (Tag-Gated Prod Deploy)


## Project Evidence

#### Cicd-user configuratioin
The cicd-user is configured to be accessed via ClI with zero access to any AWS service, all activities has to be through role assumption.

![cicd user](projectimages/cicd%20user.png)

---

#### CICD Role Assumption
All permissions now come **only** from the role policy

![cicd-pipeline role](projectimages/cicd-pipeline%20role.png)

 ---
 
#### Secure Prod Deployment Role (Enterprise CI/CD Architecture)
Pipeline-only prod access

![Prod Deployment](projectimages/Prod%20Deployment.png)

---

#### Manual approval gates
Code cannot deploy to prod without human authorization.Even if the pipeline is compromised, **prod stays locked**.
Dev Build → Dev Deploy → Manual Approval → Prod Deploy

![Manual Approval](projectimages/Manual%20Approval.png)
 
---

#### Audit & Monitoring
Track:
#### AssumeRole
Prove someone assumed the Prod deployment role

![Assume Role](projectimages/Assume%20Role.png)

---

#### UpdateService
Prove service update on the session

![Update service](projectimages/Update%20service.png)
 
---

## Final Enterprise Architecture
#### CI/CD Cross-Account Deployment Flow

Dev Account → CICD-Pipeline-Role  
⬇️  
Cross-Account AssumeRole  
⬇️  
Prod Account → Prod-CICD-Deploy-Role  
⬇️  
Prod Resources (Tagged + Approved Only)
