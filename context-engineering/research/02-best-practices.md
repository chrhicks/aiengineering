# LLM Planning Best Practices

**Version**: 3.0  
**Date**: July 5, 2025 (Updated with production insights)  
**Scope**: Consolidated best practices for effective LLM-driven development planning with Context Engineering  
**New Research**: Production patterns from Claude Code, Anthropic, ChatGPT, and context failure analysis

## Executive Summary

This document consolidates research-based best practices for creating effective LLM planning systems. Updated with new Context Engineering insights from production systems and context failure mode analysis, these practices focus on maximizing execution reliability while preventing the four major context failure modes: poisoning, distraction, confusion, and clash.

## Core Best Practices

### 0. Master/Child Agent Delegation (NEW - Critical Pattern)

**Principle**: Use master agent for coordination, child agents for isolated task execution

**Why This Matters**: Prevents context pollution from intermediate reasoning while maintaining coordination benefits.

**Evidence**: Anthropic's multi-agent researcher used 15x more tokens but achieved better results through context isolation.

**Implementation Pattern**:
```json
{
  "agent_coordination": {
    "master_responsibilities": [
      "High-level objective tracking",
      "Task decomposition and sequencing", 
      "Integration of child agent results",
      "Progress monitoring and decision making"
    ],
    "child_responsibilities": [
      "Isolated task execution with focused context",
      "Research and information gathering",
      "Testing and validation",
      "Specialized analysis and processing"
    ],
    "handoff_protocol": {
      "clean_delegation": "Master provides only task-specific context to children",
      "structured_returns": "Children return only results, no reasoning traces",
      "error_isolation": "Child failures don't pollute master context",
      "sequential_execution": "Master waits for child completion before proceeding"
    }
  }
}
```

**Practical Guidelines**:
- ✅ **Delegate research tasks**: Use child agents for documentation lookup and analysis
- ✅ **Isolate testing**: Run tests in child agent contexts to avoid error pollution
- ✅ **Separate validation**: Use child agents for code review and compliance checking
- ✅ **Clean handoffs**: Children return structured results only, no intermediate thinking
- ❌ **Avoid role-playing**: Don't simulate multiple agents in single context
- ❌ **Don't share reasoning**: Keep child agent thinking isolated from master

### 1. Context Failure Prevention (NEW - Production-Critical)

**Principle**: Actively monitor and prevent the four major context failure modes

**Context Failure Modes**:

#### Context Poisoning Prevention
```json
{
  "poisoning_prevention": {
    "detection_triggers": [
      "Repeated impossible goals or objectives",
      "Contradictory facts appearing in context",
      "Fabricated references or non-existent resources"
    ],
    "mitigation_strategies": [
      "Context quarantine for uncertain information",
      "Confidence thresholds for generated content",
      "Rollback to last validated state on detection"
    ]
  }
}
```

#### Context Distraction Mitigation
```json
{
  "distraction_mitigation": {
    "performance_thresholds": {
      "large_models": "Monitor degradation beyond 100k tokens",
      "smaller_models": "Monitor degradation beyond 32k tokens"
    },
    "prevention_strategies": [
      "Auto-compaction at 95% context window capacity",
      "Dynamic context loading instead of front-loading",
      "Prioritize current task over historical context"
    ]
  }
}
```

#### Context Confusion Prevention
```json
{
  "confusion_prevention": {
    "relevance_filtering": [
      "Semantic tool selection for large tool sets",
      "Remove irrelevant information before LLM calls",
      "Dynamic loading of context based on current needs"
    ],
    "tool_management": [
      "Use RAG for tool selection when >10 tools available",
      "Provide only relevant tools for current task",
      "Remove tool descriptions that don't match current objective"
    ]
  }
}
```

#### Context Clash Resolution
```json
{
  "clash_resolution": {
    "conflict_detection": [
      "Information versioning and source tracking",
      "Contradiction detection between context sources",
      "Validation of information consistency"
    ],
    "resolution_procedures": [
      "Explicit conflict resolution protocols",
      "Source prioritization rules",
      "Human escalation for unresolvable conflicts"
    ]
  }
}
```

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

### 4. Auto-Compaction System (NEW - Claude Code Pattern)

**Principle**: Automatically compress context when approaching window limits

**Why This Matters**: Prevents context window overflow while preserving essential information.

**Evidence**: Claude Code's production auto-compact system at 95% threshold

**Implementation Framework**:
```json
{
  "auto_compaction": {
    "trigger_threshold": "95% of context window capacity",
    "summarization_strategy": {
      "trajectory_summarization": "Compress user-agent interaction patterns",
      "decision_preservation": "Always retain critical decisions and rationale",
      "tool_output_compression": "Summarize verbose tool responses",
      "error_pattern_retention": "Keep error patterns for learning"
    },
    "rollback_capability": {
      "checkpoint_creation": "Save state before major operations",
      "rollback_triggers": ["Validation failures", "Context poisoning detection"],
      "recovery_procedure": "Return to last known good state with clean context"
    }
  }
}
```

**Practical Guidelines**:
- ✅ **Monitor usage**: Track context window utilization continuously
- ✅ **Preserve essentials**: Always retain critical decisions and validation results
- ✅ **Compress interactions**: Summarize user-agent conversation patterns
- ✅ **Enable rollback**: Maintain checkpoints for error recovery
- ❌ **Don't compress blindly**: Preserve information needed for current task
- ❌ **Avoid lossy compression**: Don't lose critical decision rationale

### 5. Include Explicit Reasoning Chains

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

### 6. Context Engineering Workflow Integration (NEW)

**Principle**: Apply systematic Write/Select/Compress/Isolate strategies

**Why This Matters**: Provides structured approach to context management as engineering discipline.

**Evidence**: LangChain's four-strategy framework from production analysis

**Four-Strategy Implementation**:

#### Write Context Strategy
```json
{
  "write_context": {
    "scratchpads": "Use task state for intermediate decisions and progress tracking",
    "memories": "Persist critical decisions and patterns across phases",
    "state_management": "Isolate working memory from long-term context",
    "persistence_triggers": ["Phase completions", "Critical decisions", "Error patterns"]
  }
}
```

#### Select Context Strategy  
```json
{
  "select_context": {
    "dynamic_loading": "Just-in-time context retrieval based on current needs",
    "relevance_filtering": "Include only context relevant to immediate task",
    "tool_selection": "Semantic search for large tool sets",
    "memory_retrieval": "Fetch relevant past decisions and patterns"
  }
}
```

#### Compress Context Strategy
```json
{
  "compress_context": {
    "summarization_triggers": "Auto-compact at threshold or phase transitions",
    "essential_preservation": "Always retain critical decisions and validation results",
    "trajectory_compression": "Summarize interaction patterns",
    "tool_output_compression": "Compress verbose tool responses"
  }
}
```

#### Isolate Context Strategy
```json
{
  "isolate_context": {
    "agent_delegation": "Use child agents for research, testing, validation",
    "sandbox_isolation": "Run tool calls in isolated environments",
    "state_partitioning": "Separate coordination from execution context",
    "context_boundaries": "Clear handoff protocols between agents"
  }
}
```

### 7. Manage Context Inheritance

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

### 9. Provide Structured Error Recovery

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

#### Context Engineering Effectiveness (NEW)
- **Context Failure Rate**: Percentage of tasks affected by poisoning/distraction/confusion/clash
- **Context Window Utilization**: Average and peak context usage across tasks
- **Auto-Compaction Frequency**: How often context compression is triggered
- **Child Agent Success Rate**: Percentage of delegated tasks completed successfully

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
- **Token Efficiency**: Useful work accomplished per token consumed

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

### Phase 1: Context Engineering Foundation (Weeks 1-2)
**Objective**: Implement production-validated context engineering patterns
- Implement master/child agent delegation framework
- Add context failure detection and monitoring
- Establish dynamic context loading strategies
- Create auto-compaction system design

### Phase 2: Core Enhancement (Weeks 3-4)
**Objective**: Integrate advanced context management features
- Implement Write/Select/Compress/Isolate workflow
- Add reasoning chains with context isolation
- Establish context inheritance with failure prevention
- Create comprehensive validation with context health checks

### Phase 3: Advanced Features (Weeks 5-6)
**Objective**: Add sophisticated decision-making and optimization
- Implement Tree-of-Thoughts for critical decisions
- Add context quality metrics and monitoring
- Develop specialized agent coordination patterns
- Create adaptive context management strategies

### Phase 4: Production Optimization (Weeks 7-8)
**Objective**: Scale and optimize for production use
- Measure context engineering effectiveness
- Optimize token usage and cost efficiency
- Refine based on production feedback
- Establish context engineering best practices center

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