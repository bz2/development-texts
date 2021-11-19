# Working with upstreams

Visual Meaning uses free software libraries authored by a wide range on contributors, as a core component of the development of our platform. This is in line with the [Be open and use open source] guidance from government, and wider principles of using free software for the common good.

How much interaction we have or need with these 'upstream' projects and communities will vary, here we spell out how we should go about being responsible participants.


## Normal practice

### Adding an upstream dependency

New libraries can be added as a project dependency through the normal process of code review. When proposing a change like this, it it worth checking the exact licenses used, as rough guide see the [GNU license commentary] and the [Blue Oak license list]. Note that non-free, AGPL, and 'lead' licenses likely mean we cannot redistribute in the manner we need.

Projects that have an acceptable license but are abandoned or poorly maintained can be considered but it is worth looking for alternatives.

Code authored by others (for instance, found in forum posts) without a clearly marked license must not be included in any repository we distribute.

### Working with an upstream project

Issues and bugs we discover as part of our product testing should be reported to the upstream project at the point we can identify an upstream cause. Where there is an existing report, it may be useful to add a comment with further details if we have any.

Fixes and new features should be proposed to upstream projects where possible. It's acceptable to land a workaround in our codebase while working on a better change or waiting for review. Making a temporary fork of a dependency to address an issue may sometimes be necessary, but must not be left to languish.


## Friday afternoon hacking

The preferred way of providing contribution and support to the libraries we depend on is by improving those libraries as part of our general development efforts. That may be part of planned work - in which case we should also make sure we're not blocked on upstream acceptance.

We have Friday afternoons reserved as no-meetings time. Martin has previously used this time to work on ideas outside the current sprint scope, and on upstream changes. This provides a space where a slower pace of review and landing doesn't pose a problem.


## Sponsoring and contracting

The company has some budget reserved for upstream projects that we use. We can spend that by contracting a specific person to work on an area or codebase in general, or sponsoring the project as a whole.

There are organisational challenges for many organisations, even quite well established ones, in effectively using donations. For example, see the [experience of Babel funding]. So, while [github sponsorship] or the [open collective] provide good low-barrier paths to providing funds, we should prefer paying specific developers where that is an option.

We could do an analysis of all our our dependencies and try to allocate money via some formula, but many of these are large and well supported already. Instead, let's look out for smaller projects where we can make a more meaningful difference. Anyone should be able to propose using a chunk of the budget, with sign off by the design authority.

### Template for sponsorship proposal

* Which project
* How we use it
* Link to sponsorship page
* What impact we expect from sponsoring


## Endnote

This is a living document and should be updated to reflect any changes to our process.


[Be open and use open source]: https://www.gov.uk/guidance/be-open-and-use-open-source
[GNU license commentary]: https://www.gnu.org/licenses/license-list.html
[Blue Oak license list]: https://blueoakcouncil.org/list
[github sponsorship]: https://github.com/sponsors
[open collective]: https://opencollective.com/

[What is an open source upstream?]: https://www.redhat.com/en/blog/what-open-source-upstream
[The CADT Model]: https://www.jwz.org/doc/cadt.html
[experience of Babel funding]: https://news.ycombinator.com/item?id=27116357
