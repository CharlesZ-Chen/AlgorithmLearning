https://ipfs.io/ipfs/bafykbzacecpbuvilbmokyl2ajs2hbve3qaut4h3vtir6htydecy2h5jaqmy7i?filename=Alex%20Yu%20-%20System%20Design%20Interview_%20An%20Insider%E2%80%99s%20Guide-Independently%20published%20%282020%29.pdf

#### 4 steps Framework for System Design

- Step 1: Understanding the problem & build design scope ( 3 - 10 mins)

 - clarify requirements:
  - Functional/Features
  - Non-Functional/Exepectations

 - clarify scalabilities:
   - # of users
   - how fast to scale up? 3, 6, 12 months?

 - clarify current state of systems:
   - existing services may leverage
   - current tech stack

 - Step 2: Propose high-level design and get buy-in (10 - 15 mins)

  - blueprint
  - box diagram with key components
  - request flow
  - Back-of-Envelope estimation -- clarify it this is needed before dive
  - walk through a few key concrete use cases 

- Step 3: Design deep dive (~ 25 mins)
  - Pre-req: following objectives should already achieved:
    - agreed on overall goals & feature scope
    - sketched out a high-level blueprint / box diagram for the overall design
    - Obtained high-level feedback on this high-level design
    - Identified initial areas to deep dive based on feedback

  - Key: identify & prioritize components in the architecture

  - **note**: AVOID dive into unnessary details

 - Step 4: Wrap up (~ 5 mins)
  - identify system bottlenecks, what are potential improvements?
  - give a recap of proposed design. Summarize tradeoffs between multiple solutions, if any.
  - discuss about error cases, and their handling / mitigations
  - discuss about monitor metrics & error logs
  - how to roll out the system
  - how to handle the next scale curve
