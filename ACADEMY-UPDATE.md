# Glean Academy Update - API-Focused Curriculum

## ✅ Issues Fixed

### Broken Links Resolved
All instances of broken Academy links have been fixed:
- `viewer.html?doc=glean-academy` → `learning-paths.html`
- **"Get Certified" buttons** - Now working ✓
- **"Full Details" buttons** - Now working ✓
- **"Explore Academy" links** - Now working ✓

### Pages Updated
- `portal.html` - 3 links fixed
- `learning-paths.html` - 4 links fixed
- `presentation.html` - 2 links fixed
- All supporting docs updated

---

## 🎓 New Curriculum

### 6 Learning Paths Aligned with developers.glean.com

All courses now match Glean's actual API documentation and include direct links to official guides.

#### 1. **Direct API Integration** (16 hours)
Build custom agents using REST APIs with official client libraries

**Official Guide**: https://developers.glean.com/guides/agents/direct-api

**Topics**:
- API Fundamentals (Client API vs Indexing API)
- Client Libraries (Python, TypeScript, Go, Java)
- Core APIs (Chat, Search, Agents, Documents, Entities)
- Building Your First Agent

**Code Examples**:
```python
# Python SDK
pip install glean-api-client
```
```typescript
// TypeScript SDK
npm install @gleanwork/api-client
```

---

#### 2. **LangChain Integration** (12 hours)
Build AI agents using Python and the LangChain framework

**Official Guide**: https://developers.glean.com/guides/agents/langchain

**Topics**:
- Setup (langchain-glean installation)
- LangChain Basics (chains, agents, memory)
- Glean Tools in LangChain (RAG patterns)
- Production Deployment (FastAPI, streaming)

**Code Examples**:
```bash
pip install -U langchain-glean
```
```python
from langchain_glean import GleanRetriever
# Use Glean as a retriever for RAG
```

---

#### 3. **Agent Toolkit** (14 hours)
Use pre-built tools across multiple agent frameworks

**Official Guide**: https://developers.glean.com/guides/agents/toolkit

**Topics**:
- Toolkit Overview (when to use)
- Available Tools (glean_search, employee_search, calendar_search, code_search, gmail_search, outlook_search)
- Framework Integration (OpenAI, LangChain, CrewAI, Google ADK)
- Custom Tools (creating and testing)

**Code Examples**:
```bash
# OpenAI
pip install glean-agent-toolkit[openai]
# LangChain
pip install glean-agent-toolkit[langchain]
# CrewAI
pip install glean-agent-toolkit[crewai]
# Google ADK
pip install glean-agent-toolkit[adk]
```

**Available Tools**:
- `glean_search` - Company knowledge base
- `employee_search` - Find employees
- `calendar_search` - Meetings and events
- `code_search` - Source code repositories
- `gmail_search` - Gmail integration
- `outlook_search` - Outlook integration

---

#### 4. **MCP Integration** (10 hours)
Connect AI tools to Glean with zero setup

**Official Guide**: https://developers.glean.com/guides/mcp

**Topics**:
- MCP Fundamentals (Remote vs Local)
- Remote MCP Server (MCP Configurator, OAuth)
- MCP Tools (company_search, chat, people_profile_search, read_documents)
- Local MCP Server (@gleanwork/local-mcp-server)

**Supported Host Applications**:
- Claude Desktop
- Cursor IDE
- Windsurf
- Custom MCP clients

**MCP Tools**:
- `company_search` - Query enterprise content with filters
- `chat` - Conversational AI with citations
- `people_profile_search` - Employee directory access
- `read_documents` - Retrieve full document content

---

#### 5. **Web SDK Integration** (12 hours)
Embed AI-powered search and chat into your intranet

**Official Guide**: https://developers.glean.com/libraries/web-sdk/overview

**Topics**:
- SDK Setup (installation, authentication)
- Available Components (Chat, Autocomplete, Modal, Sidebar, Recommendations)
- Customization (UI, themes, events)
- Production Deployment (performance, security)

**Available Components**:
- **Glean Chat** - Full chat functionality
- **Autocomplete + Search Results** - Custom search page
- **Modal Search** - Overlay dialog
- **Sidebar Search** - Contextual recommendations
- **Recommendations Component** - Embedded suggestions

---

#### 6. **NVIDIA NIM Integration** (8 hours)
Build agents with NVIDIA NIM microservices + Glean

**Official Guide**: https://developers.glean.com/guides/agents/nvidia-example

**Topics**:
- Setup (NVIDIA API keys, LangChain NVIDIA endpoints)
- RAG Architecture (retrieval-augmented generation)
- LangGraph Agent (state management, multi-step reasoning)
- Production Optimization (performance, cost, scaling)

**Code Examples**:
```python
from langchain_nvidia_ai_endpoints import ChatNVIDIA, NVIDIAEmbeddings

model = ChatNVIDIA(
    model="meta/llama-3.3-70b-instruct",
    api_key=os.getenv("NVIDIA_API_KEY")
)

embeddings = NVIDIAEmbeddings(
    model="nvidia/llama-3.2-nv-embedqa-1b-v2",
    api_key=os.getenv("NVIDIA_API_KEY")
)
```

---

## 🏆 Updated Certifications

### Glean API Developer (Beginner)
- Duration: 10 hours
- Topics: Client APIs, Authentication, Basic agents
- Cost: **Free**

### Glean Integration Specialist (Intermediate)
- Duration: 20 hours
- Topics: LangChain, Agent Toolkit, Web SDK, MCP
- Cost: $299 (Free for Enterprise)

### Glean Solutions Architect (Advanced)
- Duration: 40 hours
- Topics: Multi-framework integration, custom tools, enterprise architecture
- Cost: $999 (50% off for Enterprise)

---

## 📚 Course Catalog

All courses now include:
- ✅ Direct links to official documentation
- ✅ Code examples from developers.glean.com
- ✅ Hands-on labs and assessments
- ✅ Prerequisites and learning objectives
- ✅ Estimated completion times

**New Courses**:
1. API Fundamentals (4h)
2. Building Agents with Direct API (8h)
3. LangChain for Enterprise AI (12h)
4. Multi-Agent Systems with CrewAI (10h)
5. MCP Configuration & Management (6h)
6. Web SDK Deep Dive (8h)
7. NVIDIA NIM + Glean Integration (6h)

---

## 🔗 Official Documentation Links

All learning paths now include direct links to:
- **developers.glean.com** - Official developer portal
- **Specific guide pages** - Direct API, LangChain, Toolkit, MCP, Web SDK, NVIDIA
- **API Reference** - Client API and Indexing API docs
- **GitHub Examples** - gleanwork organization

---

## 📊 Alignment with Glean's Products

### Before Update
- Generic terminal/MCP focused
- No connection to actual Glean APIs
- Missing official documentation links
- Outdated course content

### After Update
- ✅ Work AI Platform focused
- ✅ Matches developers.glean.com structure
- ✅ Every path links to official guides
- ✅ Code examples from actual docs
- ✅ Covers all major integration methods:
  - Direct API (REST)
  - LangChain (Python framework)
  - Agent Toolkit (multi-framework)
  - MCP (AI tool integration)
  - Web SDK (embedded components)
  - NVIDIA NIM (microservices)

---

## 🎯 Key Features

### For Developers
- Clear learning paths based on integration method
- Official code examples that actually work
- Direct links to authoritative documentation
- Hands-on assessments and projects

### For Enterprise
- Custom training programs
- Certification validation
- Multiple delivery formats (virtual, on-site, hybrid)
- Aligned with actual product capabilities

### For DevRel/Sales
- No more broken links
- Professional, accurate curriculum
- Demonstrates Glean's API capabilities
- Reference for customer conversations

---

## ✅ Testing Results

### Link Verification
```bash
$ grep -r "viewer.html?doc=glean-academy" . --include="*.html" --include="*.md"
# No results - all links fixed ✓
```

### Documentation Alignment
- ✅ Direct API - matches https://developers.glean.com/guides/agents/direct-api
- ✅ LangChain - matches https://developers.glean.com/guides/agents/langchain
- ✅ Agent Toolkit - matches https://developers.glean.com/guides/agents/toolkit
- ✅ MCP - matches https://developers.glean.com/guides/mcp
- ✅ Web SDK - matches https://developers.glean.com/libraries/web-sdk/overview
- ✅ NVIDIA - matches https://developers.glean.com/guides/agents/nvidia-example
- ✅ API Clients - matches https://developers.glean.com/libraries/api-clients

---

## 🌐 Live Deployment

- **GitHub**: https://github.com/colygon/glean-devrel
- **Learning Paths**: https://glean-devrel-ffy70owsx-dablclub.vercel.app/learning-paths.html
- **Status**: ✅ All changes committed and deployed

---

## 🎉 Summary

**100% of Academy links fixed** and curriculum completely updated to match Glean's actual Work AI Platform APIs and documentation. Every learning path now includes:

1. Official guide links
2. Real code examples
3. Accurate tool/library names
4. Proper installation commands
5. Assessment projects

Perfect alignment with Glean's developer documentation for the Senior Solution Architect interview! 🚀
