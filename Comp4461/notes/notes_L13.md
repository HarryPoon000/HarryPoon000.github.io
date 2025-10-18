# Comp 4461 Lecture 13 (16/10/2025): Evaluation
[Home Page](../../README.md) | [Previous Lecture](./notes_L12.md) | [Next Lecture]()

- [Comp 4461 Lecture 13 (16/10/2025): Evaluation](#comp-4461-lecture-13-16102025-evaluation)
- [Usability testing](#usability-testing)
  - [Aspects of Usability](#aspects-of-usability)
  - [Test Environment](#test-environment)
  - [Task Design](#task-design)
  - [Evaluation Metrics (More than Usability)](#evaluation-metrics-more-than-usability)
    - [Recording Methods](#recording-methods)
  - [Users](#users)

# Usability testing

## Aspects of Usability
- **Learnability**  
  – How easy is it for users to accomplish basic tasks the first time they encounter the design?

- **Efficiency**  
  – Once users have learned the design, how quickly can they perform the tasks?

- **Memorability**  
  – When users return to a design after a period of not using it, how easily can they reestablish proficiency?

- **Reliability**  
  – How many errors do users make, where are these errors occurring, and how easy is it to recover from these errors?

- **Satisfaction**  
  – How pleasant is it to use?


## Test Environment

- one way mirror: observe users from outside

- **Controlled**  
  – Monitoring and recording

- **Comfortable**  
  – Simulating the actual task environment  
  – Relaxing


##  Task Design

- simulate different context of use for people

- **Focus on user goals rather than system functionality**  
  – Specific, concrete user goals that describe a complete job (or interaction)

- **Depends on what you are looking for**  
  – Specific: how well does a task flow work?  
  – Broad: how well does your interpretation match users’ mental model?

- **Consider “the context of use”**  
  – What would someone need to do with your tool?  
  – Under what circumstances would they be in? E.g., relaxed vs. under pressure, non-interrupted vs. interrupted constantly, etc.

## Evaluation Metrics (More than Usability)

- **Performance**  
  – Task success, time on task, errors, efficiency

- **Issue**  
  – Identify issue, issue severity

- **Behavior**  
  – Observed verbal and non-verbal behavior  
  – Eye-tracking

- **Self-reported feedback on user experiences**  
  – “Think aloud”  
  – Interview and questionnaire: ease, satisfaction, clarity, comprehension, etc.

### Recording Methods

- Lots of different methods for doing this with various pros and cons:  
  – Paper and pencil (2nd observer helps)  
  – Audio recording  
  – Video recording  
  – System recording (user interaction with system, system reaction)

## Users

- **Who**  
  – People who will be using your design (target)  
  – People who can represent your target (proxy)

- **How Many**  
  – Still under debate in the community  
  – Rule of thumb: ~5  
    Number of usability problems found with n users is described by  
    \( N(1 - (1 - L)^n) \)  
    Where:  
    - \( N \) = total number of usability problems  
    - \( L \) = proportion of problems discovered on 1 user (typically, \( L = 31\% \))

- **Main argument**:  
  If you have 15 people, it’s better to test three designs with 5 users each, rather than one design with 15 people **in the early stage**.


