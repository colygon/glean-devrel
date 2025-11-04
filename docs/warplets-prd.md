# Warplets - Product Requirements Document

## Executive Summary

**Warplets** are pre-configured, ephemeral cloud environments that bring the full Warp terminal experience to remote development. Developers can deploy a Warplet (a Docker container with the Warp agent) to any cloud provider and instantly connect via SSH, inheriting all Warp features including AI assistance, workflows, and Warp Drive integration.

**Status:** Concept  
**Version:** 1.0  
**Last Updated:** January 4, 2026  
**Owner:** DevRel Team

---

## Problem Statement

### Current Pain Points

1. **Remote Development Friction**
   - Developers working on remote servers lose Warp's AI features, input editor, and modern UX
   - Setting up development environments on cloud instances is time-consuming
   - SSH sessions require manual Warpify setup every time

2. **Inconsistent Development Environments**
   - Different team members have different local setups
   - "Works on my machine" problems persist
   - Hard to reproduce production environments locally

3. **Cloud Resource Management**
   - Developers manually spin up/down cloud instances
   - No standardized way to manage development containers
   - Difficulty sharing configured environments with teammates

4. **Warpify Adoption Barriers**
   - Users don't know about Warpify or how to use it
   - Manual setup required for each SSH connection
   - Limited to existing remote machines (not ephemeral)

### User Stories

**As a developer, I want to:**
- Instantly spin up a cloud dev environment with Warp pre-configured
- Work in a containerized environment that mirrors production
- Share my exact development setup with teammates via Warp Drive
- Automatically connect to cloud environments with full Warp features
- Destroy environments when done without leaving orphaned resources

**As a team lead, I want to:**
- Standardize development environments across my team
- Reduce onboarding time for new developers
- Ensure everyone has access to the same tools and configurations
- Track cloud resource usage and costs per developer

**As a DevOps engineer, I want to:**
- Provide developers with pre-configured, secure environments
- Integrate Warplets into CI/CD pipelines for testing
- Manage Warplet templates with infrastructure-as-code
- Monitor and control cloud resource consumption

---

## Solution: Warplets

### What is a Warplet?

A **Warplet** is a containerized, cloud-hosted development environment that:
- Runs a Docker container with Warp agent pre-installed
- Auto-Warpifies on SSH connection (no manual setup)
- Syncs with Warp Drive for workflows, prompts, and environment variables
- Can be deployed to any cloud provider (AWS, GCP, Azure, DigitalOcean, Fly.io, Railway)
- Is ephemeral by default (auto-destroys after inactivity)
- Supports custom configurations via Warplet templates

### How It Works

```
┌─────────────────────┐
│  Warp Desktop App   │
│  (macOS/Linux)      │
└──────────┬──────────┘
           │
           │ SSH Connection
           │ (Auto-Warpified)
           ↓
┌─────────────────────┐
│     Warplet         │
│  ┌───────────────┐  │
│  │ Docker        │  │
│  │ Container     │  │
│  │               │  │
│  │ - Warp Agent  │  │
│  │ - Dev Tools   │  │
│  │ - Your Code   │  │
│  └───────────────┘  │
│                     │
│  Cloud Provider     │
│  (AWS/GCP/Azure)    │
└─────────────────────┘
           ↕
    Warp Drive Sync
  (Workflows, Prompts,
   Env Vars, Notebooks)
```

### Key Features

#### 1. One-Command Deployment
```bash
# Deploy a Warplet
warp deploy --provider aws --region us-west-2

# Deploy with a template
warp deploy --template python-ml --provider gcp

# Deploy with custom config
warp deploy --config ./warplet.yaml
```

#### 2. Auto-Warpify on Connect
- No manual Warpify prompt
- Warp agent runs automatically in container
- Full Warp features available immediately (AI, input editor, blocks, workflows)

#### 3. Warp Drive Integration
- Automatically syncs your personal workflows, prompts, and env vars
- Team Warplets sync team-wide resources from Warp Drive
- Changes made in Warplet persist to Warp Drive

#### 4. Template Marketplace
Pre-built Warplet templates for common use cases:
- **Languages**: Python, Node.js, Go, Rust, Java
- **Frameworks**: React, Django, Rails, Next.js
- **Data Science**: Jupyter, PyTorch, TensorFlow
- **DevOps**: Terraform, Ansible, Kubernetes tools
- **Databases**: PostgreSQL, MongoDB, Redis included

#### 5. Lifecycle Management
```bash
# List active Warplets
warp list

# SSH into a Warplet
warp connect my-warplet

# Pause a Warplet (stop but don't destroy)
warp pause my-warplet

# Destroy a Warplet
warp destroy my-warplet

# Auto-destroy after 2 hours of inactivity
warp deploy --auto-destroy 2h
```

#### 6. Team Collaboration
- Share Warplet templates via Warp Drive
- Team admins can create/manage shared Warplets
- Pre-configure team standards (tools, env vars, workflows)
- Onboard new developers in <5 minutes

---

## Technical Architecture

### Components

#### 1. Warp Desktop Client
- **Warplet Manager**: UI for deploying/managing Warplets
- **Auto-SSH**: Automatically establishes SSH connection to deployed Warplet
- **Sync Engine**: Bidirectional sync with Warp Drive

#### 2. Warp Agent (Remote)
- Lightweight binary running in Warplet container
- Handles Warpify protocol over SSH
- Manages tmux sessions for multiplexing
- Syncs with Warp Drive for resources

#### 3. Warplet Orchestrator (Cloud Service)
- Manages Warplet deployments across providers
- Handles lifecycle (create, pause, destroy, auto-destroy)
- Tracks resource usage and costs
- Provides REST API for CLI/UI

#### 4. Container Registry
- Stores base Warplet images
- Hosts community and official templates
- Supports private registry for enterprise

### Deployment Flow

1. **User runs `warp deploy`**
   - CLI sends request to Warplet Orchestrator
   - User's cloud credentials used (AWS IAM, GCP Service Account, etc.)

2. **Orchestrator provisions infrastructure**
   - Creates cloud compute instance (EC2, GCE, etc.)
   - Pulls Warplet Docker image from registry
   - Injects user's SSH public key
   - Configures networking and security groups

3. **Container starts with Warp Agent**
   - Warp agent initializes and registers with Warp Drive
   - Downloads user's workflows, prompts, and env vars
   - Starts SSH server

4. **Warp Desktop auto-connects**
   - SSH connection established automatically
   - Warpify protocol activates (no prompt needed)
   - User has full Warp experience in cloud environment

5. **Lifecycle management**
   - Idle timeout starts counting (default: 1 hour)
   - If no activity, sends warning notification
   - Auto-destroys after timeout (configurable)

### Technology Stack

**Backend:**
- Warplet Orchestrator: Go/Rust
- Container runtime: Docker
- Cloud SDKs: AWS SDK, GCP SDK, Azure SDK, Terraform provider

**Agent:**
- Warp Agent: Rust (same as Warpify)
- SSH server: OpenSSH
- Multiplexer: tmux

**Infrastructure:**
- Container images: Docker Hub / private registry
- Networking: Cloud VPC, security groups
- Storage: Ephemeral (destroyed with Warplet) or persistent volumes (optional)

---

## Warplet Configuration

### Warplet Manifest (`warplet.yaml`)

```yaml
# Warplet Configuration
version: 1
name: my-python-warplet
description: Python 3.11 development environment

# Base image
image: warp/python:3.11

# Cloud provider configuration
provider: aws
region: us-west-2
instance_type: t3.medium

# Lifecycle
auto_destroy: 2h
auto_pause: 30m

# Networking
ports:
  - 8000:8000  # Expose port 8000 for web server
  - 5432:5432  # PostgreSQL

# Environment
environment:
  - DATABASE_URL: ${WARP_DRIVE_SECRET:db_url}
  - API_KEY: ${WARP_DRIVE_SECRET:api_key}

# Warp Drive sync
warp_drive:
  sync_workflows: true
  sync_prompts: true
  sync_env_vars: true
  sync_notebooks: true

# Startup commands
init:
  - pip install -r requirements.txt
  - python manage.py migrate

# Persistent storage (optional)
volumes:
  - name: code
    path: /workspace
    size: 20GB
    persistent: true

# Team sharing
team: engineering
shared: true

# Cost controls
max_cost_per_hour: 0.50
budget_alert_email: devops@company.com
```

### Template Structure

```
warplet-templates/
├── python/
│   ├── warplet.yaml
│   ├── Dockerfile
│   └── .warprc
├── node/
│   ├── warplet.yaml
│   ├── Dockerfile
│   └── .warprc
├── ml/
│   ├── warplet.yaml
│   ├── Dockerfile
│   ├── requirements.txt
│   └── .warprc
└── custom/
    └── (user-defined)
```

---

## User Experience

### Deployment Flow

#### Step 1: Choose or Create Template
```
$ warp deploy

? Select a Warplet template:
  > Python 3.11 Development
    Node.js 20 + TypeScript
    Go 1.21
    ML/AI (PyTorch + Jupyter)
    Custom (use warplet.yaml)

? Select cloud provider:
  > AWS (us-west-2)
    GCP (us-central1)
    Azure (eastus)
    DigitalOcean (sfo3)
    Railway
```

#### Step 2: Configure Options
```
? Warplet name: my-python-env
? Instance size: t3.medium ($0.04/hour)
? Auto-destroy after: 2 hours
? Share with team? No

✓ Configuration saved
```

#### Step 3: Deploy
```
🚀 Deploying Warplet "my-python-env"...

  ✓ Validating cloud credentials
  ✓ Provisioning EC2 instance (i-0abc123)
  ✓ Pulling Docker image warp/python:3.11
  ✓ Starting container
  ✓ Configuring SSH access
  ✓ Syncing Warp Drive resources
  
✅ Warplet deployed successfully!

  Name:       my-python-env
  Provider:   AWS (us-west-2)
  Instance:   t3.medium
  Cost:       ~$0.04/hour
  SSH:        warp-my-python-env.warp.dev
  Auto-destroy: 2 hours from now
  
🔗 Connecting automatically...
```

#### Step 4: Auto-Connect
```
Warp automatically opens new tab with SSH connection:

┌─────────────────────────────────────────────────┐
│ ⚡ my-python-env (Warplet) | AWS us-west-2     │
├─────────────────────────────────────────────────┤
│                                                 │
│ ubuntu@warplet:~/workspace$ █                   │
│                                                 │
│ 🎉 Connected to Warplet!                        │
│ All Warp features are active.                   │
│ Auto-destroy in 2 hours.                        │
│                                                 │
└─────────────────────────────────────────────────┘
```

### Management UI

**In Warp Desktop:**
```
Warp → Warplets (⌘K → "Warplets")

┌─────────────────────────────────────────────────┐
│ Your Warplets                     [+ New]       │
├─────────────────────────────────────────────────┤
│                                                 │
│ my-python-env                    🟢 Running     │
│ AWS t3.medium | $0.04/hr | 1h 23m active       │
│ [Connect] [Pause] [Destroy]                     │
│                                                 │
│ ml-training                      🟡 Paused      │
│ GCP n1-highmem-8 | Paused 3d ago               │
│ [Resume] [Destroy]                              │
│                                                 │
│ team-staging                     🟢 Running     │
│ Azure Standard_D4s | Shared with team          │
│ [Connect] [View Details]                        │
│                                                 │
└─────────────────────────────────────────────────┘

Total cost this month: $24.80
```

---

## Feature Specifications

### MVP (Phase 1)

**Must Have:**
- ✅ Deploy Warplet to AWS (EC2)
- ✅ Auto-Warpify on SSH connection
- ✅ Basic lifecycle management (deploy, connect, destroy)
- ✅ 5 official templates (Python, Node, Go, Rust, Generic Linux)
- ✅ Warp Drive sync (workflows, prompts)
- ✅ Auto-destroy after configurable timeout
- ✅ Cost estimation and tracking
- ✅ CLI interface (`warp deploy`, `warp list`, etc.)

**Should Have:**
- 🔄 Warplet Manager UI in Warp Desktop
- 🔄 Port forwarding configuration
- 🔄 Environment variable injection
- 🔄 Basic monitoring (CPU, memory, uptime)

**Won't Have (Phase 1):**
- ❌ GCP/Azure support (AWS only for MVP)
- ❌ Persistent storage
- ❌ Team sharing
- ❌ Custom templates (official templates only)
- ❌ Advanced networking (VPC peering, etc.)

### Phase 2 (Enterprise Features)

**Multi-Cloud Support:**
- GCP, Azure, DigitalOcean, Railway, Fly.io
- Unified interface across providers
- Cloud-agnostic cost tracking

**Team Collaboration:**
- Share Warplet templates via Warp Drive
- Team-owned Warplets with access control
- Centralized team template library
- Shared persistent storage

**Advanced Lifecycle:**
- Pause/resume (stop instance, keep disk)
- Scheduled auto-start (weekdays 9am)
- Snapshots and restore
- Clone Warplet to new instance

**Developer Experience:**
- VS Code integration (open remote workspace)
- Git integration (auto-clone repos on deploy)
- Dotfiles sync (sync your .zshrc, .vimrc, etc.)
- Custom init scripts

### Phase 3 (Advanced)

**CI/CD Integration:**
- Deploy Warplets for PR previews
- Run tests in isolated Warplets
- GitHub Actions integration
- GitLab CI integration

**Advanced Networking:**
- VPC peering for database access
- Private Warplets (no public IP)
- Custom DNS
- Load balancing for shared Warplets

**Security & Compliance:**
- SSO integration (Okta, Azure AD)
- Audit logging
- Compliance presets (SOC2, HIPAA)
- Network security scanning

**Observability:**
- Real-time resource monitoring
- Cost anomaly detection
- Performance profiling
- Log aggregation

---

## Business Model

### Pricing

**Individual Developers:**
- **Free Tier**: 20 hours/month of Warplet usage (AWS t3.micro equivalent)
- **Pro Tier** ($19/month): Unlimited Warplets, all instance types, priority support
- **Pay-as-you-go**: Cloud costs + 10% Warp fee for orchestration

**Teams:**
- **Team Plan** ($49/user/month): Includes Pro features + team sharing + centralized billing
- **Enterprise**: Custom pricing with volume discounts, dedicated support, SLA

**Cloud Cost Pass-Through:**
- Users pay actual cloud costs (EC2, GCE, etc.)
- Warp adds service fee (10% for Free/Pro, 5% for Team/Enterprise)
- Transparent cost tracking in dashboard

### Revenue Projections

**Year 1:**
- Target: 10,000 Warplet deployments/month
- Average Warplet cost: $2/deployment
- Warp fee: 10% = $0.20 per deployment
- Monthly revenue: $2,000 from platform fees
- Subscription revenue: 2,000 Pro users × $19 = $38,000/month

**Year 3:**
- 100,000 deployments/month
- 20,000 Pro users
- 500 team accounts (10 users each)
- Est. monthly revenue: $300K+

---

## Technical Considerations

### Security

**Authentication:**
- SSH keys managed by Warp (stored securely)
- Optional: Bring your own SSH key
- Cloud credentials stored with encryption (AWS Secrets Manager, etc.)

**Network Security:**
- Warplets deployed in isolated VPCs
- Security groups restrict access to SSH only (from user's IP)
- Optional: Private Warplets (no public IP, access via bastion)

**Container Security:**
- Base images scanned for vulnerabilities (Trivy, Snyk)
- Regular updates and patches
- Non-root user by default
- Read-only root filesystem option

### Performance

**Connection:**
- SSH latency: <50ms (same region), <150ms (cross-region)
- Warpify overhead: <10ms
- Target: Sub-second to fully interactive terminal

**Resource Usage:**
- Warp Agent memory: <50MB
- Warp Agent CPU: <1% idle, <5% active
- Container startup time: <30 seconds

### Reliability

**Uptime:**
- Target: 99.9% availability for Warplet Orchestrator
- Graceful degradation if Orchestrator is down (direct SSH still works)
- Auto-retry failed deployments

**Data Durability:**
- Ephemeral Warplets: Data lost on destroy (by design)
- Persistent volumes: Standard cloud provider SLA (99.99%+)
- Warp Drive sync ensures no loss of workflows/prompts

### Scalability

**Horizontal:**
- Warplet Orchestrator: Stateless, scales horizontally
- Load balanced across multiple regions
- Support for 100,000+ concurrent Warplets

**Vertical:**
- Users can choose any instance size their cloud provider offers
- No artificial limits on CPU/RAM/storage

---

## Go-to-Market Strategy

### Target Audience

**Primary:**
1. **Individual developers** working on open-source projects
2. **Freelancers** needing consistent dev environments
3. **Remote teams** wanting standardized setups

**Secondary:**
1. **Engineering teams** at startups/scaleups
2. **Bootcamp students** learning to code
3. **Data scientists** needing GPU instances

### Launch Plan

**Phase 1: Private Beta (Month 1-2)**
- Invite 100 power users from Warp community
- AWS only, limited templates
- Gather feedback, iterate quickly

**Phase 2: Public Beta (Month 3-4)**
- Open to all Warp users
- Announce on Twitter, Hacker News, Product Hunt
- Partner with cloud providers (AWS credits for new users)

**Phase 3: GA (Month 5+)**
- Full launch with marketing campaign
- Integration with developer communities (Dev.to, Hashnode)
- Conference talks and workshops

### Marketing Materials

**Blog Posts:**
- "Introducing Warplets: Your Dev Environment in the Cloud"
- "5 Ways Warplets Speed Up Your Development Workflow"
- "How Warplets Solves 'Works on My Machine'"

**Tutorials:**
- "Deploy Your First Warplet in 60 Seconds"
- "Building a Custom Warplet Template"
- "Team Onboarding with Shared Warplets"

**Videos:**
- Product demo (2 min)
- Deep dive tutorials (10 min each)
- Customer success stories

---

## Success Metrics

### Launch Metrics (First 3 Months)

- **Adoption**: 5,000 unique users deploy a Warplet
- **Engagement**: 2,000 active Warplets per day
- **Retention**: 40% of users deploy a 2nd Warplet within 7 days
- **NPS**: >50

### Business Metrics (Year 1)

- **Revenue**: $200K MRR from Warplet platform
- **Conversion**: 10% of free users upgrade to Pro
- **Churn**: <5% monthly for paid users
- **CAC Payback**: <6 months

### Product Metrics

- **Deployment Success Rate**: >95%
- **Time to First Connection**: <2 minutes (median)
- **Average Warplet Lifetime**: 3-4 hours
- **Cost per Warplet**: $0.50-$2.00

### User Satisfaction

- **Feature Satisfaction**: >80% users rate Warplets 4-5 stars
- **Support Tickets**: <2% of deployments result in support ticket
- **Community Sentiment**: Positive mentions on Twitter, Reddit, HN

---

## Risks & Mitigations

### Technical Risks

**Risk: Cloud Provider Outages**
- *Impact*: High - Users can't deploy or access Warplets
- *Mitigation*: Multi-region support, graceful degradation, status page

**Risk: Security Vulnerability in Warp Agent**
- *Impact*: Critical - Could compromise user containers
- *Mitigation*: Regular security audits, bug bounty program, rapid patching

**Risk: SSH Connection Failures**
- *Impact*: Medium - Users can't connect to Warplets
- *Mitigation*: Robust retry logic, fallback to manual SSH, monitoring

### Business Risks

**Risk: Low Adoption**
- *Impact*: High - Warplets don't generate revenue
- *Mitigation*: Aggressive marketing, free tier, partnerships

**Risk: Cloud Costs Higher Than Expected**
- *Impact*: Medium - Margins compressed
- *Mitigation*: Reserved instances, spot instances, cost monitoring

**Risk: Competition from Cloud IDEs**
- *Impact*: Medium - GitHub Codespaces, GitPod, etc.
- *Mitigation*: Focus on Warp's unique UX, AI features, local-first approach

### Legal/Compliance Risks

**Risk: GDPR/Data Privacy Violations**
- *Impact*: Critical - Fines, reputation damage
- *Mitigation*: GDPR-compliant infrastructure, data processing agreements

**Risk: Cloud Provider ToS Violations**
- *Impact*: High - Account suspension
- *Mitigation*: Review ToS, implement usage limits, abuse detection

---

## Open Questions

1. **Persistent Storage Strategy**
   - Should we support persistent volumes in MVP?
   - How to handle data when Warplet is destroyed?
   - Backup/restore workflows?

2. **Networking Complexity**
   - Do users need VPC peering for database access?
   - How to handle firewall rules for custom ports?
   - Support for private Warplets (no public IP)?

3. **Template Ecosystem**
   - Should we allow community templates in MVP?
   - How to review/curate community templates?
   - Monetization for premium templates?

4. **Integration Priorities**
   - Which cloud providers after AWS?
   - VS Code integration priority?
   - CI/CD integration timing?

5. **Pricing Model**
   - Is 10% service fee competitive?
   - Should free tier include cloud credits?
   - Volume discounts for teams?

---

## Appendix

### Competitive Analysis

**GitHub Codespaces:**
- ✅ Tight GitHub integration
- ✅ VS Code in browser
- ❌ Expensive ($0.18/hour minimum)
- ❌ GitHub-locked
- ❌ No local terminal integration

**GitPod:**
- ✅ Multi-provider support
- ✅ Open source
- ❌ Browser-based only
- ❌ No local terminal experience
- ❌ Complex setup

**AWS Cloud9:**
- ✅ Native AWS integration
- ✅ Free tier
- ❌ AWS-locked
- ❌ Dated UI
- ❌ Limited to EC2

**Warplets Advantages:**
- ✅ Local terminal experience (not browser)
- ✅ Full Warp features (AI, workflows, etc.)
- ✅ Multi-cloud (eventually)
- ✅ Warp Drive integration
- ✅ Auto-Warpify (no setup)

### Technical FAQ

**Q: Why Docker instead of VMs?**
A: Faster startup (<30s vs 2-3min), cheaper, easier to template, portable.

**Q: How does auto-Warpify work?**
A: Warp agent runs in container, detects SSH connection, initializes Warpify protocol automatically.

**Q: Can I use my own Docker images?**
A: Phase 2 feature. MVP uses official templates only.

**Q: What happens to my code when Warplet is destroyed?**
A: Ephemeral by default (code is lost). Use persistent volumes or sync to Git.

**Q: Can I access Warplet from non-Warp terminal?**
A: Yes, standard SSH works. You just lose Warp features.

**Q: Support for GPU instances?**
A: Yes, users can select any instance type their cloud provider offers.

---

## Next Steps

1. **Validation** (Week 1-2)
   - User interviews with 20 Warp power users
   - Validate pricing assumptions
   - Gather template requirements

2. **Design** (Week 3-4)
   - UI mockups for Warplet Manager
   - CLI command design
   - Warplet manifest spec finalization

3. **Prototype** (Week 5-8)
   - Build basic Warplet Orchestrator
   - Create 2-3 base Docker images
   - Test AWS deployment flow

4. **Alpha** (Week 9-12)
   - Internal dogfooding
   - Refine UX based on feedback
   - Security review

5. **Beta** (Week 13-16)
   - Invite 100 beta users
   - Iterate based on feedback
   - Prepare launch materials

6. **Launch** (Week 17+)
   - Public announcement
   - Marketing campaign
   - Monitor metrics and iterate

---

**Document Status:** Draft v1.0  
**Feedback:** Please submit comments via [Google Doc link] or Slack #warplets-prd  
**Approvals Needed:** Product, Engineering, Design, Marketing, Legal
