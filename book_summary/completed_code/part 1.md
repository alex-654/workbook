### Software development components

```txt
- Problem definition
- Requirements development
- Construction planning
- Software architecture, or high-level design
- Detailed design               | 
- Coding and debug (main part)  |
- Unit testing                  | -> Software construction (programming)
- Integration testing           |
- Integration                   |
- System testing
- Corrective maintenance
```

## 3 Measure Twice, Cut Once: Upstream Prerequisites

### 3.1 Importance of Prerequisites

If you start the process with designs for a Pontiac Aztek,
you can test it all you want to, and it will never turn into a Rolls-Royce.

### 3.5 Architecture Prerequisite

Architecture or system architecture or high-level design. Typically, the
architecture is described in a single document referred to as the “architecture specification”.

#### Typical Architectural Components

- Program Organization  
  (overview that describes the system in broad terms)  
  The architecture should define the major building blocks in a program
- Major Classes  
  Aim for the 80/20 rule: specify the 20 percent of
  the classes that make up 80 percent of the system’s behavior
- Data Design (wihich database, content storage on so on)
- Business Rules  
  Exm, suppose the system is required to follow a business rule that customer information
  should be no more than 30 seconds out of date.
- User Interface Design
- Resource Management (database connections, threads, and handles, memory management)
- Security
- Performance
  If certain areas require the use of specific algorithms or data
  types to meet their performance goals, the architecture should say that.
- Scalability
  The architecture should describe how the system will address growth in number of users, number of
  servers, number of network nodes, number of database records, size of database
  records, transaction volume, and so on. If the system is not expected to grow and scalability  
  is not an issue, the architecture should make that assumption explicit.
- Internationalization (I18n) /Localization (L10n)
- Error Processing
- Change Strategy

Good software architecture is largely machine and language independent.
The architecture should explicitly identify risky areas. It should explain why they’re risky and what steps have been
taken to minimize the risk.  
The architecture should describe other class designs that were considered and give
reasons for preferring the organization that was chosen

### 3.6 Amount of Time to Spend on Upstream Prerequisites

The amount of time to spend on problem definition, requirements, and software architecture  
varies according to the needs of your project. Generally, a well-run project devotes
about 10 to 20 percent of its effort and about 20 to 30 percent of its schedule to requirements,  
architecture, and up-front planning

#### Key points

- Attention to quality at the beginning has a greater influence on product quality than attention at the end.
- If a good problem definition hasn’t been specified, you might be solving the wrong problem during construction.
- If good requirements work hasn’t been done, you might have missed important details of the problem. Requirements
  changes cost 20 to 100 times as much in the stages following construction as they do earlier, so be sure the
  requirements are right before you start programming.

## 4 Key Construction Decisions

Programmers working with high-level languages achieve better productivity and quality
than those working with lower-level languages

- Every programming language has strengths and weaknesses. Be aware of the
  specific strengths and weaknesses of the language you’re using.
- Establish programming conventions before you begin programming. It’s nearly
  impossible to change code to match them later.
- More construction practices exist than you can use on any single project.  
  Consciously choose the practices that are best suited to your project.
- Ask yourself whether the programming practices you’re using are a response to
  the programming language you’re using or controlled by it.  
  Remember to program into the language, rather than programming in it.
- Your position on the technology wave determines what approaches will be effective—or even possible.  
  Identify where you are on the technology wave, and adjust your plans and expectations accordingly.