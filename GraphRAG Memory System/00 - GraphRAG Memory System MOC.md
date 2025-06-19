---
type: moc
status: active
tags: 
  - graphrag
  - memgraph
  - mcp
  - knowledge-management
aliases:
  - "GraphRAG MOC"
  - "Memory System Hub"
---

# GraphRAG Memory System - MOC

> **Map of Content for the GraphRAG-based AI Memory System Project**  
> *A comprehensive long-term memory solution combining graph databases, vector embeddings, and visual exploration*

## 🎯 Project Overview

**Goal**: Create an advanced long-term memory system for AI assistants using GraphRAG architecture that combines:
- **Vector embeddings** (Voyage 3.5) for semantic search
- **Knowledge graphs** (Memgraph) for relationship navigation  
- **Visual exploration** (Memgraph Lab) for insight discovery
- **MCP integration** (Cursor) for seamless AI interaction

**Status**: ✅ Technical setup complete, moving to design phase

---

## 🗺️ Navigation

### 📚 Research & Concepts
- [[GraphRAG Concepts and Theory]]
- [[Vector-Graph Integration Patterns]]
- [[Long-term Memory Architecture Research]]

### 🏗️ Technical Foundation  
- [[Memgraph Database Setup]]
- [[MCP Server Configuration]]
- [[Memgraph Lab Visualization]]
- [[Docker Architecture Overview]]

### 🎨 System Design
- [[Node Types Schema]]
- [[Relationship Types Design]]
- [[Data Flow Architecture]]
- [[Query Patterns and Use Cases]]

### 🔧 Implementation
- [[Database Schema Implementation]]
- [[Vector Integration with Voyage 3.5]]
- [[MCP Tools Development]]

### 🧪 Testing & Validation
- [[System Performance Tests]]
- [[Memory Persistence Validation]]
- [[Graph Visualization Testing]]

---

## 🔗 Key Concepts

### **GraphRAG Architecture**
Combines the semantic understanding of RAG (Retrieval-Augmented Generation) with the relationship mapping power of knowledge graphs.

### **Decentralized Network Topology**  
Following the MOC philosophy - neither fully centralized (single index) nor fully distributed (no structure), but decentralized with multiple navigation hubs.

### **Multi-Modal Memory Storage**
- **Documents** → **Sections** → **Paragraphs** (hierarchical)
- **Concepts** ↔ **Relationships** ↔ **Context** (semantic)
- **Temporal** connections preserving conversation history

---

## 📊 Current Status

| Component | Status | Notes |
|-----------|---------|--------|
| Memgraph DB | ✅ Running | Port 7687, Docker container |
| MCP Server | ✅ Active | Port 8000, HTTP transport |
| Lab Interface | ✅ Connected | Port 3000, visualization ready |
| Cursor Integration | ✅ Working | MCP tools available |
| Schema Design | 🔄 In Progress | Node/relationship types |
| Vector Integration | ⏳ Planned | Voyage 3.5 embeddings |

---

## 🎯 Next Actions

1. **Complete schema design** - finalize node and relationship types
2. **Implement vector storage** - integrate Voyage 3.5 embeddings  
3. **Build core MCP tools** - document ingestion, search, visualization
4. **Test with real data** - validate memory persistence and retrieval
5. **Optimize performance** - query speed and graph navigation

---

## 🔍 Quick Access

**Find notes by:**
- `#graphrag` - Core GraphRAG concepts
- `#memgraph` - Database-specific content  
- `#mcp` - Integration and tools
- `#research` - Background research and theory
- `#implementation` - Technical implementation details

**Project Context**: [[LNTS Mercury Project Overview]]