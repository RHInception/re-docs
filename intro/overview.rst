.. _intro_overview:

Overview
********

This section is a narrative, or story, introducing us to the
individiual roles each component plays in the Release Engine. At the
end of this narrative we'll have learned:

* Overall workflow
* About the key components
* How components communicate


Scenario
========

We work in a software shop and we have been told to decreate our time
to delivery. We took some measurements and realized that even with
`Jenkins <http://jenkins-ci.org/>`_ and some home-brewed systems for
deployment, we're still spending ≥ **20%** of our sprint time on *just
deploying to test environments*. Let's focus on getting back that 20%.

How do we approach this? What functionality must be present in any
kind of system which can automate deployments? Also consider that
we're just one stop in a much larger shop. Given that constraint, it
follows that whatever we build should be accessible to outsiders,
extensible so other teams can build on it to fit their requirements,
as well as have a clear language for describing steps in a release.

* Authentication and authorization
* Storage for deployment playbooks
* Loosey coupled components, so individual installations can scale to
  meet their owners requirements
* Something to do manage all of the actual steps happening
* And, some sort of configurable notification system, so we can get
  updates in real time.

When used together, the Release Engine provides all these things.
