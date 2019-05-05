---
layout: post
title: On My First Product
published: true
---

I built my first, end-to-end, unsupervised, wholly-owned product this year. Here are my tweet-length thoughts in between manual build-deploy cycles.

# What I learned:

Because I'd observed many codebases around me - including my own - suffering from lack of planning, structure or oversight, I aimed to implement a backend specified by thorough testing, API specifications, product documentation, technical documentation, full CD/CI pipeline, not to mention a fully detailed and all-in UX design process full of interviews, personas, writeups, multiple mockups and wireframes and an interactive prototype.

It was great, in that I learned in broad but meaningful stroke how to go about producing robust, well-understood software with a clear roadmap and direction

The problem was that all this came before a relevant prototype was built or even a hello-world to test the deployment flow. To be clear, a prototype was built that did the thing - that is, integrate some APIs and aggregate data together in a clean UI. But this was completely overhauled with the production of the system proper. This meant loads of time spent developing the backbone of the system, including all the specs and tests mentioned with lots of functionality written, without having run anything even once! Of course, come first test runs and first deployments, everything was always breaking, because it was written from the armchair - a bizarre approach I will never recreate. I had essentially misunderstood TDD at the beginning, holding back my team because I was cocky about the transition to a whole-new stack of tech and paradigms.

What I should have done was test-drive the CD CI flow with some trivial-ish integration + unit tests and an incrementally built codebase from the smallest working units upwards, ensuring at _every_ step that all tests pass. 

All was not lost of course. The mistakes I made were recovered from. And though I'll never get that lost time back, I can avenge its passing by never making those mistakes again.

# What I would keep:

1. I've never been more sold on TDD in my life
2. Serverless when it's not _impedient_
3. API specifications and mock servers. I'm extremely sold on this.
4. seed.run - it's great. It gives me sufficient CD features, with logs, monitoring, rollbacks, one-click deploys, AWS Secret Manager integration and more, with a pleasant interface.

# What I wouldn't do again:

1. 100% new stack and product lifecycle
2. Serverless when it isn't needed
3. Concerning myself with scalability before we hit scaling issues
4. Not following the following approach:
  1. First, a hello-world that runs locally and deploys smoothly with our CDCI flow
1. Not using AWS Secrets Manager
5. Not seeing a psychoanalyst about my fetish for refactoring?