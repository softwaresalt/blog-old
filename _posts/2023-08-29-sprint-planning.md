---
title: "Azure DevOps Guidance: Sprint Planning"
date: 2023-08-29
layout: single
categories:
  - DevOps
tags:
  - Agile
  - Process
---

## Azure DevOps Guidance: Sprint Planning

## Planning

Planning work is notoriously difficult to do well.  The less well understood the work, the less accurate the estimates will be. But some techniques work universally. Just as with breaking big problems into smaller problems to make them easier to solve, we break big chunks of work into small pieces and then estimate that effort; it's easier to estimate duration for smaller pieces of work than for larger ones.

### Sprint Goal

Sprint goals provide a definition of done for the sprint and help teams finish the things to which they've committed.  The sprint goal should be expressed in a way that makes it easier to define what is more valuable when you have to make a choice, which clarifies priorities and allows decentralizing decision making to speed things up.

Don't over-complicate a sprint goal. Pick a straightforward goal and stick with it until the end of the next sprint.

### The Backlog

For sprint planning purposes, you should not concern yourself too much with Epics and Features.  For Data Strategy engagements, the epics and features should be derived from the use case scenarios driven out of the workshops.  The user stories are the individual blocks from which we will build our customer's solution.

In the backlog view, create user stories to capture each building block of work, which should never exceed a week's worth of effort (30 hours).  In the taskboard view's capacity section, your daily hours will never exceed 6, so for a typical week, 30 hours will be your total weekly hours.

For the purposes of planning, you should capture ALL forms of work required, including scheduling and participating in meetings, excluding Scrum ceremonies such as sprint planning, daily scrum, sprint review, sprint retrospective.  

Ideally, we would have user stories planned out multiple sprints, but this isn't always possible.  Do always try to capture work that needs to be done regardless of when you will be able to do it; that's what the backlog is for.

Try to put your user stories in stack rank order based on the order of importance in which that work needs to be done, such as dependencies.  As a group, we can reorder later based on group priorities.  To stack rank your user story, either drag the item into position or open the context menu for the item by selecting the elipsis next to the title and select "Move to position..."

When creating the user story, be sure to set the Story Size using the [story point guidelines](./2023-08-29-story-points.md).  This is your range-based, approximate sizing for this area of work.

![Backlog Planning](/assets/images/UserStory.Backlog.Planning.png)

After creating a user story, be sure to map it to a parent feature.  We always want to have a tree of work items from task all the way up the chain to the Objective level.  This way, we can always trace our work back to a core objective as well as see aggregate progress from each level in the tree from the backlog view.  Note: the main dashboard includes indicators for orphaned tasks, user stories, and features so that we can quickly identify work items that are unmapped.

![Backlog Mapping](/assets/images/UserStory.Backlog.Mapping.png)

### Tips for virtual sprint planning

- Be really prepared - communicate plans clearly ahead of time, so that everyone has clear expectations.
- Use a video conferencing tool that allows for breakout sessions
- Set up the interactive online resources you plan to use and include links in the meeting request.
- Online discussions don’t start as naturally as they would in person, so share discussion topics ahead of time, and consider preparing some ice-breakers.
- Ensure that you’ve accounted for time differences for teams that span time zones.
- Tech issues arise no matter how much advanced planning and testing you do. Always expect the unexpected.

## What does a good user story look like?

A user story should be the smallest composable unit of **outcome** that you can define for a given persona, which might be you.  Starting with the **persona**, define the **action** needed to effect an **outcome** that adds real value to the customer by helping them achieve their goals in the description field.

### Assigned To

Where possible, try to assign the user story to the individual to whom the child tasks are or will be assigned.  If multiple people will be responsible for child tasks, decompose the story into individually deliverable outcomes separately assigned.  This helps keep the stories small and separates the areas of concern for delivereable outcomes.  This also helps maintain a view of aggregate story points attributed to individuals.

### Iteration First Committed

After you assign a user story to a sprint, it is approved by the product owner, and as you commit to delivering the user story in the assigned sprint, you must copy the current iteration text into the Iteration First Committed field.  This enables the scrum master to track user stories that get pushed out from their original sprint for internal LED reporting and tracking.

### Owned By

This field allows a dev lead, for example, to assign themselves as responsible for the outcome of the story while assigning the work to another developer.  The main dashboard includes a report item enabling the user to track work they _own_ in addition to the work _assigned to_ them.

### Tagging

Use the tagging feature to label the story with relevant attributes you may want to filter on later in queries and reports or for easy identification on boards and backlogs.

### Complexity

The relative complexity of the user story should be given a "T-Shirt" size of Low, Medium, or High.  The value of assigning a complexity level is that it helps with estimation.  For example, highly complex work areas will not only take longer to implement, but will also likely introduce additional risk, especially where the details of implementation aren't well understood or are unknown.  For the purposes of planning and estimation, try to break highly complex user stories down into user stories that do not exceed medium complexity unless you cannot produce a useable outcome from any one of a decomposed set of less complex stories.

Complexity can be relative to the person designating its level.  What may be complex to one person may not be to another.  One way of thinking about this is that complexity indicates how well the owner understands the task or problem. Problems with unknown elements to them represent risk that can introduce larger differentials between the original estimate and the actual hours.  The better the problem is understood, the closer the original estimate will be to the actual work time.  This is why it is always better as a standard engineering practice to break work into smaller units and work towards a better understanding of the problem through each of those smaller work units.

### Sizing / Points

Sizing a story allows you to estimate a relative range of effort before you break it down into tasks.  After some time, you should be able to estimate how many story points you can deliver in a sprint.  As a rule of thumb, you should never have stories that exceed a week's worth of effort.  Anything over 30 hours should be decomposed into smaller units of useable outcome.

Story Size | ~effort days (>=d - <d) | ~effort hours (>=h - <h)
| - | - | -
0 | |
1 | 0 - 0.5d | 0 - 3h
2 | 0.5 - 1d | 3 - 6h
3 | 1 - 2d | 6 - 12h
5 | 2 - 3d | 12 - 18h
8 | 3 - 4d | 18 - 24h (decompose)
13 | 4 - 5d | 24 - 30h (decompose)

#### Blocked

With the current style configuration on user stories, the card for stories marked Blocked=Yes will show red in the taskboard view to assist in visually identifying blocked stories during scrum calls.  In addition, the dashboard report includes a tile to help identify blocked stories in the current sprint.

![User Story Example](/assets/images/UserStory.Example.png)

#### Description

User stories should always address one or more personas, although preferably just one in order to adequately address the specific needs of that persona.
Note the format of the description in the user story screen shot. Given the persona you are addressing for this story, an action needs to be taken to achieve a desired outcome or goal for the persona addressed.
The standard format is: As [persona X], I would [verb | action | ability Y] so that I can achieve [outcome Z].

#### Acceptance Criteria

Every user story should define for the product owner and their stakeholders the terms/conditions for determining if the outcome of the user story is **usable.**  It doesn't matter how much work you may have done, if it isn't useable by the persona addressed, then it doesn't meet the criteria for acceptance by the product owner.  It could be that a story represents a portion of or a step in a sequence that achieves the desired outcome, as long as this is adequately described and is composed such that it can be tested for completeness.

#### Implementation Details

Filling out the implementation details is optional, but can be helpful in providing implementation guidance and direction for tasking out the details of the work.

## Timelines

The majority of observed national holidays land on either the beginning or end of the week. Individual's also frequently leverage weekends to create extended, contiguous days off.  To get the most attendance out of the most important sprint ceremonies, such as planning and reviews & demos, the sprints will be scheduled to run every two weeks, starting on Wednesdays and ending on Tuesdays.

### Sprint Planning

Typically, sprint planning should take up to 2 hours per week according to scrum.org, but we find that people have a hard time genuinely focusing for that long, nor is everyone productive during that time. Our teams have also been operating remotely for years, making the impact of in-person planning infeasable. To adapt and try to make everyone as productive as possible, we are scheduling the sprint planning sessions for no more than 90 minutes.  However, to achieve our planning objectives within 90 minutes, pre-work must be done.

Throughout the week prior to planning, each team member should coordinate with the scrum master and product owner on the backlog stories scheduled for the following sprints with an emphasis on finalizing the upcoming sprint backlog.  Initially, those stories can be placeholders, but must be sized and leveled for complexity. Refinement can take place during the planning session.

**IMPORTANT:** To simplify planning during the backlog assignment process, limit the size of each user story to no more than 5, which constitutes up to 3 days of work.  Smaller workloads are easier to estimate, leading to more accurate estimates.  Stories larger than 5 should be broken down into more detail.  Smaller stories are also easier to complete within a sprint and present lower risk to delivering committed work.  Using this system, the cumulative story points per person should not exceed 20.

### Story Refinement

During the planning session, refer to the guide for what makes a good story to properly flesh the stories out.

### Tasking

Throughout the week prior to planning, you should try to stub out tasks for each story assigned to the upcoming sprint and provide a preliminary estimate in hours.  Try to limit task hours to no more than 6.  If the hours estimate for a given task exceeds 6 hours, then you have probably not broken down the work into sufficiently small pieces for individual delivery.  As stated earlier, there is value in decomposing work into smaller units: they are easier to understand, to estimate, and to deliver.  You should make a disciplined practice of decomposing work into small units of deliver.

## State Changes

User story states indicate where the story is in a workflow progression.

- **New:**  The story has only been created and added to the backlog.  It has not been approved yet for inclusion in a sprint.
- **Approved:** The story has been approved by the product owner or proxy to be prioritized for work in a given sprint.
- **Committed:** The owner of the story has committed to completing the user story by the end of the sprint.
- **Active:** The story is actively being worked on in the current sprint.

## Backlog Refinement

Product Backlog refinement is the act of adding detail, estimates, and order to items in the Product Backlog. This is an ongoing process in which the Product Owner and the Development Team collaborate on the details of Product Backlog items. During Product Backlog refinement, items are reviewed and revised. The Scrum Team decides how and when refinement is done.
