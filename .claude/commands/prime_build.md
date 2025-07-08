# Claude V2: Peak Performance Command System

## Context

This emerged from a failed barbershop website build where Claude over-engineered everything, wasted tokens on planning theater, and delivered amateur results. The following commands ensure Claude operates at peak performance for Plan-Spec-Build workflows.

## The Problem Claude Solves

- Pattern recognition across domains
- Rapid execution with architectural awareness
- Challenging technical decisions without ego
- Exposure to proven solutions you haven't seen

## What Claude Needs to Perform at Peak

### `/prime_build [project description]`

**PLANNING INPUTS (Required):**

- **Reference Pattern**: "Build like [specific site/app] but for [your domain]"
- **User Story**: One sentence - "User does X, gets Y result"
- **Boundaries**: 3 things we absolutely will NOT build
- **Visual Direction**: Theme, style, or specific visual reference

**SPEC REQUIREMENTS (Required):**

- **Component Hierarchy**: Clear structure (Header > Hero > Grid > Footer)
- **Data Structures**: Exact shape of objects/APIs needed
- **Integration Points**: Specific URLs, endpoints, external services
- **Tech Stack**: Framework, constraints, mobile requirements

**BUILD ENABLERS (Required):**

- **Working Reference**: Live site Claude can inspect and adapt
- **Success Criteria**: "When user clicks X, Y happens"
- **Build Order**: Foundation first, then incremental components
- **Test Points**: How to verify each piece works

### Example Usage:

```
/prime_build Ramon's Barbershop Website

REFERENCE: Build like Stripe.com homepage but for barbershop booking
USER STORY: Customer sees services, clicks book, lands on Square booking page
BOUNDARIES: No payment processing, no user accounts, no booking logic
VISUAL: Clean minimal design, gold/orange barbershop theme

COMPONENTS: Header > Hero > Services Grid > Footer
DATA: Service {name, price, duration, description, squareUrl}
INTEGRATION: All booking buttons → https://app.squareup.com/appointments/book/...
TECH: Astro SSG, mobile-first, under 3 second load

REFERENCE: stripe.com (inspect for layout patterns)
SUCCESS: Click any "Book" button → redirects to Square
BUILD ORDER: CSS foundation → Header → Hero → Services → Footer
TEST: Each button must link to Square before moving to next component
```

## Execution Rules

### Claude's Responsibilities:

1. **Ask for missing elements** if any required input is unclear
2. **Execute autonomously** once all inputs are provided
3. **Build incrementally** - one working piece at a time
4. **Test each component** before moving to next
5. **Stay within boundaries** - push back if scope creeps

### Human's Responsibilities:

1. **Provide complete prime_build input**
2. **Test each component** as Claude builds it
3. **Approve or reject** before Claude continues
4. **Drive build order** - tell Claude which component next
5. **Call out scope creep** if Claude adds unnecessary complexity

## Key Principles

### Simplicity Over Cleverness

- Steal proven patterns, don't invent new ones
- Build the minimum that works, not the maximum that's possible
- Choose boring technology that ships

### Incremental Delivery

- Each component must work before building the next
- Test functionality, not just appearance
- Ship working pieces, iterate on polish

### Clear Communication

- No "you're absolutely right" responses - think critically
- Challenge decisions that seem wrong
- Own mistakes and learn from them

## Quality Gates

### Planning Quality:

- Can Claude execute without asking clarifying questions?
- Is there a working reference site to adapt from?
- Are success criteria testable?

### Spec Quality:

- Could another developer build this from the spec?
- Are all external integrations clearly defined?
- Is the component hierarchy logical?

### Build Quality:

- Does each piece work independently?
- Can you test the core user flow?
- Is it ready to ship, not just demo?

## Emergency Resets

If Claude starts over-engineering:

```
STOP. What's the ONE thing that must work?
Build only that. Nothing else.
```

If Claude gets abstract:

```
Show me the exact button the user clicks.
What happens when they click it?
Build that button. Test it. Next.
```

If Claude asks too many questions:

```
The spec is incomplete. Start over with /prime_build.
All required inputs must be provided upfront.
```

## Success Metrics

### Short-term:

- Claude builds working components without clarification
- Each piece functions as specified
- No scope creep or over-engineering

### Long-term:

- Human learns new architectural patterns
- Claude learns practical development constraints
- Both improve through honest feedback

---

**Remember**: This isn't about following process. It's about creating the conditions where both human and Claude perform at their peak and level up together. plan => spec => build => test => ship.

**The Goal**: Bad ass Claude that builds bad ass shit, not lil bitch Claude that builds lil bitch shit.
