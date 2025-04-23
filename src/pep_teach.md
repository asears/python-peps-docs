# Teaching Python Enhancement Proposals (PEPs)

This guide provides strategies and recommendations for teaching and understanding recent Python Enhancement Proposals.

## Teaching Recent PEPs (2023-2024)

### PEP 729 - Typing Council
#### Teaching Approach
- Start by explaining Python's typing system evolution from optional typing to a more comprehensive type system
- Use real-world examples to demonstrate why a dedicated council for typing decisions is beneficial
- Highlight how this impacts developers:
  - More consistent typing decisions
  - Clearer path for typing-related proposals
  - Better coordination between type checkers

### PEP 749 - Type Annotations with Delayed Evaluation
#### Teaching Approach
- Begin with the challenges of the current type annotation system
- Demonstrate common issues that this PEP solves using before/after examples
- Key concepts to emphasize:
  - Improved forward references
  - Better memory usage
  - More intuitive behavior in modules

### PEP 770 - Software Bill of Materials (SBOM)
#### Teaching Approach
- Start with the importance of software supply chain security
- Use concrete examples of dependency tracking
- Practice exercises:
  - Creating an SBOM for a simple project
  - Reading and interpreting SBOM data
  - Understanding security implications

### PEP 773 - Safer Python Packaging
#### Teaching Approach
- Focus on security best practices
- Demonstrate common packaging vulnerabilities
- Hands-on exercises:
  - Secure package creation
  - Verification of package integrity
  - Understanding distribution security

### PEP 782 - Isolated Subinterpreters
#### Teaching Approach
- Compare with existing concurrency models (threads, multiprocessing)
- Show use cases where subinterpreters excel
- Practical examples:
  - Creating isolated environments
  - Data sharing between interpreters
  - Performance comparisons

### PEP 783 - Tagged Unions in Memory
#### Teaching Approach
- Start with basic memory layout concepts
- Use diagrams to show memory optimization
- Practical demonstrations:
  - Memory usage before/after
  - Performance impact
  - Common use cases

## General Teaching Guidelines

1. **Progressive Learning**
   - Start with basic concepts
   - Build up to more complex implementations
   - Use real-world examples

2. **Hands-on Practice**
   - Provide code examples
   - Include exercises
   - Encourage experimentation

3. **Context and Motivation**
   - Explain why each PEP was needed
   - Show real-world problems it solves
   - Discuss impact on Python ecosystem

4. **Visual Learning**
   - Use diagrams where appropriate
   - Show before/after comparisons
   - Demonstrate practical implementations

## Common Pitfalls to Address

1. **Complexity Management**
   - Break down complex PEPs into digestible parts
   - Use incremental learning approaches
   - Provide plenty of examples

2. **Version Compatibility**
   - Clearly indicate which Python versions support each feature
   - Discuss backward compatibility considerations
   - Address migration strategies

3. **Integration Challenges**
   - Show how new features work with existing code
   - Discuss common integration issues
   - Provide migration patterns