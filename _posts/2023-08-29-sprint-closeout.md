---
title: "Azure DevOps Guidance: Sprint Close-out"
date: 2023-08-29
layout: single
categories:
  - DevOps
tags:
  - Agile
  - Process
---

## Azure DevOps Guidance: Sprint Close-out

At the end of each sprint are some basic tasks that each of us need to do to close out a sprint.  By end of day on the last day of the sprint, each team member should have closed out all work assigned to themselves in the sprint taskboard.

## For completed user stories

1. Closed all tasks that have been completed.
    1. Completed tasks should be marked as Closed and in the Closed column of the board.
    2. Remaining hours should be set to 0.
    3. Completed hours should be set to the actual hours of work completed.
1. Closed out all bugs that have been completed.
    1. Completed bugs should be marked as Closed and in the Closed column of the board.
    2. Remaining hours should be set to 0.
    3. Completed hours should be set to the actual hours of work completed.
1. Closed out all user stories that have been completed.
    1. Completed user stories should marked as Closed.

## For user stories in a New or Approved state

User stories that were never committed to in a sprint should also not have any active or closed child work items.  Move the parent story to the backlog by dragging the story to the backlog, which will automatically reassign all child items to the backlog.  Simply changing the story iteration path will not affect child work items.  Dragging the story to the backlog tile performs an automation on the story and all child items.

![Drag Story to Backlog Tile](/assets/images/UserStory.MoveToBacklogViaTile.png)

Ideally, however, there should never be uncommitted user stories on the taskboard after the first week of the sprint.  If a story cannot be committed to by its owner by the end of week one of a two-week sprint, then it is very unlikely that the owner has sufficient confidence in the ability to complete the story by the end of week two.  Therefore, by the end of week one of a two-week sprint, the owner of an uncommitted user story should move it to the backlog and add commentary in the discussion section of the story explaining why for team transparency and contextual understanding.  Without an explanation, historical knowledge can quickly be lost and the product owner will not know why approved work is not able to be completed.

## For user stories in a Committed state with no active or completed tasks or bugs

Once a user story has been committed to in a sprint, it should be tagged for the sprint in which in was originally committed, such as `Orig:SprintN`.  This allows the scrum master to trace and report on user story spill-over for internal Microsoft LED compliance reporting.

## For active user stories with remaining Active and/or New tasks or bugs:

1. The State of any "Active" tasks that have not yet been worked on should be reset to "New."
1. Tasks that have been worked on and hours decremented from Remaining:
    1. Using the context menu "..." for the task, choose the "Create copy of work item..." option to create a duplicate.
    1. Duplicate task: keep the same user story parent as the original.
    1. Duplicate task: Zero out the Completed hours.
    1. Duplicate task: Change the Original Estimate hours to match the Remaining hours. Save and Close.
    1. Original task: Zero out the Remaining hours and set to Closed.
1. Using the context menu "..." for the user story, select the Split! option to assist in copying key details of the user story into a new user story assigned to the next sprint iteration.
    1. By default, the Split! function will include all New or Active tasks and ignore all closed tasks.
    1. Following the above process, you should only have tasks on the board that are either New or Closed, which allows you to keep all tasks automatically selected for reassignment to the newly split user story.
    1. New user story is automatically assigned to the next sprint, but you can change that and reassign to the backlog when the story opens up in ADO.
