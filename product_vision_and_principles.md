# Product design principles

## Principle 1: Reflect VM's purpose and strategy in the design

- The purpose of the Shared Meaning Platform is to support the purpose of Visual Meaning (creating shared meaning to help clients unlock systemic change)
- VM's strategy is to deliver primarily through long term Shared Meaning Partnerships with large public (or quasi-public) sector organisations, which account for the vast majority of our revenue.
- In these partnerships we can assume that there will be two basic client user groups: 
	1. "Stakeholders" = regular folks involved in the organisational system or affected by it (employees, contractors, etc). These may be basic or advanced level users
	2. "Change agents" = people actively trying to make the organisational system (or ecosystem) work better.
- Our theory of change is basically as follows:
	1. We provide **stakeholders** with a tool that helps them use maps and linked data to do everyday things much more easily compared to the corporate tools they're used to
	2. The more they use the tool, the more shared meaning we create across organisational boundaries, because the structure of the data and the visual language of the maps all follow a consistent ontology, and the data and maps stay close to reality through the activity of a Shared Meaning Network
	3. We help **change agents** take advantage of this shared meaning by designing custom interactions on the SMP to support systems interventions (e.g. telling stories, asking questions, visualising patterns ...)
	4. As stakeholders become more engaged with the platform, they become "change agents" themselves, using more of the change agent tools to support interventions of their own, thus creating a virtuous cycle in which more shared meaning is generated across larger ecoysystems, unlocking the potential for more significant systemic changes
- We will continue to run a smaller proportion of projects outside of these long term partnerships, but they will still be based on the same theory of change, i.e. generating shared meaning from consistent base maps, on which interactions can be designed that are tailored to the intervention need 

The distinction between "core platform features" for stakeholders and "customisable tools" for change agents then informs the next principle.

## Principle 2: Separate core from extensible functionality

- Core platform features:
	- should be at least as simple and intuitive to use for non-experts as Google Maps
	- should be optimised, stable, and not change very much.
	- have two primary use cases:
		1. to help new folks understand how the system works and where they fit into it
		2. to help everyday folks find organisational (as opposed to operational) information that would otherwise be hard to track down because it lives on a bunch of different corporate systems (e.g. Who owns this process? When does the contract for this IT system expire? What does this acronym stand for? Who else does this activity? etc.)
	- should cover the following interactions and nothing else: search, map switching, map exploration, item selection, link navigation
	- should also include an "advanced" mode for expert users who want to perform more advanced filters / queries directly on the model. This should include list mode, graph mode (?), filter tools, advanced search. How this integrates with the basic experience is an ongoing design challenge; whatever choices we make must not jeopardise the simplicity of the experience for new users.
- Customisable tools:
	- should be based on a set of simple unified design principles / system, applied to an extensible set of discrete, configurable interaction "behaviours"
	- have two primary use cases:
		1. to allowing change agents to build "tasks" for users to perform in support of change intiatives
		2. to configure custom states for specific user groups (e.g. a view with particular pre-set filters to be used as a decision support tool) This includes default states that could be applied to larger audiences e.g. having certain filter toggles always on
	- should be developed through user-centred design based on the design principles / system 

## Principle 3: Promote autonomy down the user chain

- The business model is based on the assumption that the use of the tool will develop organically over time, so we achieve our purpose best when we make it easy/desirable for people to move from one "level" of user to the next.
- We can think of users in a "chain": from devs, to VM consultants, to "change agents", to "stakeholders". When we develop features, we should develop them in a way that maximises autonomy for the next user on in the chain. 
- This requires us to have a much better idea of the expected skill/capability/process maturity level for that user group.
...


## Questions

- Where should we draw the dividing line between features that are "core platform" vs "customisable"? e.g. should filter toggles be core?
	- Are there better names for these?
- How do we link basic and advanced "modes" in the core platform experience? Where do data filters go? How easily should users be able to switch between the two?

etc.
