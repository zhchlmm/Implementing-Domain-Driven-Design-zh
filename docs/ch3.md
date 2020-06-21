# 第 3 章 上下文映射图

> Chapter 3. Context Maps

Whatever course you decide upon, there is always someone to tell you that you are wrong. There are always difficulties arising which tempt you to believe that your critics are right. To map out a course of action and follow it to an end requires courage.

—Ralph Waldo Emerson

The Context Map of a project can be expressed in two ways. The easier way is to draw a simple diagram that shows the mappings between two or more existing Bounded Contexts (2). Understand, however, that you are just drawing a simple diagram of what already exists. The drawing illustrates how the actual software Bounded Contexts in the solution space are related to one another through integration. This means that the more detailed way to express Context Maps is as the source code implementations of the integrations. We’ll look at both ways in this chapter, but for most of the implementation details see Integrating Bounded Contexts (13).

At a high level, keep in mind that this chapter focuses on the solution space assessment, whereas the previous chapter dealt quite a bit with the problem space assessment.

::: tip
Road Map to This Chapter

- Learn why drawing a Context Map is essential for the success of your project.
- See how easy it can be to draw a meaningful Context Map.
- Consider the common organizational and system relationships and how they affect your projects.
- Learn from the SaaSOvation teams as they produce Maps to get control of their projects.
  :::

## 3.1 WHY CONTEXT MAPS ARE SO ESSENTIAL 上下文映射图为什么重要

When you start out on a DDD effort, first draw a visual Context Map of your current project situation. Produce a Context Map of the current Bounded Contexts involved in your project and the integration relationships between them. Figure 3.1 shows an abstract Context Map. We’ll be filling in the details as we progress.

<Figures figure="3-1">A Context Map of an abstract Domain. Three Bounded Contexts and their relationships are drawn. The U stands for Upstream and D stands for Downstream.</Figures>

This simple drawing is your team’s Map. Other project teams can refer to it, but they should also create their own Maps if they are implementing DDD. Your Map is drawn primarily to give your team the solution space perspective it needs to succeed. Other teams may not be using DDD and/or they may not care about your perspective.

::: tip
Oh, No! There’s New Terminology!

We are introducing Big Ball of Mud, Customer-Supplier, and Conformist here. Be patient; these and other DDD team and integration relationships noted here are discussed in detail later in this chapter.
:::

For example, when you are integrating Bounded Contexts in a large enterprise, you may need to interface with a Big Ball of Mud. The team maintaining the muddy monolith may not care what direction your project takes as long as you adhere to their API. So, they aren’t going to gain any insight from your Map or what you do with their API. Still, your Map needs to reflect the kind of relationship you have with them, because it will give your team needed insight and indicate areas where inter-team communication is imperative. Having that understanding can do much to help your team succeed.

::: tip
Communications Facility

Besides giving you an inventory of systems you must interact with, a Context Map serves as a catalyst for inter-team communication.
:::

Imagine what would happen if your team assumes that the team maintaining the muddy monolith will provide new APIs that you are depending on, but they don’t intend to provide them, or they don’t even know what you are thinking. Your team is counting on a Customer-Supplier relationship with the mud. The legacy team, however, by providing only what they currently have, forces your team into an unexpected Conformist relationship. Depending on how late in the project you got the bad news, this unseen yet actual relationship could delay your delivery or even cause your project’s failure. By drawing a Context Map early, you will be forced to think carefully about your relationships with all other projects you depend on.

Identify each model in play on the project and define its BOUNDED CONTEXT. . . . Name each BOUNDED CONTEXT, and make the names part of the UBIQUITOUS LANGUAGE. Describe the points of contact between the models, outlining explicit translation for any communication and highlighting any sharing. [Evans, p. 345]

::: tip
![](./figures/ch3/team_meeting-1.jpg)

When the CollabOvation team first started developing its greenfield model, they should have used a Context Map. Even though they were nearly starting from scratch, stating their assumptions about the project in the form of a Map would have prompted them to think about separate Bounded Contexts. They still could have listed significant modeling elements on a whiteboard, and then gathered them into groups of related linguistic terms. That would have forced recognition of linguistic boundaries and resulted in a simple Context Map. However, they actually didn’t understand strategic modeling in the least. They first needed to attain a strategic modeling breakthrough. Later on they did make the crucial discovery of this project-saving tool, applying it to their eventual benefit. When the subsequent Core Domain project got under way, it again paid off substantially.
:::

Let’s see how you can quickly produce a useful Context Map.

### 3.1.1 Drawing Context Maps 绘制上下文映射图

A Context Map captures the existing terrain. First, you should map the present, not the imagined future. If the landscape will change as your current project progresses, you can update the Map at that time. First focus on the current situation so you can form an understanding of where you are and determine where to go next.

Creating a graphical Context Map need not be complicated. Your first option is always hand-drawn diagrams where whiteboards and dry-erase markers rule. The style used here is easily adapted as shown by [Brandolini]. If you decide to use a tool to capture the drawing, be sure to keep it informal.

Referring back to Figure 3.1, the Bounded Context names are just placeholders, as are the integration relationships. They would all be actual names in a tangible Map. The upstream and downstream relationships are shown, the meanings of which are explained later in the chapter.

Whiteboard Time

Draw a simple diagram of your current project situation that communicates at a high level where the boundaries are, the relationships between them and their teams, what kinds of integrations are involved, and the necessary translations between them.

Remember that software implements what’s in the drawing. If you need more information about what you should draw, consider the systems that your Bounded Context integrates with.

Sometimes we’ll want to zoom in and add more detail to a given part of a Context Map. It’s just a different perspective on the same Context(s). Besides boundaries, relationships, and translations, we may want to include other items such as Modules (9), significant Aggregates (10), perhaps how teams are allocated, and any other information relevant to the Contexts. These techniques are demonstrated later in the chapter.

All of the drawings and any prose can be placed into a single reference document if it has value to the team. With any such effort we should avoid ceremony and remain both simple and agile. The more ceremony you add, the fewer people will want to use the Map. Putting too much detail in diagrams won’t really help the team. Open communication is the key. As conversations unveil strategic insight, add it to the Context Map.

::: tip
No, It’s Not Enterprisy

A Context Map is not an Enterprise Architecture or system topology diagram.
:::

A Context Map is not an Enterprise Architecture or system topology diagram. The information is conveyed relative to interacting models and DDD organizational patterns. Still, Context Maps may be used in high-level architectural investigations, providing views of the enterprise not otherwise available. They may highlight architectural deficiencies such as integration bottlenecks. Because they exhibit an organizational dynamic, Context Maps may even help us identify sticky governance issues that could block progress, and other team and management challenges that are more difficult to uncover using other methods.

Cowboy Logic

AJ: “The missus said, ‘I was out in the pasture with the cows; didn’t you notice me?’ I said, ‘Nope.’ She didn’t talk to me for a week.”

![](./figures/ch3/cowboy_logic_aj_talks.jpg)

The diagrams deserve to be posted prominently on a wall in a team area. If the team frequents a wiki, the diagrams might also be uploaded there. If a wiki will be largely ignored, don’t bother. It’s been said that a wiki can be a place where information goes to die. No matter where they are displayed, Context Maps will be hidden in plain sight unless the team pays regular attention to them through meaningful discussion.

### 3.1.2 Projects and Organizational Relationships 产品和组织映射关系

To briefly reiterate, SaaSOvation is on a path to develop and refine three products:

1. A social collaboration suite product, CollabOvation, enables registered users to publish content of business value using popular Web-based tools such as forums, shared calendars, blogs, wikis, and the like. This is the SaaSOvation flagship product and was the company’s first Core Domain (2) (although the team didn’t know the DDD terminology at the time). It is the Context from which IdOvation’s (point 2) model was eventually extracted. CollabOvation now uses IdOvation as a Generic Subdomain (2). CollabOvation will itself be consumed as a Supporting Subdomain (2), being an optional add-on to ProjectOvation (point 3).
2. A reusable identity and access management model, IdOvation provides secure role-based access management for registered users. These features were first combined with CollabOvation (point 1), but that implementation was limited and not reusable. SaaSOvation has refactored CollabOvation, introducing a new, clean Bounded Context. A key product feature is the support of multiple tenants, which is vital to an SaaS application. IdOvation serves as a Generic Subdomain to its consuming models.
3. An agile project management product, ProjectOvation, is at this point in time the new Core Domain. Users of this SaaS product can create project management assets, as well as analysis and design artifacts, and track progress using a Scrum-based execution framework. As with CollabOvation, ProjectOvation uses IdOvation as a Generic Subdomain. One of the innovative features adds team collaboration (point 1) to agile project management, enabling discussions around Scrum products, releases, sprints, and individual backlog items.

::: tip
Finally, the Definitions!

The organizational and integration patterns mentioned previously are defined . . .
:::

What are the relationships between these Bounded Contexts and their individual project teams? There are several DDD organizational and integration patterns, one of which commonly exists between any two Bounded Contexts. Each of the following definitions is largely quoted from [Evans, Ref]:

- Partnership: When teams in two Contexts will succeed or fail together, a cooperative relationship needs to emerge. The teams institute a process for coordinated planning of development and joint management of integration. The teams must cooperate on the evolution of their interfaces to accommodate the development needs of both systems. Interdependent features should be scheduled so that they are completed for the same release.
- Shared Kernel: Sharing part of the model and associated code forms a very intimate interdependency, which can leverage design work or undermine it. Designate with an explicit boundary some subset of the domain model that the teams agree to share. Keep the kernel small. This explicit shared stuff has special status and shouldn’t be changed without consultation with the other team. Define a continuous integration process that will keep the kernel model tight and align the Ubiquitous Language (1) of the teams.
- Customer-Supplier Development: When two teams are in an upstream-downstream relationship, where the upstream team may succeed interdependently of the fate of the downstream team, the needs of the downstream team come to be addressed in a variety of ways with a wide range of consequences. Downstream priorities factor into upstream planning. Negotiate and budget tasks for downstream requirements so that everyone understands the commitment and schedule.
- Conformist: When two development teams have an upstream/downstream relationship in which the upstream team has no motivation to provide for the downstream team’s needs, the downstream team is helpless. Altruism may motivate upstream developers to make promises, but they are unlikely to be fulfilled. The downstream team eliminates the complexity of translation between bounded contexts by slavishly adhering to the model of the upstream team.
- Anticorruption Layer: Translation layers can be simple, even elegant, when bridging well-designed Bounded Contexts with cooperative teams. But when control or communication is not adequate to pull off a shared kernel, partner, or customer-supplier relationship, translation becomes more complex. The translation layer takes on a more defensive tone. As a downstream client, create an isolating layer to provide your system with functionality of the upstream system in terms of your own domain model. This layer talks to the other system through its existing interface, requiring little or no modification to the other system. Internally, the layer translates in one or both directions as necessary between the two models.
- Open Host Service: Define a protocol that gives access to your subsystem as a set of services. Open the protocol so that all who need to integrate with you can use it. Enhance and expand the protocol to handle new integration requirements, except when a single team has idiosyncratic needs. Then, use a one-off translator to augment the protocol for that special case so that the shared protocol can stay simple and coherent.
- Published Language: The translation between the models of two Bounded Contexts requires a common language. Use a well-documented shared language that can express the necessary domain information as a common medium of communication, translating as necessary into and out of that language. Published Language is often combined with Open Host Service.
- Separate Ways: We must be ruthless when it comes to defining requirements. If two sets of functionality have no significant relationship, they can be completely cut loose from each other. Integration is always expensive, and sometimes the benefit is small. Declare a bounded context to have no connection to the others at all, enabling developers to find simple, specialized solutions within this small scope.
- Big Ball of Mud: As we survey existing systems, we find that, in fact, there are parts of systems, often large ones, where models are mixed and boundaries are inconsistent. Draw a boundary around the entire mess and designate it a Big Ball of Mud. Do not try to apply sophisticated modeling within this Context. Be alert to the tendency for such systems to sprawl into other Contexts.

By integrating with the Identity and Access Context, both the Collaboration Context and the Agile Project Management Context avoid going their Separate Ways with respect to security and permissions. True, Separate Ways may be applied Context-wide for a specific system, but it can also be employed on a case-by-case basis. For example, one team could refuse to use a centralized security system but may still choose to integrate with some other corporate standard facilities.

The teams will cooperate with Customer-Supplier roles. There’s no way that SaaSOvation’s management will allow one team to force others to be Conformists. It’s not that a Conformist relationship is always negative. Rather, Customer-Supplier requires commitment on the part of the Supplier to provide support for the Customer, which fosters the kind of inter-team relationships SaaSOvation thinks it needs to achieve complete success. Of course, Customers aren’t always right, and so some give-and-take must exist. Overall it is the positive organizational relationship that the teams need to maintain.

The teams’ integrations will make use of Open Host Service and Published Language. Perhaps surprisingly they will also employ Anticorruption Layer. This is not a contradiction, even though they are establishing open standards between their Bounded Contexts. They can still realize the benefits of isolated translation by using its fundamental principles in the downstream Contexts, but with less complexity than needed when consuming a Big Ball of Mud. The translation layers will be simple and elegant.

The Context Map drawings that follow use these abbreviations to indicate the patterns employed at each end of a relationship:

- ACL for Anticorruption Layer
- OHS for Open Host Service
- PL for Published Language

As you review the following sample Context Maps and supporting text, it may be helpful to glance back at Chapter 2, “Domains, Subdomains, and Bounded Contexts.” The diagrams of each of the three sample Bounded Contexts are also useful here. Since they remain fairly high-level, those diagrams could be included as part of the Maps for each Context, although they are not repeated here.

### 3.1.3 Mapping the Three Contexts 映射 3 个示例限界上下文

Now let’s jump into the team experience so we can learn from what they did . . .

::: tip
![](./figures/ch3/team_huddle-c3.jpg)

When the CollabOvation team realized the tangle they had created, they dug into [Evans] to help find their way out of it. Among other discoveries of enormous value within the strategic design patterns, they found a practical tool named Context Maps. They also found a helpful article online by [Brandolini] expanding on this technique. Since the tool’s guidance indicated that they should map the existing terrain, that’s the first step they took. Figure 3.2 shows the results.

<Figures figure="3-2">The tangle within the Collaboration Context caused by unwelcome concepts is exposed by this Map. The caution sign points out the area of impurity.</Figures>

The first Map produced by the team highlights their early recognition of the existence of a Bounded Context that they named Collaboration Context. By the odd shape of the existing boundary they appropriately conveyed the likely existence of a second Context, but one without a clean and clear separation from the Core Domain.
:::

A narrow passage near the top allows foreign concepts to migrate back and forth almost without censure, as the caution sign indicates. It’s not that Context boundaries need to be completely impenetrable. As with any boundary, the team wants the Collaboration Context to control with full knowledge what crosses its borders and for what purpose. Otherwise the territory becomes overrun with unknown and possibly unwelcome visitors. In the case of a model, the unwelcome visitors generally cause confusion and bugs. Modelers should be cordial and even welcoming, but under conditions that favor order and harmony. Any foreign concepts entering the boundaries need to demonstrate the right to be there, even taking on characteristics compatible with the territory within.

::: tip
![](./figures/ch3/team_meeting-2.jpg)

This analysis led to a better understanding not only of the current condition of the model, but in what direction the project needed to go. Once the project team realized that concepts such as security, users, and permissions did not belong inside the Collaboration Context, they responded accordingly. The team had to segregate these from the Core Domain and allow them to enter only under agreeable terms.
:::

This is a vital DDD project commitment. The Language of each Bounded Context must be honored in order for all models to remain pure. Linguistic segregation and a strict adherence to it help each team involved in the project to focus on their own Bounded Context and keep their vision correctly focused on their own work.

::: tip
Applying Subdomain analysis, or problem space assessment, led the team to the diagram shown in Figure 3.3. Two Subdomains were carved out of a single Bounded Context. Since it is a good goal to align Subdomains one-to-one with Bounded Contexts, this analysis showed the need to separate the single Bounded Context into two.

<Figures figure="3-3">The team’s Subdomain analysis led to the discovery of two, a Collaboration Core Domain and a Security Generic Subdomain.</Figures>
:::

::: tip
The Subdomain and boundary analysis led to decisions. When human users of CollabOvation interact with the available features, they do so as Participants, Authors, Moderators, and so forth. A variety of other contextual separations are discussed later, but this gives a good idea of the necessary divisions that were created. With that knowledge, the clean and crisp boundaries indicated on the high-level Context Map shown in Figure 3.4 came about. The team used Segregated Core [Evans] to refactor to reach this point of clarity. The recognizable shapes of the boundaries act as icons or visual cues for each Context. Keeping the same relative shapes across diagrams can help with cognition.

<Figures figure="3-4">The original Core Domain is marked with a bold boundary and integration points. Here IdOvation serves as a Generic Subdomain for the downstream CollabOvation.</Figures>
:::

The Context Maps usually don’t appear all at once as the various sketches may lead you to believe, although when finally understood, they are not difficult to produce. Thought and discussion help to refine a Map through rapid iterations. Some of the refinements might come in the way of integration points, which describe the relationships between Contexts.

::: tip
The first two Maps indicate the gains made after applying strategic design. After the original CollabOvation project was well under way, the team had factored out identity and access concerns. As they progressed, they produced the Context Map in Figure 3.4. The team sketched only the Core Domain, Collaboration Context, along with the new Generic Subdomain, Identity and Access Context. They didn’t depict any future models, such as the Agile Project Management Context. It wouldn’t help the team to jump ahead too far. They only needed to correct flaws with what existed. Transformations supporting forthcoming systems would be needed soon enough, and that Map belonged to the future team to produce.
:::

Whiteboard Time

- Thinking of your own Bounded Context, can you identify concepts that don’t belong? If so, draw a new Context Map that shows the desired Contexts and relationships between them.
- Which of the nine DDD organizational and integration relationships would you choose, and why?

::: tip
![](./figures/ch3/team_meeting-1.jpg)

When the next project involving ProjectOvation was starting up, it was time to augment the existing Map with the new Core Domain, the Agile Project Management Context. The results of that mapping are seen in Figure 3.5. It was not premature to capture what was in planning, even though it was not yet in code. The details inside the new Context weren’t fully understood, but that would come with discussion. Applying high-level strategic design at this early stage would help all teams understand where their responsibilities lay. Since the third of the three high-level Maps is just an augmentation of the previous, we’ll be focusing on it. That’s where SaaSOvation is headed. The company has assigned experienced lead developers to the new project. Being the richest of the three Contexts and the current direction, the new Core Domain is where the best developers should be working.

<Figures figure="3-5">The current Core Domain is marked with a bold boundary and integration points. The CollabOvation Supporting Subdomain and IdOvation Generic Subdomain are upstream.</Figures>

Some essential segregations are already well understood. Similar to the Collaboration Context, when users of ProjectOvation create products, plan releases, schedule sprints, and work on the tasks of backlog items, they do so as Product Owners and Team Members. The Identity and Access Context is segregated out of the Core Domain. The same goes for their use of the Collaboration Context. It is now a Supporting Subdomain. Any consumption by the new model will be protected by boundaries and translations into Core Domain concepts.
:::

Consider the finer details of these diagrams. They are not system architecture diagrams. If they were, given that Agile Project Management Context is our new Core Domain, we would expect it to reside at the top or center of the diagram. Here, however, it is at the bottom. This possibly curious characteristic indicates visually that the core model is downstream of the others.

This nuance serves as another visual cue. Upstream models have influences on downstream models, as activities on a river that occur upstream tend to have impacts on populations downstream, whether positive or negative. Consider pollutants dumped into a river by a large city. Those pollutants may have little impact on that city, but downstream cities may face severe consequences. The vertical proximity of models on the diagram helps identify the upstream influences on downstream models. The labels U and D explicitly call this out between each associated model. These labels make vertical positioning of each Context less important, yet it is still visually appealing to employ them.

Cowboy Logic

LB: “When you get yourself a powerful thirst, always drink upstream from the herd.”

![](./figures/ch3/cowboy_logic_lb-talks.jpg)

The Identity and Access Context is furthest upstream. It has an impact on both the Collaboration Context and the Agile Project Management Context. Our Collaboration Context is also upstream to the Agile Project Management Context because the agile model depends on the collaboration model and services. As noted in Bounded Contexts (2), ProjectOvation will operate as autonomously as is practical. Operation must continue largely independent of the availability of surrounding systems. This does not mean that autonomous services can operate entirely independently of upstream models. We must design in ways to drastically limit direct real-time dependencies. Though autonomous, our Agile Project Management Context is still downstream of the others.

Outfitting an application with autonomous services does not mean that databases from upstream Contexts are simply replicated into the dependent Context. Replication would force the local system to take on many undesirable responsibilities. That would require the creation of a Shared Kernel, which doesn’t really achieve autonomy.

On the latest Map, note the connector boxes on the upstream side of each connection. Both of the connectors are labeled OHS/PL, an abbreviation identifying Open Host Service and Published Language. All three downstream connector boxes are labeled ACL, shorthand for Anticorruption Layer. The technical implementations are covered under Integrating Bounded Contexts (13). Briefly, these integration patterns have these technical characteristics:

- Open Host Service: This pattern can be implemented as REST-based resources that client Bounded Contexts interact with. We generally think of Open Host Service as a remote procedure call (RPC) API, but it can be implemented using message exchange.
- Published Language: This can be implemented in a few different ways but is many times done as an XML schema. When expressed with REST-based services, the Published Language is rendered as representations of domain concepts. Representations may include both XML and JSON, for example. It is also possible to render representations as Google Protocol Buffers. If you are publishing Web user interfaces, it might also include HTML representations. One advantage to using REST is that each client can specify its preferred Published Language, and the resources render representations in the requested content type. REST also has the advantage of producing hypermedia representations, which facilitates HATEOAS. Hypermedia makes a Published Language very dynamic and interactive, enabling clients to navigate to sets of linked resources. The Language may be published using standard and/or custom media types. A Published Language is also used in an Event-Driven Architecture (4), where Domain Events (8) are delivered as messages to subscribing interested parties.
- Anticorruption Layer: A Domain Service (7) can be defined in the downstream Context for each type of Anticorruption Layer. You may also put an Anticorruption Layer behind a Repository (12) interface. If using REST, a client Domain Service implementation accesses a remote Open Host Service. Server responses produce representations as a Published Language. The downstream Anticorruption Layer translates representations into domain objects of its local Context. This is where, for example, the Collaboration Context asks the Identity and Access Context for a User-in-Moderator-role resource. It might receive the requested resource as XML or JSON, and then translates to a Moderator, which is a Value Object. The new Moderator instance reflects a concept in terms of the downstream model, not the upstream model.

The chosen patterns are common ones. Constraining the choices helps keep the scope of integration discussed in this book manageable. We’ll see, even among these select few patterns, that there is diversity in how they can be applied.

The question remains: Is that all there is to creating a Context Map? Possibly. The high-level view provides a good amount of knowledge about the project as a whole. Still, we may be curious about what goes on inside the connections and the named relationships on each Context. Curiosity among team members influences us to produce a bit more detail. When we zoom in, the somewhat blurred picture of the three integration patterns becomes clearer.

Let’s take a minor step back in time. Since the Collaboration Context was the first Core Domain, let’s peer inside it. First we introduce the zooming technique with the simpler integrations, then progress to the more advanced ones.

Collaboration Context

Now, back to the experience of the Collaboration team . . .

::: tip
![](./figures/ch3/team_huddle-c2.jpg)

The Collaboration Context was the first model and system—the first Core Domain—and its workings are now well understood. The integrations employed here are easier yet less robust in terms of reliability and autonomy. Creating a zoomed Context Map is done with relative ease.
:::

As a client of the REST-based services published by the Identity and Access Context, the Collaboration Context takes a traditional RPC-like approach to reaching resources. This Context doesn’t permanently record any data from the Identity and Access Context that it can subsequently reference for local reuse. Rather, it reaches out to the remote system to request information every single time it needs it. This Context is obviously highly dependent on remote services, not autonomous. This is a fact that SaaSOvation is willing to live with for now. Integration with a Generic Subdomain was completely unexpected. To meet their demanding delivery schedule the team couldn’t invest time in a more elaborate autonomous design. At the time the up-front ease-of-design perk could not be passed up. After the rollout of ProjectOvation and the experience with autonomy gained there, similar techniques may be employed for CollabOvation.

The boundary objects in the zoomed Map captured in Figure 3.6 request a resource synchronously. When the remote model’s representation is received, the boundary objects grab the content of interest out of the representation and translate it, creating the appropriate Value Object instance. A Translation Map to turn the representation into a Value Object is shown in Figure 3.7. Here a User in the Role of Moderator in the Identity and Access Context is translated as a Moderator Value Object in the Collaboration Context.

<Figures figure="3-6">A zoom in on the Anticorruption Layer and Open Host Service of the integration between the Collaboration Context and the Identity and Access Context</Figures>

<Figures figure="3-7">A logical Translation Map that shows how a representational state (XML in this case) is mapped to a Value Object in the local model.</Figures>

Whiteboard Time

Create a Translation Map of one of the interesting aspects of integration found in your project’s Bounded Context.

What if you find the translations overly complex, requiring a lot of data copying and synchronization, making your translated object look a lot like the one from the other model? Perhaps you are using too much from the foreign Bounded Context, adopting too much from that model, and thus causing confusing conflict in your own model.

Unfortunately, if the synchronous request fails because the remote system is unavailable, the entire local execution must fail. The user will be informed of the problem and asked to try again later.

Systems integrations commonly rely on RPC. At a high level RPC appears to be very much like a regular programming procedure call. Libraries and tools make it attractive and easy to use. Unlike calling a procedure that resides in your own process space, however, a remote call has a higher potential for performance-degrading latency or outright failure. Network and remote system load can delay RPC completion. When the RPC target system is unavailable, a user’s request to your system will not complete successfully.

While REST-based resource usage isn’t really RPC, it still has similar characteristics. Although complete system failure is relatively rare, this is a potentially annoying limitation. The team looks forward to improving on this situation as soon as possible.

Agile Project Management Context
Since the Agile Project Management Context is the new Core Domain, let’s pay particularly close attention to it. Let’s zoom in on it and its connections to other models.

To achieve a greater degree of autonomy than RPC affords, the Agile Project Management Context team will need to carefully constrain its use. Out-of-band, or asynchronous, event processing is therefore strategically favored.

A greater degree of autonomy can be achieved when dependent state is already in place in our local system. Some may think of this as a cache of whole dependent objects, but that’s not usually the case when using DDD. Instead we create local domain objects translated from the foreign model, maintaining only the minimal amount of state needed by the local model. To get the state in the first place we may need to make limited, well-placed RPC calls, or similar requests for REST-based resources. But any necessary synchronization with remote model changes can often best be achieved through message-oriented notifications published by remote systems. The notifications might be sent on a service bus or a message queue, or be published via REST.

::: tip
Think Minimalistic

The synchronized state is the limited, minimal attributes of the remote models that are needed by the local model. It’s not only to limit our need to synchronize data, it’s also a matter of modeling concepts properly.
:::

It pays to limit our use of remote state, even when considering the design of the local modeling elements themselves. We don’t want, for example, a ProductOwner and a TeamMember to in reality reflect a UserOwner and a UserMember because they take on so many characteristics of the remote User object that a hybridization happens unwittingly.

Integration with the Identity and Access Context
Looking at the zoomed Map in Figure 3.8, we see that the resource URIs provide notifications about significant Domain Events that have occurred in the Identity and Access Context. These are made available through the NotificationResource provider, which publishes a RESTful resource. Notification resources are groups of published Domain Events. Every Event ever published is always available for consumption in order of occurrence, but each client is responsible for preventing duplicate consumption.

<Figures figure="3-8">A zoom in on the Anticorruption Layer and Open Host Service of the integration between the Agile Project Management Context and the Identity and Access Context</Figures>

A custom media type indicates that two resources can be requested:

```java
application/vnd.saasovation.idovation+json

//iam/notifications

//iam/notifications/{notificationId}
```

The first resource URI enables clients to get (literally HTTP GET) the current notification log (a fixed set of individual notifications). Per the documented custom media type,

```
application/vnd.saasovation.idovation+json
```

the URI is considered minted and stable because it never changes. No matter what the current notification log consists of, this URI provides it. The current log is a set of the most recent events that have occurred in the Identity and Access model. The second resource URI enables clients to get and navigate a chain of all previous event-based notifications that have been archived. Why do we need a current log and any number of distinct archived notification logs? See Domain Events (8) and Integrating Bounded Contexts (13) for details on how feed-based notifications work.

Actually at this point the ProjectOvation team is not committed to using REST in all cases. For example, they are currently negotiating with the CollabOvation team over whether to use a messaging infrastructure instead. Under consideration is the use of RabbitMQ. Nonetheless, at this time their integrations with the Identity and Access Context will be REST-based.

For now let’s leave most of the technology details out of the picture and consider the role of each of the objects interacting in the zoomed Map. Here’s an explanation of the integration steps visually demonstrated in the sequence diagram found in Figure 3.9:

- MemberService is a Domain Service that is responsible for providing ProductOwner and TeamMember objects to its local model. It is the interface of the basic Anticorruption Layer. Specifically, maintainMembers() is used periodically to check for new notifications from the Identity and Access Context. This method is not invoked by normal clients of the model. When a recurring timer interval fires, the notified component uses the MemberService by invoking method maintainMembers(). Figure 3.9 shows the timer recipient as MemberSynchronizer, which delegates to MemberService.
- The MemberService delegates to IdentityAccessNotificationAdapter, which plays the role of the Adapter between the Domain Service and the remote system’s Open Host Service. The Adapter acts as a client to the remote system. The interaction with the remote Notification-Resource is not shown.
- Once the Adapter has received the response from the remote Open Host Service, it delegates to the MemberTranslator to translate the Published Language media into concepts of the local system. If the local Member instance already exists, the translation updates the existing domain object. This is indicated by the MemberService self-delegation to its internal updateMember(). The Member subclasses are ProductOwner and TeamMember, which reflect the local contextual concepts.

<Figures figure="3-9">A view of the inner workings of the Agile Project Management Context and Identity and Access Anticorruption Layer</Figures>

We should not focus on the technologies or integration products involved. Rather, by cleanly separating Bounded Contexts, we are able to keep each Context pure, while applying data from other Contexts to express concepts in our own.

The diagrams and supporting text exemplify how we might create Context Map documents. It need not be extensive but should provide enough background and explanation to bring a new project member up to speed. However, create a document only if it is helpful to the team.

Integration with the Collaboration Context

Next, let’s consider how the Agile Project Management Context interacts with the Collaboration Context. Here, too, we strive for autonomy, but this raises the bar, posing some interesting challenges to accomplish the goal of system independence.

ProjectOvation has add-on features that are supplied by CollabOvation. Some include project-based forum discussions and shared calendar scheduling. Users won’t directly interact with CollabOvation. ProjectOvation must determine whether the options are available to a given tenant and, if so, on its own facilitate resource creation in CollabOvation.

Consider a section of this Create a Product use case:

Precondition: The collaboration feature is enabled (option was purchased).

1. The user provides Product descriptive information.
2. The user indicates a desire for a team discussion.
3. The user requests that the defined Product be created.
4. The system creates the Product with a Forum and Discussion.

A Forum and a Discussion must be created in the Collaboration Context on behalf of the Product. In contrast, this is unlike the Identity and Access Context where a tenant has already been provisioned and users, groups, and roles have been defined, and notifications about those events are available. In that case the objects are preexisting. In this case the Agile Project Management Context needs objects that don’t exist yet and won’t exist until it requests them. That’s a potential obstacle to autonomy because we depend on the availability of the Collaboration Context in order to create resources remotely. With desired autonomy, this raises an interesting challenge.

::: tip
Why Is Discussion Used in Both Contexts?

This is an interesting situation because it’s one where the name of the concept, Discussion, is the same in both Bounded Contexts, but they are different types, different objects, and thus have different state and different behavior.

In the Collaboration Context a Discussion is an Aggregate and it manages a set of Posts—implicit children that are themselves Aggregates. In the Agile PM Context the Discussion is a Value Object and only holds a reference to the actual Discussion with Posts in the foreign Context. Note, however, that in Chapter 13 when the team implements the integrations, they discover that they should strongly type the different kinds of Discussions in the Agile PM Context.
:::

We need to leverage eventual consistency using Domain Events (8) and an Event-Driven Architecture (4). There’s nothing that says that only remote systems can consume notifications produced by our local system. When a ProductInitiated Domain Event is published by our model, it is handled by our own system. The local handler requests the Forum and Discussion to be created remotely. This could be done via RPC or messaging, depending on what CollabOvation supports. If using RPC and the remote collaboration system were not available at that time, the local handler would simply keep trying on a periodic basis until it finally met with success. If messaging is supported instead of RPC, the local handler would send a message to the collaboration system. In turn, collaboration would respond with its own message when resource creation completes. When the Event handler back in ProjectOvation received this notification, it would update the Product with an identity reference to its newly created discussion.

What happens if the product owner or team members try to use the discussion prior to its existence? Is the unavailable discussion considered a bug in the model? Will it cause the system to exhibit an unreliable condition? Consider the fact that any given subscriber may not have paid to use the collaboration add-on in the first place. That’s a nontechnical reason to design in resource unavailability. Working around eventual consistency is in no way a kludge. It’s just another valid state that should be modeled.

An elegant way to handle all of the possible unavailability scenarios is to make them explicit. Consider this Standard Type implemented as a State [Gamma et al.], as described in Value Objects (6):

```java
public enum DiscussionAvailability {
    ADD_ON_NOT_ENABLED, NOT_REQUESTED, REQUESTED, READY;
}

public final class Discussion implements Serializable {
    private DiscussionAvailability availability;
    private DiscussionDescriptor descriptor;
    ...
}

public class Product extends Entity {
    ...
     private Discussion discussion;
    ...
}
```

Using this design, a Discussion Value Object is protected from misuse because the State defined by DiscussionAvailability protects it. When someone attempts to participate in a discussion about the Product, it can safely hand off its discussion State. If not READY, the participant will be shown one of three messages:

To use team collaboration you need to purchase the add-on option.

The product owner didn’t request the creation of a product discussion.

The discussion setup has not yet completed; check back soon.

If the Discussion availability is READY, we allow full team member participation.

Interestingly, as implied by the first of the unavailable state messages, the possibility exists that the business chooses to make collaboration options selectable even though they have not yet been purchased. Leaving collaboration UI options enabled could be an effective marketing tickler to encourage follow-on purchase. Who better to nag management to purchase an add-on option than those who are daily reminded that they could be using it, but cannot? Clearly, technical benefits are not the only ones realized by the use of the availability State.

At this time the team isn’t certain what its actual integration with collaboration will be. For the sake of Customer-Supplier discussions, they’ve produced the diagram in Figure 3.10. The Agile Project Management Context may use a second Anticorruption Layer to manage integration between itself and the Collaboration Context. It would be like the one it uses for the Identity and Access Context. The diagram shows the primary boundary objects, which are similar to their counterparts used for identity and access management integration. Actually there is not one single CollaborationAdapter. It is just a placeholder for the several needed, but unknown at this time.

<Figures figure="3-10">A zoom in on an Anticorruption Layer and Open Host Service of the possible integration components between Agile Project Management Context and Collaboration Context</Figures>

Shown inside the local Context are DiscussionService and SchedulingService. These represent the Domain Services that could be used to manage discussions and calendar entries in the collaboration system. The actual mechanisms will be determined by Customer-Supplier negotiations between the teams, which are implemented in Integrating Bounded Contexts (13).

The team can understand part of their model now. What happens, for example, when a discussion has been created and the result is communicated to the local Context? The asynchronous component—either RPC client or message handler—tells the Product to attachDiscussion(), passing it a new Discussion Value instance. All local Aggregates with pending remote resource interests will be cared for in this fashion.

This examination has gone into some useful detail on Context Maps. We need to exercise restraint, however, as we can quickly reach the point of diminishing returns. Perhaps we could have included Modules (9), but those have been placed in their own dedicated chapter. Include any relevant, high-level elements that will lead to vital team communication. On the other hand, push back when detail seems ceremonious.

Produce Context Maps that you can post on the wall. You can upload them to a team wiki as long as it’s not just the project’s attic where nobody ever goes. Keep discussions about the project flowing back to your Map to stimulate useful refinements.

![](./figures/ch3/own_it.jpg)

## 3.2 WRAP-UP 本章小结

That was definitely a productive session with Context Mapping.

- We’ve discussed what Context Maps are, what help they provide to your team, and how you can create them with ease.
- You took a detailed look into SaaSOvation’s three Bounded Contexts and their supporting Context Maps.
- Using mapping, you zoomed in on the integrations between each of the Contexts.
- You examined the boundary objects supporting Anticorruption Layer and their interactions.
- You saw how to produce a Translation Map showing the local mapping between REST-based resources and the corresponding object in the consuming domain model.

Not every project will need the level of detail demonstrated here. Others may require more. The trick is to balance the need to understand with practicality and not pile too much detail into this level. Remember that we are likely not going to keep a very detailed graphical Map up-to-date far into the project. We’ll benefit most from what can be posted on a wall, enabling team members to point at them during discussions. If we reject ceremony and embrace simplicity and agility, we’ll produce useful Context Maps that help us move forward rather than bog down the project.
