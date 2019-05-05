---
layout: post
title: How to Implement OAuth Serverlessly - How to Verify State and Carry Over RedirectURLs
published: true
---

I'm not sure how many devs out there need such a guide. The barrier to entry for serverless isn't quite so low as too entice many developers building out their first APIs without the necessary experience to anticipate the workarounds and concessions they'd have to make before attempting it, not least due to still niche nature of serverless. But as it happens I was struggling to preserve redirect URLs and state between endpoint invocations for OAuth 2.0 Auth flow without any session storage, and I figured I couldn't have been the first. 

( I apologize if I can't decide on whether "front end" has a space or not )

# The problem with State

Typically an `/authCode` endpoint will generate a random string for `state`, a kind of unique token verifying that the Auth provider is who they claim to be. The provider will request the `/callback` endpoint with a `state` parameter in the query string. The endpoint should verify that this is the same `state` generated from the `/authCode` handler. 

Typically this is done by holding the state in session. I've also heard of using the logged in user's session ID for state - however if you're keeping session ID visible to the web client, it may be accessible to someone simultaneously coordinating a MTM attack while you while you use their 'free' hotspot. Either way, some sort of persistent storage maintained by the backend is responsible for associating for endpoint invocations. 

This is good for many "serverless" backends as they maintain session, despite the mating call of statelessness, for token invalidation and the like. However, if you're keeping your token TTLs short you can probably get away with not having session. In our case our TTLs were about 20 minutes ( as is NYU IT policy for active sessions ) and we felt it unnecessary to actively invalidate tokens. This may or may not have been a good choice, perhaps more on this later.

So, what do you do if you don't want to maintain any persistence for state?

# Or redirect URLs for that matter

The other thing you'd want to preserve between the first invocation the frontend makes with your API and the eventual redirect, is where exactly to redirect to. That is, you want to redirect the client to a URL they first set in the parameters to the `/authCode` endpoint. You probably don't want hardcode your redirect URL in the backend, not least because you might have multiple clients hosted at different URLs, and also to decouple the particulars of your frontend from that of the backend.

So how do you preserve state and redirect URL?

# Stateless "session" across requests : Cookies

Just set two cookies for State and Redirect in the response of your `/authCode` URL. That's it.

Your provider will pass the cookies along as it makes its callback request.
