---
layout: post
title: On Rooms' Backend Architecture
published: true
---
Following the advice of a friend I realized that I could have more fun learning a completely new paradigm of backend development - serverless. More specifically, *Serverless*... Framework.

I decided on AWS Lambda to:
- Save money for my team
- Enable easy Alexa integration
- Challenge myself to think around a new paradigm of backend architecture
- Create a needlessly scalable service (for similar reasons to above)

At this point in time I realize that by using Serverless to abstract away API Gateway and Cloudformation defeats some of the purpose but I'm having fun learning about the underlying tech anyways!

I'm using DDB because it fits easily with AWS and Cloudformation/Serverless Resources without much config and I haven't used it in a prod env yet.

<!-- talk about vandium, classes, structure and lengevity plans -->

One of my main goals for this project is to decouple the infrustructure and business logic from the WSQ campus and the existing locations here - meaning I want this to be extensible easily for future devs here and for devs at the other NYU campuses if needed. It's also a challenge in software design to be as modular and future proof as possible.

I'm using Webpack+Babel to transpile from es7 to es5. I find it weird that I've become used to the (syntactically) hacky nature of promises to handle async functions so I welcome async/await as a way to return to more conventional try/catch logic. I am otherwise dependent on => functions and Classes so in conclusion, the future is now with Babel!

I'm using [Vandium](https://github.com/vandium-io/vandium-node) which is a handy "framework" (but feels more like a lib) for simplifying some of the more boilerplate Lambda NodeJS logic.

### Class structures

Essentially all locations need to provide the exact same functionality as far as I'm concerned - I just need to reconcile the differences in their APIs and their responses. This is naturally conducive to an OOP structure. There are no interfaces in JS and I'm not using TS. Why? I don't know it and haven't bothered to learn yet.

We can consider that every location must comply to the following requirements:

- We can list available rooms/roomTimes
- We can search for specific rooms
- We can reserve rooms

All this requires standardizing the following (or creating Models for):

- Rooms
- RoomTimes
- Reservation Requests
- Reservations (there's a way to combine this and the above)
- Locations

***What is a roomTime?***

Since we're concerned with reserving rooms for a temporary period our concerns are both temporal and spatial - this necessitates a model that represents a room during a specific time period. Thus each room has many roomTimes associated with it. 

**We face a problem** - not every location requires the same reservation request parameters. For example, some of the schools require a minimum of 2 occupants and their email addresses in addition to the reserver's. LaGuardia Coop's reservation system requires full name, current year standing, email address, NetID and how the reserver found out about the Coop.

> A somewhat interesting aside, the Coop actually uses Google Calendars for each room under the hood to manage actual occupancies - meaning we can circumvent the scheduling system (*ScheduleOnce*) and directly modify the underlying GCal, with permission. In fact, we *are* doing this. I will detail this shortly.

Anyways, Bobst doesn't require that info. But Bobst *does* requires the user to be logged in to view or reserve rooms whereas the Coop doesn't. 

To implement this stuff the service can return the fields required + descriptions when the location endpoint is hit and the client can dynamically create the appropriate form fields (or Alexa prompts) in turn. To reduce the number of questions the user is asked everytime they reserve I'm considering a *post-first-login* questionnaire that asks certain common questions that remain static between reservations (email address, full name, etc). This questionnaire is reduced by grabbing info from our auth server (**Shibboleth**) which provides a lot of good stuff like year standing, school (within NYU), diploma (postgrad vs. grad vs. undergrad) etc. It may even make said questionnaire irrelevant, but this I will find out once I can see the questions asked and the details offered by Shibboleth. 

On the topic of the Calendar based scheduling systems, my boss at STIT tells me there are apparently many GCal-based study room reservation systems. Even if this were incorrect, the rooms at the Coop (one GCal per room) suffice as motivation to create a class for GCal based rooms. From my experience with the Coop's GCal (which I was graciously allowed Read access to for research) I found that only occupations were listed, in 1 hour multiples. That means I have to create *virtual* roomTimes starting from the opening hours till closing for each day starting from "now" to 2 weeks on from then. This necessitates a VirtualRoomTime child class from the RoomTime with builtin methods for handling this emulation logic.

### Database

Our schemas revolve around the models detailed above. The exception being that we're not interested in past or existing reservations as data but rather only for metrics. We do keep individual user's reservations under their entries for the User views. 

Our Location classes will contain information about their corresponding DB entries but we can assume the default corresponding entry PrimaryKey will be the shortcode.

As I started writing the DB interface I was being overly enthused about dependency injections for unit tests so I created a few layers of classes to abstract the DB completely, in case we decide to change our db of choice later. Why would we do such a thing and how likely is it? I don't know, and probably not at all. But I already went thru all the trouble so I'll take care of the superfluous code later. Famous last words?  

### Authentication

NYU offers OIDC and SAML for auth but I need stateless session so OIDC it is. Also as an apparent benefit, OIDC doesn't require MFA - at least for the time being - so I can do automated endpoint testing.