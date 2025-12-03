---
title: "Azure DevOps Guidance: Definition Of Done"
date: 2023-08-29
layout: single
categories:
  - DevOps
tags:
  - Agile
  - Process
---

## Azure DevOps Guidance: Definition Of Done

## What is it?

The definition of done (DoD) is a formal description of quality standards, specifically, the quality required for work to become part of the Increment. It ensures members of the Scrum Team have a shared understanding of what it means for work to be complete.

The definition of done makes transparent the team’s shared understanding of what needs to happen for any piece of work to be completed to a useable standard. Think of it as a checklist that defines what’s needed for an Increment to be releasable.

A definition of done is also relative to each team and what they are delivering, so it should be negotiated at the beginning of each project with the development team to decide what quality standards are important to uphold.

Some possible items to include in the checklist:

- Private Endpoints (PE) implemented end-to-end for the data path implemented.
- Credentials stored securely in AKV accessible only through PEs.
- Spark Notebooks code reviewed
- Data pipelines peer reviewed
- Data pipelines include deduplication logic
- Data pipelines implement incremental load logic
- Data contracts (schemas) stored in database
- User stories represent releasable, deployable code or solutions
- Solution increment is checked into source control (e.g. ADO, GitHub).
- Solution increment is tested with realistic data

## How to document

For each project, a Wiki page should be created documenting the negotiated definition of done to which the team has committed.  This is a living document open to updates by the team throughout the engagement as necessary.

## Evolving the definition of done

It's very difficult to comprehensively identify everything that should be included in the definition up front.  We allow for the definition to evolve over time as we discover things.  For example: 

- In a product backlog refinement session, the team might notice that a particular acceptance criterion is almost universal and so they add it to the definition of done.
- In sprint planning, the team might notice some vital work that isn’t currently captured in the definition of done.
- In the daily scrum, the team might surface impediments that could be avoided by a stronger definition of done.
- In sprint review, the team might discover missed stakeholder expectations and decide to add stakeholder sign-off or user acceptance testing (UAT) to the definition of done.
- In a sprint retrospective, the team might identify some ways a stronger definition of done might have prevented the introduction of bugs during the sprint.

## Defining the definition of done

This is an exercise that the entire team should participate in.  Start by articulating the question the team will answer.  For example, what do we need to do as a team to deliver the data strategy accelerator and drive value for our customer?  Stages in this exercise:

- Brainstorming
- Defining categories, for example:
  - Done with user story
  - Done with sprint
  - Solution increment deployable
  - Solution increment can be merged into next branch of source control
- Sort and consolidate ideas from brainstorming into buckets and categorize
- Publish definitions of done by category
