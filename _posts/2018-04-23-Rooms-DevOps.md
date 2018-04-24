---
layout: post
title: On Rooms' DevOps Architecture
published: true
---
Buzzword!

From what I understand, DevOps refers to a mentality or paradigm in bridging traditional _Ops_ or deployment roles and Development ones. It also refers to specific individuals at companies who manage this sort of thing? I'm not sure but I see a lot of job openings for "DevOps Engineers" so idk. 

In any case it seems like DevOps methodologies have merit and revolve around automating a lot of the fun parts of software deployment like managing server compute instances, uploading codebases to said instances and setting up db connections, etc /s.

What implementing DevOps ideas into this project means is just CD CI and the various steps along that route. What my DevOps flow/stack looks like is this:

- Business logic is 'modular' (*read: abusing dependency injections*)
- Unit tests everywhere
- Coverage to shame developers (*read: me*) who haven't written tests for their commits
- Linting to enforce a Singular Vision of Indentation&trade;, among other things
- Git hooks to reject commits for the above reasons 
- CodePipeline to essentially run `$serverless deploy`  when the commit pushes, depending on the branch in question
	- More specifics about Codebuild and endpoint testing should be in the readme of the repo by the time you read this

Now I can write my business logic (+ tests) and everything is automated for me! 