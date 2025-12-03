---
title: "Azure DevOps Guidance: Sprint Daily Process"
date: 2023-08-29
layout: single
categories:
  - DevOps
tags:
  - Agile
  - Process
---

## Azure DevOps Guidance: Sprint Daily Process

We use the Azure DevOps Agile toolset to [plan our work](./2023-08-29-sprint-planning.md) for future sprints, to allow product owners and stakeholders to prioritize the work that we will do, and to communicate the work that we are currently doing.

Each day marks hours of capacity for each team member to achieve outcomes committed to for the current sprint.

To effectively communicate what we are doing in a sprint to stakeholders, product owners, and to also hold ourselves accountable in daily stand-ups (DSUs), we must update our work in the Kanban board view on a daily basis.

## What does this look like?

### User Story Approval Process

In the normal course of sprint planning, user stories should be approved by the product owner. This does not mean that they will be implemented in the sprint, just that of all the work in the product backlog, this is sufficiently important to the business to prioritize into the current sprint.

Once approved, the person to whom the work is assigned may commit to completing this user story within the duration of the current sprint.

The product owner should approve stories during sprint planning.

### Committing to a User Story

Once you _mark_ a user story as committed, you are committing to completing this user story within the current sprint.  The value of a commitment is that the business is able to plan ahead based on what it expects to be delivered.  You should be cautious to avoid "hope creep."  It's always better to deliver on your promises than to overpromise and underdeliver.

You should decide to which stories you are committing in the current sprint within two days of sprint planning.  Stories that you don't commit to should be returned to the backlog for future sprint planning.

NOTE: There are times when an approved story can be pulled back into the sprint if you are ahead of schedule or when a committed story is blocked unexpectedly, leaving you capacity.

There is a rule applied to user stories in this template that enforces the Iteration First Committed field to be populated once the story transitions to or beyond the committed state.  To populate this field, just copy the value in the Iteration field and paste into the Iteration First Committed field.  This field is used to help the scrum master track slippage of stories across sprints.  Some projects get assigned an internal resource to review agile practices and implementation for LED standards and quality tracking.

### Activating a User Story

Once you pull a child task from the _New_ state into the _Active_ state, you must also transition the parent user story to the _Active_ state.

### Closing a User Story

Once you have closed all of a user story's tasks, you must also transition the parent story to the _Closed_ state.
Closing the parent story will also collapse the story and its tasks on the board to streamline the user experience by only showing non-closed stories.

### Updating Hours Worked

As you work tasks in a sprint, you should decrement the _Remaining_ hours on the task and increment the _Completed_ hours.  Being disciplined about doing this is super helpful because it causes the standard burndown chart for the sprint to accurately communicate to the business how we are progressing throughout the sprint relative to the expected burndown line.  The completed hours helps us run metrics later to see how close our estimates are to our actuals.  There is no penalty for being off on this metric; the purpose is to help improve estimates on an individual basis.  Some individuals are very pessimistic in their estimates, while others are overly optimistic.  Some individuals regularly overdeliver on low complexity work items.  Metrics on this can help us determine on an individual basis where we can bring individuals' estimates more in line with how they actually perform over time.

### Work Discussion

The discussion section of a user story is a valuable component of the agile process.  We should use this to ask questions, log activities, provide updates, link to other stories, and tag individual team members for awareness.
A disciplined Agile practitioner will regularly log information into this section of a user story as an asynchronous way of maintaining regular updates and information about sprint activities.  If you are not able to attend a daily stand-up, logging your work here will effectively do the job.
Also, product owners and stakeholders who groom the backlog and monitor active sprints will find regular updates to user stories super valuable: they won't have to go searching for answers from people in real-time.  It's better if you are transparent and provide as much information as possible up front.

<!--## Bugs-->

--------------

- NOTE: By the Monday following sprint planning (every other Wednesday), all user stories assigned to a team member should be either committed to or reverted to the backlog.
- NOTE: The discussion section of the user story should be the primary repository of historical and contextual knowledge and transparency around the story and all of its child work items in order to maintain continuity of knowledge across the team and extending to the product owner and beyond.

Additionally, a new field exists in user stories in the Planning section labeled "Iteration First Committed."  This field will become a required field when the user story is transitioned from Approved to Committed.  At this
