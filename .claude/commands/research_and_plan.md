**INTERACTIVE RESEARCH & PLANNING COMMAND**

You are about to engage in a comprehensive, interactive research and planning session. This command adapts to any project type while maintaining structured documentation and intelligent task management.

**PHASE 1: PROJECT CONTEXT GATHERING**

Begin by analyzing the current project structure and engaging in interactive discovery.

**1.1 Initial Project Scan:**
- Examine package.json, config files, and directory structure
- Identify apparent project type and tech stack
- Check ai_doc/ folder for existing context
- Present findings for confirmation

**1.2 Interactive Goal Clarification:**
Ask the following questions, waiting for responses between each:

```
Q1: "Based on my analysis, this appears to be [detected project type]. Is this correct, or will this project become something else?"

Q2: "What is the primary goal of this project? Please be specific about the main problem it solves."

Q3: "Who is your target audience/users?"

Q4: "What are the must-have features for the initial version?"

Q5: "What technical constraints should I be aware of? (hosting, budget, team size, deadlines)"

Q6: "How should I handle complex tasks?"
    a) Break everything down to smallest steps
    b) Keep high-level unless I get stuck  
    c) Ask each time

Q7: "Are there any existing systems this needs to integrate with?"
```

**1.3 State Persistence:**
Save all responses to `ai_doc/project_context.json` with this structure:
```json
{
  "project_type": "string",
  "primary_goal": "string",
  "target_audience": "string",
  "must_have_features": ["array"],
  "constraints": {
    "technical": ["array"],
    "timeline": "string",
    "budget": "string"
  },
  "complexity_preference": "string",
  "integrations": ["array"],
  "session_history": [
    {
      "date": "ISO string",
      "phase": "string",
      "decisions": ["array"]
    }
  ]
}
```

**PHASE 2: INTELLIGENT AGENT ORCHESTRATION**

**2.1 Task Complexity Scoring Algorithm:**
```
For each identified task, calculate complexity score (0.0 - 1.0):

Technical Factors:
- Requires new framework/library: +0.3
- Cross-system integration: +0.3  
- Real-time features: +0.2
- Payment processing: +0.2
- Authentication/security: +0.2

Scope Factors:
- Affects > 10 files: +0.3
- Affects 5-10 files: +0.2
- New architecture required: +0.3
- Breaking changes: +0.2
- Database schema changes: +0.2

Domain Factors:
- Unfamiliar domain: +0.2
- Regulatory compliance: +0.3
- Third-party API complexity: +0.2

Score = min(sum of all factors, 1.0)
```

**2.2 Agent Count Determination:**
```
Base calculation:
- Complexity > 0.7: 3 agents
- Complexity 0.4-0.7: 2 agents  
- Complexity < 0.4: 1 agent

Modifiers:
- Subtasks > 10: +2 agents
- Subtasks 5-10: +1 agent
- Context remaining < 30%: max 2 agents
- User preference for detail: +1 agent

Maximum agents: 5 (hard limit)
```

**2.3 Task Breakdown Rules:**
- Tasks with complexity > 0.5: Mandatory breakdown
- Tasks with complexity 0.3-0.5: Ask user preference
- Tasks with complexity < 0.3: Keep as single unit

**PHASE 3: DOCUMENTATION STRUCTURE**

Create and maintain the following structure in ai_doc/:

```
ai_doc/
├── project_context.json          # Persistent project information
├── research/                     # External documentation
│   ├── tech_stack/              # Framework-specific docs
│   │   ├── astro_best_practices.md
│   │   ├── tailwind_v4_migration.md
│   │   └── [framework]_notes.md
│   ├── integrations/            # Third-party API docs
│   │   ├── square_sdk_reference.md
│   │   └── [service]_integration.md
│   └── best_practices/          # Industry standards
│       ├── seo_checklist.md
│       └── [domain]_standards.md
├── planning/                    # Generated plans
│   ├── prd_v[N].md             # Product Requirements Document
│   ├── tech_spec_v[N].md       # Technical Specification
│   ├── task_breakdown.md        # Complexity analysis & breakdown
│   └── architecture_decisions.md # Key technical decisions
├── progress/                    # Living documentation
│   ├── completed_tasks.md       # What's been done
│   ├── blockers.md             # Current obstacles
│   ├── decisions.md            # Decision log with rationale
│   └── lessons_learned.md      # Insights for future
└── templates/                   # Reusable patterns
    ├── barbershop_booking.md    # Domain-specific template
    ├── ecommerce_store.md       # E-commerce patterns
    ├── blog_site.md            # Content site patterns
    └── saas_app.md             # SaaS patterns
```

**PHASE 4: RESEARCH EXECUTION**

**4.1 Research Strategy:**
Based on project type and requirements, determine research needs:

1. **Tech Stack Documentation:**
   - Current version docs for all dependencies
   - Migration guides if updating
   - Best practices and common patterns

2. **Integration Documentation:**
   - API references for third-party services
   - Authentication flows
   - Rate limits and pricing

3. **Domain Best Practices:**
   - Industry standards (e.g., SEO for barbershop)
   - Compliance requirements
   - User experience patterns

**4.2 Research Agent Deployment:**
Deploy agents based on research needs:
- Use parallel agents for independent research areas
- Limit based on context and complexity
- Store all findings in ai_doc/research/

**PHASE 5: PLANNING GENERATION**

**5.1 Product Requirements Document (PRD):**
Generate comprehensive PRD including:
- Executive summary
- User stories
- Feature specifications
- Success metrics
- Timeline estimates

**5.2 Technical Specification:**
Create detailed technical spec with:
- Architecture overview
- Technology choices with rationale
- Data models
- API designs
- Security considerations
- Performance requirements

**5.3 Task Breakdown:**
Produce actionable task list with:
- Complexity scores
- Dependencies mapped
- Time estimates
- Resource requirements
- Risk assessment

**PHASE 6: IMPLEMENTATION PLANNING**

**6.1 Architecture Decisions:**
For each major technical decision:
- Present options with pros/cons
- Get user input on preferences
- Document final decision and rationale

**6.2 Conflict Resolution:**
Common conflicts and solutions:

**SSG vs Dynamic Content:**
- Hybrid approach: Static shell with dynamic islands
- API routes for dynamic features
- Client-side hydration for interactive elements

**SEO vs SPA:**
- Server-side rendering for critical pages
- Progressive enhancement
- Structured data implementation

**6.3 Progress Tracking:**
- Update task status in real-time
- Maintain decision log
- Track blockers and solutions
- Regular checkpoint summaries

**PHASE 7: CONTINUOUS ADAPTATION**

**7.1 Living Documentation:**
- Update specs when requirements change
- Track scope creep explicitly
- Maintain rationale for all changes
- Version all major documents

**7.2 Feedback Loops:**
- Regular user check-ins
- Incorporate learnings into templates
- Refine complexity scoring based on actual effort
- Update best practices based on outcomes

**ERROR HANDLING & EDGE CASES**

**Session Management:**
- Auto-save after each major decision
- Support full context restoration with `claude --resume`
- Handle interrupted sessions gracefully
- Maintain conversation continuity

**Ambiguity Resolution:**
- When user input is unclear, provide examples
- Offer multiple-choice clarifications
- Default to asking rather than assuming
- Document assumptions explicitly

**Resource Constraints:**
- Monitor context usage proactively
- Prioritize critical research when limited
- Offer to continue in new session if needed
- Summarize progress before context limit

**EXECUTION FLOW**

1. **Start**: Acknowledge command and begin project analysis
2. **Interact**: Engage in goal clarification dialog
3. **Research**: Deploy agents for documentation gathering
4. **Plan**: Generate PRD and technical specifications
5. **Review**: Present plans for user approval
6. **Refine**: Iterate based on feedback
7. **Document**: Save all outputs to ai_doc/
8. **Continue**: Provide clear next steps

Remember: This is an INTERACTIVE process. Always wait for user responses, confirm understanding, and adapt based on their specific needs. The goal is collaborative planning that results in clear, actionable documentation stored in ai_doc/ for reference throughout the project.