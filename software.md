---
layout: default
title: Software Development
---

<section class="home-panel panel-development-process">
<div class="container">
<p class="lead">Software Development</p>

<p>Watford Consulting can help you build your next Bespoke Software Product.</p>

<p>There are several components considered critical for successful software product delivery and
Watford Consulting can guide you every step of the way. By getting
these components in place early you can be confident that changes made to your software will be
accountable, buildable and testable - unleashing the return on your investment as soon as possible</p>

<p>Building great software is our passion. Read on for an overview of how we get there.</p>

<p class="lead">Passion and Professional Pride</p>
<p>You need a software development team who are passionate about what they do, who get a kick out of producing elegant
solutions for difficult problems, who are proud of what they produce. Of course we think that describes Watford 
Consulting perfectly!</p>

<p>A few traits to look for in your team: <ul>
<li>They are enthusiastic about getting software built to a professional standard. They want to stand by their software,
not throw it over the wall so that it is someone else's problem. Software is a matter of professional pride.</li>
<li>They are continuously learning, engaging in the community, attending conferences, applying best practices.</li>
<li>They want their software released to users. They crave feedback on the software effectiveness. They use that
feed back to inform further development</li>
<li>They are pragmatic, recognising that the commercial concerns of the business must be satisfied. It is no use
building purist software if there is no one left to use it.</li>
<li>They go out of their way to understand the problem fully, learn the terminology of the problem space. The more
we know the luckier we get! The more we can understand about the problem space, the more the solution space grows
and the greater the chances of finding an elegant solution.</li>
</ul></p>

<p>Your team will guide you through development and help you ensure the other components you need are in place.</p>

<p class="lead">Software Version Control Systems</p>
<p>Get all sources for your software products into a version control system on day one. This paves the way for multiple
developers to work on the code base at the same time, and for your build systems to continuously detect changes
and immediately test and rebuild your products.</p>

<p>Depending on your workflow you might favour a system that allows easy branching, such as the highly regarded
Git version control system. Git is well supported by software development tools.</p>

<p>Network Repositories for version control systems such as Bitbucket Server (formally called Stash) are ideal for
hosting on-premises. If you prefer something externally hosted look at the offerings from GitHub and Bitbucket.</p>

<p class="lead">Build Automation</p>
<p>Once you have you Version Control System you can concentrate on a Continuous Build and Integration tool. These tools
will detect every change commited against your code to trigger rebuilding and testing of your software products.</p>

<p>Getting this your build running fast is important to give developers fast feedback and to help the team notice when
some changes have broken the build. Better still, if your workflow involves developers working on bugfix or feature 
branches, have the build automation system build those branches too. This should keep your main branch clear
of any build failures.</p>

<p>Release builds should always come from your build automation system, never from the developers own environment. This
keeps all builds consistent and means you won't suffer issues when it turns out the developer's environment was 
inconsistent with the build environment.</p>

<p>For Java development, build scripting with Ant and Ivy is a mature choice when you need a lot of build customisation. 
If your build is a bit more mainstream then Maven or any of its derivative build systems will work well.</p>

<p>Once the build scripts are in place you'll want a system to run the build when changes are detected int he version
control system. Jenkins is a very popular automation server with integrations for many tools such as code style checking,
static analysis tools and build artefact repositories.</p>

<p>Once your builds have run you will need to store the resulting artefacts using a repository manager such as Artifactory
ready for testing and deployment. Repository managers are also ideal for mirroring libraries normally downloaded from
the internet during build.</p>


<p class="lead">Design Implementation</p>

<p>Coding is where the developer's heart is! This is where the abstract ideas are turned into something concrete - a very satisfying process.</p>

<p>With the build infrastructure in place it is a good idea to start by building a 'Walking Skeleton' where just enough of 
the software architecture is implemented to see how the software might hang together. External dependencies, such as databases
or network authentication services, are stubbed out meaning the software can be built, deployed and tested against a very
basic acceptance criteria.</p>

<p>Once the skeleton is in place start adding software meat to those bones! Use agile approaches to build slices of functionality
through the system at a time. The aim here is to produce a focused piece of functionality that would provide some value
to users right away.</p>

<p>In Agile environments we need to move quickly to implement features. Paying attention to Design Patterns and Best Practices 
helps produce software that will be more easily understood by others, aiding on-boarding of new developers and easing any future maintenance.</p>

<p>Software systems can be complex, but we can tackle the complexity by breaking systems down into small components which are
easier to reason about an implement. Since these components become the foundation of the larger system it is imporant to 
ensure they are well defined and well tested. Developers can build automated tests that are run as part of the build process
to exercise these smaller components in Unit Tests.</p>


<p class="lead">Code Review</p>

<p>Those automated tests are only as good as the code they are written in. A supportive code review is a great way to keep the code base
adhering to best practice. Use code reviews as the gate keeper to accepting code changes into your main branch.<p>


<p class="lead">Testing</p>

<p>Testing goes further than the developers' automated testing. A variety of system and integration classes of test are needed to
ensure that the emerging complexity built from all those simple parts we worked hard to build does what is needed.</p>

<p>Watford Consulting can supplement your test teams, devising the functional and non-functional tests which exercise your products, ensuring
the requirements have been met.</p>
