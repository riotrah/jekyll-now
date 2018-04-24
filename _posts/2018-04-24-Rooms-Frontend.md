---
layout: post
title: On Rooms' Frontend Architecture
published: true
---

I discussed the motivations behind PWAs earlier. They're just awesome. I don't think the app as of yet necessitates a data store and we can probably get away with just passing everything as props but perhaps after a discussion with our designer that may change. As I've taken over backend duties our frontend engineer can take care of the executive decisions here.

One thing I haven't designed yet is a feedback page and amenities vote section. I want a way for users to vote on amenities (from a list that we create) present in a given room, perhaps after the reservation date is passed we can offer a feedback modal during the user's next reservation success or as a feedback button for each room in the "past reservations" review. 

Alexa Skills are essentially lambda functions so if I design my backend right it shouldn't be hard to create a Skill making use of the existing ones.

But while the above is true, Google DialogFlow now outputs Alexa intents, rendering some of my motivation to use Lambda/Serverless even more moot. But hey Dialogflow *does* allow using Lambdas to handle invocations so all is not lost?