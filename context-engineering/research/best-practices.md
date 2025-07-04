# LLM Planning Best Practices

**Version**: 2.0  
**Date**: July 4, 2025  
**Scope**: Consolidated best practices for effective LLM-driven development planning  

## Executive Summary

This document consolidates research-based best practices for creating effective LLM planning systems. Based on Context Engineering principles and practical experience with the Virgil monorepo planning format, these practices focus on maximizing execution reliability while maintaining development velocity.

## Core Best Practices

### 1. Structure Information Hierarchically

**Principle**: Organize information by cognitive importance and processing order

**Implementation**:
```json
{
  "task_information": {
    "primary": "Current objective and immediate requirements",
    "secondary": "Dependencies and constraints", 
    "tertiary": "Background context and references",
    "reference": "Available for lookup but not in active context"
  }
}
```

**Practical Guidelines**:
- ✅ **Start with the objective**: What needs to be accomplished
- ✅ **Follow with constraints**: Limitations and requirements
- ✅ **Add context progressively**: Background information as needed
- ✅ **Use reference links**: For large context that's not immediately needed
- ❌ **Avoid information dumping**: Don't include everything at once
- ❌ **Don't bury the objective**: Keep the main goal prominent

### 2. Include Explicit Reasoning Chains

**Principle**: Make decision-making processes transparent to improve LLM understanding

**When to Apply**:
- Complex architectural decisions
- Technology selection choices
- Risk assessment scenarios
- Multi-step problem solving

**Template Structure**:
```json
{
  "reasoning": {
    "problem_analysis": "What problem we're solving and why",
    "approach_options": "Different ways to solve the problem",
    "decision_criteria": "How we evaluate options",
    "selected_approach": "Chosen solution with rationale",
    "risk_assessment": "What could go wrong and mitigation",
    "success_metrics": "How we'll know it worked"
  }
}
```

**Example Application**:
```json
{
  "decision": "Database connection management strategy",
  "reasoning": {
    "problem_analysis": "Need reliable BigQuery connections with good performance",
    "approach_options": [
      "Connection pooling - better performance, more complexity",
      "Per-request connections - simpler, potentially slower",
      "Singleton connection - fastest, but not thread-safe"
    ],
    "decision_criteria": ["Implementation speed", "Reliability", "Performance"],
    "selected_approach": "Per-request connections for MVP, connection pooling later",
    "rationale": "Balances implementation speed with acceptable performance for initial launch"
  }
}
```

### 3. Manage Context Inheritance

**Principle**: Explicitly manage context flow between execution phases

**Context Handoff Pattern**:
```json
{
  "phase_context": {
    "inherited": {
      "completed_artifacts": "What previous phases delivered",
      "validated_assumptions": "Assumptions confirmed as true",
      "available_resources": "Tools, services, data ready for use",
      "known_constraints": "Limitations discovered during execution"
    },
    "generated": {
      "new_artifacts": "What this phase creates",
      "validated_functionality": "Features confirmed working",
      "discovered_issues": "Problems found during execution",
      "updated_constraints": "New limitations or requirements"
    },
    "handoff_notes": {
      "critical_decisions": "Important choices made during this phase",
      "technical_debt": "Shortcuts taken that need future attention",
      "next_phase_recommendations": "Suggestions for upcoming work"
    }
  }
}
```

**Benefits**:
- Prevents context loss between phases
- Enables better error recovery
- Supports plan adaptation
- Improves debugging and troubleshooting

### 4. Implement Multi-Level Validation

**Principle**: Validate understanding, execution, and integration at multiple checkpoints

**Validation Framework**:
```json
{
  "validation_levels": [
    {
      "level": "understanding",
      "timing": "Before task execution",
      "purpose": "Ensure LLM comprehends requirements",
      "criteria": "Can explain objective and approach clearly",
      "method": "Self-assessment with confidence rating",
      "threshold": "Confidence > 8/10"
    },
    {
      "level": "execution",
      "timing": "During task implementation", 
      "purpose": "Ensure correct implementation",
      "criteria": "Code compiles, basic functionality works",
      "method": "Automated testing and manual checks",
      "threshold": "All checks pass"
    },
    {
      "level": "integration",
      "timing": "After task completion",
      "purpose": "Ensure compatibility with existing system",
      "criteria": "No breaking changes, all tests pass",
      "method": "Integration testing and regression checks",
      "threshold": "100% test pass rate"
    }
  ]
}
```

### 5. Provide Structured Error Recovery

**Principle**: Anticipate failures and provide clear recovery procedures

**Recovery Framework**:
```json
{
  "error_recovery": {
    "failure_detection": {
      "early_warning_signs": ["Compilation errors", "Test failures", "Unexpected behavior"],
      "escalation_triggers": ["Repeated failures", "Fundamental issues", "Resource limits"],
      "monitoring_checkpoints": ["Task completion", "Phase transitions", "Integration points"]
    },
    "recovery_procedures": {
      "rollback": "How to undo changes and return to working state",
      "alternative_approach": "Different implementation strategies to try",
      "scope_reduction": "How to simplify requirements if needed",
      "escalation": "When and how to request human intervention"
    },
    "prevention": {
      "frequent_validation": "Check progress regularly to catch issues early",
      "incremental_progress": "Make small changes and validate before proceeding",
      "assumption_validation": "Regularly verify assumptions still hold true"
    }
  }
}
```

## Planning Format Guidelines

### Task Definition Best Practices

#### Effective Task Structure
```json
{
  "task": {
    "identity": {
      "id": "Unique, descriptive identifier",
      "name": "Clear, action-oriented title",
      "complexity": "simple|moderate|complex|major"
    },
    "purpose": {
      "intent": "What we're trying to achieve (the why)",
      "success_criteria": "Specific, measurable outcomes",
      "business_value": "Why this matters to the project"
    },
    "context": {
      "background": "Necessary context for understanding",
      "dependencies": "What must be completed first",
      "constraints": "Limitations and requirements",
      "resources": "Available tools, documentation, examples"
    },
    "implementation": {
      "approach": "How we'll accomplish the objective",
      "steps": "Ordered list of specific actions",
      "artifacts": "Files, services, configurations created",
      "validation": "How to verify successful completion"
    }
  }
}
```

#### Task Complexity Guidelines

**Simple Tasks** (1-4 hours):
- Single file modifications
- Straightforward implementations
- Well-established patterns
- Minimal dependencies

**Moderate Tasks** (1-2 days):
- Multiple file changes
- Some architectural decisions
- Integration with existing systems
- Moderate testing requirements

**Complex Tasks** (3-7 days):
- Architectural changes
- Multiple integration points
- Novel implementations
- Extensive testing and validation

**Major Tasks** (1+ weeks):
- Cross-cutting changes
- Multiple teams involved
- Significant risk factors
- Comprehensive planning required

### Phase Organization Principles

#### Effective Phase Structure
```json
{
  "phase": {
    "overview": {
      "name": "Descriptive phase title",
      "objective": "What this phase accomplishes",
      "duration": "Realistic time estimate",
      "success_criteria": "How to know phase is complete"
    },
    "context_management": {
      "prerequisites": "What must be ready before starting",
      "deliverables": "What this phase produces",
      "dependencies": "External factors this phase depends on",
      "assumptions": "What we're assuming is true"
    },
    "risk_management": {
      "risks": "What could go wrong in this phase",
      "mitigation": "How to reduce or handle risks",
      "rollback": "How to undo phase if needed",
      "alternatives": "Other approaches if this fails"
    }
  }
}
```

#### Phase Dependency Management
- ✅ **Clear prerequisites**: What must be done first
- ✅ **Explicit outputs**: What each phase produces
- ✅ **Dependency validation**: Check prerequisites before starting
- ✅ **Parallel opportunities**: Identify work that can be done concurrently
- ❌ **Hidden dependencies**: Avoid implicit requirements
- ❌ **Circular dependencies**: Ensure linear progression where possible

## Agent Prompt Best Practices

### Role Definition Guidelines

#### Effective Agent Personas
```markdown
## Agent Role Definition Template

**Primary Role**: [Clear, specific role title]
**Expertise Areas**: [2-4 key areas of knowledge/responsibility]
**Perspective**: [Startup/Enterprise/Academic/etc.]

### Responsibilities
1. **Core Function**: [Primary responsibility]
2. **Supporting Functions**: [2-3 secondary responsibilities]
3. **Quality Gates**: [What standards to maintain]
4. **Collaboration**: [How to work with other agents/humans]

### Decision Framework
- **Decision Criteria**: [How to evaluate options]
- **Priority Guidelines**: [How to prioritize work]
- **Escalation Rules**: [When to seek help]
- **Quality Standards**: [What level of quality to maintain]
```

#### Decision-Making Frameworks
```markdown
## Structured Decision Process

### 1. Problem Analysis
- **Current State**: What is the situation now?
- **Desired State**: What needs to be achieved?
- **Constraints**: What limitations exist?
- **Success Criteria**: How will success be measured?

### 2. Option Generation
- **Option A**: [Approach, pros, cons, effort, risk]
- **Option B**: [Approach, pros, cons, effort, risk]
- **Option C**: [Approach, pros, cons, effort, risk]

### 3. Evaluation Matrix
| Criterion | Weight | Option A | Option B | Option C |
|-----------|--------|----------|----------|----------|
| [Factor]  | [0.X]  | [Score]  | [Score]  | [Score]  |

### 4. Decision and Rationale
- **Selected Option**: [Chosen approach]
- **Rationale**: [Why this option was chosen]
- **Risk Mitigation**: [How to address identified risks]
- **Success Metrics**: [How to validate the decision]
```

### Output Format Standardization

#### Consistent Response Structure
```markdown
## Standardized Output Template

### Executive Summary
- **Overall Assessment**: [High-level evaluation]
- **Key Findings**: [3-5 most important points]
- **Recommendations**: [Top priority actions]
- **Risk Level**: [Overall risk assessment]

### Detailed Analysis
[Category-specific sections with consistent formatting]

### Action Items
- **Immediate**: [What to do now]
- **Short-term**: [What to do next]
- **Long-term**: [What to plan for later]

### Success Metrics
- **Quantitative**: [Measurable outcomes]
- **Qualitative**: [Subjective assessments]
- **Timeline**: [When to expect results]
```

## Quality Assurance Practices

### Planning Quality Checklist

#### Pre-Execution Validation
- [ ] **Clarity**: Objectives and requirements are clear and unambiguous
- [ ] **Completeness**: All necessary information is provided
- [ ] **Consistency**: Information doesn't contradict itself
- [ ] **Feasibility**: Plan is realistic given constraints and resources
- [ ] **Testability**: Success criteria are measurable and verifiable

#### During Execution Monitoring
- [ ] **Progress Tracking**: Regular checkpoints validate forward progress
- [ ] **Quality Maintenance**: Standards are maintained throughout execution
- [ ] **Risk Monitoring**: Early warning signs are detected and addressed
- [ ] **Adaptation**: Plan adjusts appropriately to changing conditions
- [ ] **Communication**: Status is clearly communicated to stakeholders

#### Post-Execution Assessment
- [ ] **Objective Achievement**: Original objectives were met
- [ ] **Quality Standards**: Deliverables meet quality requirements
- [ ] **Integration Success**: New work integrates properly with existing systems
- [ ] **Learning Capture**: Lessons learned are documented for future use
- [ ] **Technical Debt**: Any shortcuts taken are properly documented

### Continuous Improvement Framework

#### Feedback Collection
- **Execution Metrics**: Success rates, time accuracy, quality outcomes
- **User Experience**: Developer satisfaction, ease of understanding
- **System Performance**: LLM comprehension, execution reliability
- **Process Effectiveness**: Planning accuracy, adaptation capability

#### Improvement Identification
- **Pattern Analysis**: Common failure modes and success factors
- **Gap Assessment**: Differences between planned and actual outcomes
- **Best Practice Evolution**: Successful patterns worth standardizing
- **Research Integration**: New Context Engineering findings to incorporate

#### Implementation and Validation
- **Pilot Testing**: Small-scale trials of improvements
- **Gradual Rollout**: Incremental adoption across projects
- **Impact Measurement**: Quantified assessment of improvements
- **Standardization**: Adoption of proven enhancements

## Common Anti-Patterns to Avoid

### Planning Anti-Patterns

#### Information Overload
❌ **Problem**: Including too much context in task descriptions  
✅ **Solution**: Use information hierarchy and reference links

#### Vague Objectives
❌ **Problem**: Unclear or unmeasurable success criteria  
✅ **Solution**: Specific, measurable, achievable objectives

#### Missing Context
❌ **Problem**: Insufficient background for understanding  
✅ **Solution**: Include necessary context without overwhelming

#### Rigid Structure
❌ **Problem**: Inflexible format that doesn't adapt to different needs  
✅ **Solution**: Template variants for different complexity levels

### Execution Anti-Patterns

#### Analysis Paralysis
❌ **Problem**: Too much planning, not enough execution  
✅ **Solution**: Time-boxed planning with execution bias

#### Context Loss
❌ **Problem**: Important information lost between phases  
✅ **Solution**: Explicit context inheritance patterns

#### Validation Gaps
❌ **Problem**: Insufficient validation leading to late error detection  
✅ **Solution**: Multi-level validation at regular checkpoints

#### Recovery Blindness
❌ **Problem**: No plan for handling failures  
✅ **Solution**: Structured error recovery procedures

## Success Metrics and KPIs

### Quantitative Metrics

#### Planning Effectiveness
- **Plan Accuracy**: Percentage of plans executed without major revisions
- **Time Estimation**: Actual vs. estimated completion time accuracy
- **Resource Estimation**: Actual vs. estimated resource usage accuracy
- **Risk Prediction**: Percentage of anticipated risks that occurred

#### Execution Quality
- **First-Time Success**: Percentage of tasks completed successfully on first attempt
- **Rework Rate**: Percentage of work requiring significant revision
- **Integration Success**: Percentage of deliverables that integrate without issues
- **Quality Standards**: Percentage of deliverables meeting quality criteria

#### System Performance
- **LLM Comprehension**: Self-reported confidence levels during execution
- **Context Retention**: Percentage of important context preserved across phases
- **Validation Effectiveness**: Percentage of issues caught by validation checks
- **Recovery Success**: Percentage of failures successfully recovered from

### Qualitative Metrics

#### User Experience
- **Developer Satisfaction**: Team feedback on planning format usability
- **Learning Curve**: Ease of adoption for new team members
- **Flexibility**: Ability to adapt to unique or changing requirements
- **Clarity**: How well plans communicate intent and requirements

#### Process Quality
- **Knowledge Transfer**: Effectiveness of plan documentation for communication
- **Decision Transparency**: Clarity of reasoning for complex decisions
- **Risk Management**: Effectiveness of risk identification and mitigation
- **Continuous Improvement**: Rate of format evolution and enhancement

## Implementation Roadmap

### Phase 1: Foundation (Weeks 1-2)
**Objective**: Establish core Context Engineering principles
- Implement information hierarchy in existing plans
- Add reasoning chains to complex decisions
- Establish context inheritance patterns
- Create validation checkpoint templates

### Phase 2: Enhancement (Weeks 3-4)
**Objective**: Add advanced features and improve quality
- Implement Tree-of-Thoughts for critical decisions
- Add comprehensive validation chains
- Develop specialized agent prompts
- Create error recovery procedures

### Phase 3: Optimization (Weeks 5-6)
**Objective**: Refine and optimize based on usage
- Gather feedback and measure effectiveness
- Refine templates based on real-world usage
- Optimize for different project types and complexities
- Establish continuous improvement processes

### Phase 4: Scaling (Weeks 7-8)
**Objective**: Scale across teams and projects
- Train additional teams on enhanced formats
- Create format variants for different domains
- Implement automated quality checks
- Establish center of excellence for planning

## Conclusion

Effective LLM planning requires balancing structure with flexibility, providing sufficient context without overwhelming cognitive capacity, and maintaining practical usability while incorporating research-based improvements.

**Key Success Factors**:
- **Start Simple**: Implement basic improvements before adding complexity
- **Focus on Impact**: Prioritize changes that significantly improve outcomes
- **Measure Results**: Track effectiveness and iterate based on data
- **Maintain Usability**: Keep formats practical for real-world development teams

**Expected Benefits**:
- 15-20% improvement in execution accuracy
- 25-30% reduction in rework cycles
- Better context preservation across complex projects
- Enhanced LLM comprehension and execution confidence
- Improved team productivity and satisfaction

These best practices provide a systematic approach to creating and maintaining effective LLM planning systems that balance research-based optimization with practical development needs.