# AI Documentation System

This folder contains all documentation, research, and planning materials generated and used by the Interactive Research & Planning Command.

## Structure

### 📁 research/
External documentation and research materials fetched during planning phases.

- **tech_stack/**: Framework and library documentation
- **integrations/**: Third-party API documentation  
- **best_practices/**: Industry standards and patterns

### 📁 planning/
Generated planning documents for the project.

- **prd_v[N].md**: Product Requirements Documents (versioned)
- **tech_spec_v[N].md**: Technical Specifications (versioned)
- **task_breakdown.md**: Complexity analysis and task decomposition
- **architecture_decisions.md**: Key technical decisions with rationale

### 📁 progress/
Living documentation that evolves with the project.

- **completed_tasks.md**: Track of completed work
- **blockers.md**: Current obstacles and challenges
- **decisions.md**: Decision log with context and rationale
- **lessons_learned.md**: Insights for future reference

### 📁 templates/
Reusable patterns and templates for common project types.

- **barbershop_booking.md**: Template for barbershop with Square integration
- **ecommerce_store.md**: E-commerce site patterns
- **blog_site.md**: Content-focused site patterns
- **saas_app.md**: SaaS application patterns

### 📄 project_context.json
Persistent storage of project information, goals, and preferences. This file is automatically maintained by the planning command.

## Usage

### Running the Research & Planning Command

```bash
claude --custom-command .claude/commands/research_and_plan.md
```

This will:
1. Analyze your current project
2. Engage in interactive planning dialog
3. Generate comprehensive documentation
4. Store everything in this folder

### Resuming a Session

When using `claude --resume`, the command will:
- Load project_context.json
- Review recent progress
- Continue from the last checkpoint

### Manual Updates

While the command maintains these files automatically, you can:
- Add your own research to `research/`
- Create new templates in `templates/`
- Update project_context.json with new requirements

## Best Practices

1. **Version Control**: Commit ai_doc changes to track planning evolution
2. **Regular Reviews**: Periodically review and update outdated documentation
3. **Template Reuse**: When starting similar projects, copy relevant templates
4. **Decision Tracking**: Always document why decisions were made, not just what

## Integration with Development

The documentation here should be referenced throughout development:
- Use task_breakdown.md for sprint planning
- Refer to tech_spec for implementation details
- Update progress/ files as work completes
- Check architecture_decisions.md before major changes