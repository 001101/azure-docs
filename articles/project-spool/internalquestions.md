*Note: This is a temporary page.  **This will not be published.***

## Internal Questions

- {mick} **Would you prefer to keep meta-documentation here, or in our OneNote?**
- {mick} Our Contributor's Guide recommends starting out with 10-15 use-cases.  **What are those use-cases?**  Some ideas:
  - Send and receive MMS/SMS text messages.
  - Make and receive voice calls to phone numbers through PSTNs.
  - Make and receive voice calls to phone numbers through VOIP.
  - Make and receive voice calls to any device without a phone number.
  - Build real-time chat into your application.
  - Let your users participate in group [calls, text messages, and web chats].
  - Broadcast messages to a group of devices.
  - Add web video chat into your app.
  - Add web video chat (across NATs / firewalls) into your app.
  - Send push notifications to Android and iOS devices.
  - Send media files to devices.
  - Let your users share their screens.
  - Route your business phone number to a bot
  - Eventually – message directly to Twitter, Whatsapp, Facebook, and others.

- {mick} As I prioritize our use-cases and quickstarts, I'm wondering about our launch strategy.  **Who is our "early adopter" and what scenarios would appeal to them?**
- {mick} I'm tempted to apply some of the lessons I've learned from my entrepreneurial endeavors as we craft our content.  One of those lessons is to deliver a crisp, minimal painkiller - something that alleviates so much pain with so much clarity that our users would be *very* disappointed if our solution didn't exist.  This is related to some of the things I've learned about product-market fit - specifically, [the 40% rule](https://uxplanet.org/understanding-product-market-fit-from-start-to-finish-596a4653814).   **What Spool capabilities do our users *have* to have?  Should I focus on building out docs to support those?**
- {mick} When I built my home security app, I primarily depended upon [this resource](https://webrtc.github.io/samples/) to learn WebRTC.  This gave me "building block" samples that I could combine without depending too much on documentation.  **As we build our docs, should we build a corresponding demo app like this that utilizes a prepaid Nexus to show off the use-cases in a public web app**?  I'm envisioning something very similar to the WebRTC samples app - a minimal, interactive, usable, sharable, plug-and-playable collection of building blocks that link back to the docs.  If so... **what demos should we include?**  Some ideas:
  - Call another device for nearly* free (via WebRTC / STUN)
  - Call your phone
  - Send an SMS message
  - Send an MMS message
  - Build group chat for nearly* free
  - Build a puppy/baby monitor for nearly* free (WebRTC / STUN)
  - Connect a driver to a passenger for nearly* free
  - Build a mini call center for nearly* free
  - **{asterisk}** It seems that WebRTC + SignalR empower highly technical devs to do this, right now.  But there’s a high technical barrier to entry that our SDKs might reduce.  Is our business model focused on empowering less experienced devs with our SDKs, and then monetizing *after* we’ve empowered?  Or are we directly targeting the monetizable NAT traversal / telephony needs from the outset?  

- {mick} **Should I be concerned with capturing feedback from alpha/beta users?**  If so, how do I access them, and what protocol should I follow?
- {mick} Should I see myself as carrying some marketing and evangelism responsibility?  If so...
  - I'm thinking that I should try to align our content with the stories that would excite our users the most.  **Which of our stories elicit the most love from our vocal evangelists?**
  - In my experience, early adopters tend to skew young and liberal.  **Should I focus my quickstarts and samples on builder-friendly scenarios** (e.g. a STUN puppy monitor instead of a TURN Skype clone)?
- {mick} **Is it possible to utilize Spool's clientside SDK as a way to "get started with WebRTC", without using the serverside SDK?**  Question context: one of my biggest pain-points with WebRTC was facilitating the ICE/STUN handshake protocol in JS with multiple webcams feeding streams and alerts to multiple monitoring devices.  I'm wondering if Spool would help me simplify my STUN-based implementation, and then later if I want to traverse NATs, I can hook into the paid TURN offering + serverside goodies.
- {mick}  **Do I (or other spool users) need to be aware of IC3, its gateway, or anything beyond the gateway?**.  Question context: I'm usually developing my projects under an extremely limited time and money budget, so the simpler and more intuitive things are, the more excited I get.
- {mick} **Will our product ever expose “SignalR” terminology or concepts to the developer**, or are we tucking that away behind a layer of abstraction?  
   - **If we are tucking it away, will the developer be able to configure the signaling transport protocol**?  Question context: When working with SignalR on a budget, I’ve often run into limits with WebSockets at the lower-level app service tiers.  These limits were not always easy to diagnose.  I usually start with “long-polling” and then later, once my product has been validated and I need to scale, I'd optimize towards modern protocols and unit-test those upgrades in isolation.
- {mick} - RE "Nexus" - I see that this term isn’t set in stone, and that you’re looking for suggestions.  SignalR utilizes “Hub” to describe the class through which all I/O travels within an application, and uses “Group” to cluster recipients within Hubs.  As a developer, I strongly prefer to utilize terms and constructs that are already within my lexicon.  My natural inclination here would be to refer to “nexuses” as “hubs”.  Though if we expose SignalR constructs to devs, then we'd need to disambiguate.... **Thoughts?**
- {mick} - **Is [this the guide](https://msazure.visualstudio.com/One/_git/COSINE-DEP-Spool?path=%2Fdoc%2Fguide%2Fgetting_started.md&_a=preview) that I should use to study Spool code from the perspective of a "new spool developer"**?
