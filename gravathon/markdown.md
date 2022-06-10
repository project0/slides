## Gravathon™

_AutoScout24 ARM/AWS Graviton adoption hackathon_

<!-- vertical -->

### What is Graviton

#### Graviton is the ARM implementation designed by AWS

> ARM is RISC (Reduced Instruction Set Computing)​

> X86 (Intel/AMD) is CISC (Complex Instruction Set Computing)​

<!-- vertical -->

### Why?

- Save compute costs up tp 30%
- Better efficiency and performance/costs balance
- Reduce carbon footprint and help saving the planet!

<!-- section -->

### Support ARM in Application Platform products

- Base Docker Images
- OneScout AMI
- Fizz Agents
- Infinity

<!-- vertical -->

### Challenges

- Build multi-arch docker images
- Extending clusters (fizz, infinity) with separates ARM nodegroups

<!-- section -->

### Gravathon - 02.06.2022

> 10 Engineers from Builder Teams

> Attempt to migrate 11 different services

> One infinity service fully migrated in production

<!-- vertical -->

### General learnings

- Lack of knowledge in teams about custom build and deploy scripts
- Do not upgrade versions and dependencies together with ARM switch
- Some projects use docker images for testing -> no ARM support.
- Small issue in AS24 AWS CDK (missing ability to override AMI)

<!-- vertical -->

### NodeJS learnings

> Lack of pre-compiled NodeJS modules

- outdated glibc in amazon linux 2 makes it hard to build all modules
- NodeJS version upgrade is not straight forward in some cases (compatibility and module upgrades)

<!-- vertical -->

### Java/Scala learnings

> Java >= 11 works fine

- Some issue with sbt

<!-- section -->

# Thank you!

**Richard Hillmann**

_Senior Platform Engineer_
