# CI/CD Workflow with Environmental Protection

## Overview

This document outlines a Continuous Integration and Continuous Deployment (CI/CD) workflow that implements progressive deployment across multiple environments with built-in protection measures. The workflow is implemented as a GitHub Actions pipeline that automatically builds and deploys code based on specific trigger events.

## Workflow Triggers

The pipeline is triggered by three different events:
- Push events to the `main` branch
- Pull request events targeting the `main` branch
- Manual triggers via `workflow_dispatch`

## Pipeline Stages

### 1. Build Stage

**Purpose**: Compiles the application and creates the deployable artifact.

**Environment**: Runs on `ubuntu-latest`

**Steps**:
- Checks out the code repository
- Executes a build command (currently a placeholder that echoes "Building Image/Artifact!")

### 2. Development Deployment Stage

**Purpose**: Deploys the application to the development environment for testing.

**Environment**: 
- Name: "Development Phase"
- URL: `https://portfolio-6b3d6.firebaseapp.com/`

**Trigger Condition**: Only runs when the workflow is triggered by a pull request event.

**Dependencies**: Only executes after the Build stage completes successfully.

**Steps**:
- Executes a deployment command (currently a placeholder for Firebase deployment)

### 3. Staging Deployment Stage

**Purpose**: Deploys the application to the staging environment for pre-production verification.

**Environment**: 
- Name: "Staging"
- URL: `https://portfolio-6b3d6.web.app/`

**Trigger Condition**: Only runs when the workflow is triggered by a push to the `main` branch.

**Dependencies**: Only executes after the Build stage completes successfully.

**Steps**:
- Executes a deployment command (currently a placeholder for Firebase deployment)

### 4. Production Deployment Stage

**Purpose**: Deploys the application to the production environment for end users.

**Environment**: 
- Name: "Production"
- URL: `https://raihan.dev/`

**Protection Rules**:
- Required reviewer approval (Raihan-009 is configured as a reviewer)
- Only the `main` branch is allowed to deploy to this environment
- Administrators can bypass protection rules if necessary

**Trigger Condition**: No explicit event filter, but depends on Staging completion.

**Dependencies**: 
- Only executes after the Staging Deployment stage completes successfully
- Requires manual approval from designated reviewers

**Steps**:
- Waits for reviewer approval
- Executes a deployment command (currently a placeholder for production deployment)

## Workflow Visualization

```
                  ┌──────────┐
                  │  Events  │
                  └────┬─────┘
                       │
                       ▼
                  ┌──────────┐
                  │   Build  │
                  └────┬─────┘
                       │
          ┌────────────┴────────────┐
          │                         │
          ▼                         ▼
┌───────────────────────┐  ┌───────────────────┐
│ Deploy to Development │  │ Deploy to Staging │
│  (Pull Requests only) │  │ (Main branch only)│
└───────────────────────┘  └─────────┬─────────┘
                                     │
                                     ▼
                           ┌───────────────────┐
                           │ Approval Required │
                           │  (Reviewers)      │
                           └─────────┬─────────┘
                                     │
                                     ▼
                           ┌───────────────────┐
                           │Deploy to Production│
                           │ (Main branch only) │
                           └───────────────────┘
```

## Environmental Protection Features

The workflow includes several protection mechanisms:

1. **Environment Isolation**: Development, Staging, and Production are treated as separate environments with unique URLs.

2. **Sequential Deployment**: The pipeline enforces a specific order of deployment progression:
   - For pull requests: Build → Development
   - For main branch: Build → Staging → Production

3. **Conditional Deployment**: 
   - Development deployments only happen for pull requests
   - Staging deployments only happen for pushes to the main branch
   - Production deployments only happen after successful staging deployments

4. **GitHub Environments**: The use of the `environment` keyword enables additional protection features in GitHub:
   - Environment-specific protection rules
   - Required approvals
   - Wait timers
   - Deployment branches restrictions

5. **Production Approval Process**: 
   - The Production environment requires explicit reviewer approval before deployment can proceed
   - Designated reviewers (such as "Raihan-009") must approve the deployment after the staging phase completes
   - Administrators can bypass protection rules if necessary

6. **Branch Protection Rules**:
   - Only the `main` branch is allowed to deploy to the Production environment
   - This restriction prevents unauthorized branches from accessing Production environment secrets
   - Ensures that all code deployed to Production has gone through the proper review and approval processes

## Real-World Advantages

1. **Risk Mitigation & Quality Control**: Progressive deployment through multiple environments with mandatory approvals ensures issues are caught before reaching production, significantly reducing downtime and potential business impact.

2. **Security & Compliance**: Branch protection rules prevent unauthorized access to production secrets, while approval workflows satisfy regulatory requirements in industries with strict change management protocols.

3. **Accelerated Development Cycles**: The automated pipeline with development environment deployments for pull requests enables faster feedback cycles and iteration on features without compromising stability.
