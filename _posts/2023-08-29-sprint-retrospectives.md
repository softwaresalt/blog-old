---
title: "Azure DevOps Guidance: Sprint Retrospectives"
date: 2023-08-29
layout: single
categories:
  - DevOps
tags:
  - Agile
  - Process
---

## Azure DevOps Guidance: Sprint Retrospectives

## Current Team Practice

### Retrospective Board Setup

This retrospective guidance uses the following ADO Retrospective board settings:

- Title: [project] Team: Sprint [N]
- Max Votes per User: 5
- [x] Include Team Assessment
- [x] Obscure the feedback of others until after Collect phase
- [x] Display 'Retrospective Prime Directive'
- [x] Do not display names in feedback

Column Settings Template: Drop-Add-Keep-Improve

### Retrospective Meeting Prep

If the planning session is every other Wednesday on a two-week sprint cycle (recommended), then the sprint review will be the day prior (Tuesday).  Following the sprint review is a good time to do the retrospective as the review and demo are out of the way and the team can now focus on process.  To get the most out of the retrospective meeting itself, it's best to have the team fill out the Team Assessment and add feedback cards to the board well in advance.  A good rule of thumb for this is to set the expectation on the Friday prior to the sprint review that everyone should have filled out the retrospective board by mid-day of the following Monday.

With the Team Assessment and board feedback in hand, the scrum master is now in a good position to group and summarize the feedback for the team in preparation for the retrospective meeting the next day.

### Retrospective Summary Write-up

First, the scrum master will want to group the feedback for the team in the board in meaningful buckets for them to vote on during the retrospective.  This is one of the expected outcomes of the meeting: to get to an action item.

The scrum master will also want to write up a summary of the feedback from the team.  A good way to do this is to include a section of the Azure DevOps Wiki for retrospective summaries.  Each page is dedicated to a single sprint summary.  The Wiki pages allow you to reference ADO work items, which you can go back and edit in based on how the team votes.

### Retrospective Meeting Process

The summary is an aid to the discussion in the retrospective meeting that precedes the vote.  With everyone discussing the issues and potentially correcting any misunderstandings in the summary, the team comes to a common understanding and votes on the issue most important to act on for improvement by the team.

If the team has provided their input early and the scrum master comes to this meeting fully prepared, the meeting should go pretty quickly.

### Retrospective Follow-up

Once the vote is done, the scrum master will create a user story in the backlog describing the action(s) to take and assigning to the appropriate individual for implementation, or perhaps to the scrum master for monitoring if the issue involves the whole team.  This story should be assigned to the next sprint.

## Reference

### Overall: Iteration Retrospectives (1)

Retrospectives are effective tools for helping teams learn from experience, but after a project, it’s too late (for that project). Agile teams deliver frequently, but often can’t afford to do a full project retrospective after each delivery.

Therefore: Hold short retrospectives at the end of each iteration.

They typically take 15-60 minutes; when this is first done it will tend to take on the longer side. Invite the whole development team. [But – there may be power issues related to management, so be careful about these.]

Note that iteration retrospectives don’t preclude project retrospectives.

Related Patterns: Section II. Safety discusses ways to make people comfortable sharing their concerns. Section III. Language considers a couple speech patterns you may want to be aware of. Section IV. Structure looks at different ways to organize the retrospective session. Finally, section V. Outcomes considers how the team takes the results of a retrospective and applies it back into the project.

### Safety: Safe Space (2)

Most people have thoughts and concerns they want the whole team to know, but people won’t share their inmost thoughts unless they believe it’s safe to do so. Iteration retrospectives don’t have enough time to slowly build rapport and safety.

Therefore: Let repetition and familiarity build safety over time. Use safer (more anonymous) mechanisms at first and move to more open ones as people become comfortable.

Related Patterns: Bag Check(3) provides a means of getting over uncertainty about the benefits. Anonymous Responses(4) provides a way to let people say hard truths; Safety Blanket(6) provides another way. You can move to a more Open Format(5) as people trust each other more.

### Safety: Bag Check (3)

In extremely sensitive situations, people may have concerns that they’re not willing to ignore. But if they don’t set them aside and cooperate, the team can’t do its job at all. People can’t wait a week or two to resolve this.

Therefore: Each day, let people have a brief, structured chance to express their concerns, and temporarily set them aside.

Example: Some teams use a "bag check" or "parking lot" protocol, where they explicitly identify concerns at the start of the day, but then set them aside to work. At the end of the day, there’s another meeting where people can "reclaim their bag" (if it’s still a concern) or "abandon their bag" (if the concern has dissipated). This lets the team acknowledge their concerns, but still work.

Example: Scrum teams have a daily meeting where people tell what they did yesterday, what they intend to do today, and what’s in their way. (XP teams often use a similar "stand-up" meeting.)

Example: The Core Protocols (Software for Your Head) have explicit protocols for Check In and Check Out, so people can tell the team when they are engaged or not.

### Safety: Anonymous Responses (4)

Saying nothing feels safest, but deprives the team of a chance to learn and improve.  It’s hard to be the first to speak. It’s hard to understand other people’s perspectives.

Therefore: Use anonymous or semi-anonymous techniques to make it safe to communicate things it might not feel safe to say out loud.

Example: A technique for anonymous responses would be to have each person write a topic on a card, then have the facilitator shuffle the cards, read them, write the topic on a chart, and destroy the cards.

Example: A technique for semi-anonymous responses would be to have each person write a topic on a sticky note, then walk to the chart and place the note where they think it belongs. Someone determined to break the anonymity could watch for handwriting, or see who put what where, but it mostly lets people act "as if" they don’t know who wrote what.

Related Patterns: Expect to be able to move to a more Open Format(5) as people build trust over time.

### Safety: Open Format (5)

Over time, the group becomes more comfortable, and people feel they can safely say what they want and what they see, but the meeting structure has excessive concern for safety. The team wants to review more quickly, but the safeguards slow them down.

Therefore: Evolve to a more open format. Retain a way for someone to request a safer format when they think the team needs it.

Example: Instead of placing sticky notes, let people call out topics to a facilitator.

Example: Let others on the team facilitate the review.

### Language: Safety Blanket (6)

If an issue is sensitive enough, saying, "I’m concerned about this issue" (even anonymously) can feel risky.

Therefore: Wrap the concern in a "safety blanket": instead of "What concerns do you have?," ask "What concerns do you think people on your team have?" or even, "What concerns do you think people on a similar project might have?"

Note: As people learn to trust each other, this pattern can fade away.

Related Patterns: Safe Space (2).

### Language: Reframing (7)

People omit subjects and objects in their sentences, making hidden assumptions. People ignore their own power and wait for others to do things. People mistake wishes for needs.

Therefore: Recognize when this is a problem, deconstruct what is said, and re-frame it into a statement that is active and under control of the speaker.

Example: "Management ought to provide snacks." Is this because the team wants snacks, or wants a demonstration that management cares? If it’s the former, people could start bringing snacks in and try to create a trend.

Example: "Somebody ought to make sure it works before QA gets it." This is much more powerful, when turned into an explicit request, "Developers, for each story would you add an explicit task to double-check that it works before marking the story done?"

### Structure: Backward and Forward Look (8)

Some things have gone well, others poorly. Some problems are temporary, others last longer. Talk alone doesn’t change things.

Therefore: Use a framework that looks both backward (to what happened) and forward (to what we intend to do in the future).

Example: The SAMOLE framework asks people to suggest things that the team should keep doing the SAme, things the team should do MOre of, and things the team should do LEss of.

Example: The PMI framework (deBono) asks people to consider what is Plus, Minus, or Interesting.

Example: The WW/NI framework asks people to consider what Works Well and what Needs Improvement. (This may be augmented with explicit "Resolutions" for how to act in the future.)

Example: The WW/DD framework asks people what Worked Well and what they would like to Do Differently. (This is sometimes known as the Plus-Delta framework.)

Example: Appreciative Inquiry approaches focus on peak experiences and how to recreate them, rather than on what’s gone poorly.

### Structure: Deeper Dig (9)

Just because an idea is on the table doesn’t mean everybody agrees with it. People may have other things to say. Other ideas or problems may be more important.

Therefore: Mine the data for areas of agreement and areas of conflict. Compare this retrospective to previous ones.

Example: You’d like to know how important a concern is. In anonymous forms, you may see the same topic appearing multiple times (perhaps with similar words). In an open form, you can vote or multi-vote on what’s important.

Example: Sometimes the same topic may appear in multiple categories, representing a conflict. One person may think the team is refactoring too much, another that it’s refactoring too little. You may take this information and try to surface where the rest of the group is.

Example: Sometimes the same topic may appear in multiple categories, without being contradictory. Someone may write that refactoring worked well this week, and the same person suggest that refactoring needs improvement.

Example: By looking at the previous retrospective, you may see that an  issue recurs. This may help a team learn that its interventions aren’t working in this area.

### Structure: Fish in Water (10)

Some problems are like water to a fish: so much part of the environment that they’re hard to notice. Other problems are noticed, but not named.

Therefore: Actively seek to find what the team is not seeing or not saying. You may need to create extra safety to make it safe to discuss.

Example: People sometimes talk about the "elephant in the room": a problem so big it can’t be ignored, but yet it’s never mentioned. For example, hurtful behavior by a manager could be very hard to discuss.

Example: People may get used to something and forget about the possibility it could be changed. For example, the room is always the wrong temperature, or this tool always crashes.

### Structure: Facilitation Toolbox (11)

An unexpectedly sensitive issue arises, or something comes up that doesn’t fit the team’s usual framework.

Therefore: Maintain a set of facilitation techniques, and use them when needed.

Example: Break into small discussion groups

Example: Use a Structured Sharing technique (Thiagi – www.thiagi.com).

Example: Use multi-voting, prioritization, polls, etc.

Related Patterns: If trust has shifted, you may need to focus again on creating a Safe Space(2).

### Structure: Change in Pace (12)

Using a different format each week adds novelty, but makes it hard to develop a rhythm. A standard format can make it easier to do retrospectives week after week, but it can get boring after a while.

Therefore: Periodically (e.g., every two to three months) try a different retrospective style. This could be a one-time event, or a change for retrospectives going forward. 

Example: Change the standard format from SAMOLE to WW/DD.

Example: Play a retrospective game (such as one based on a classification card game).

Example: Try one of the exercises in Norm Kerth’s book (Project Retrospectives).

### Outcomes: Tentative Rules (13)

People need agreement on how to work together, but people resent being told what to do.

Therefore: Let people explicitly set the rules under which they’ll work together, and provide feedback mechanisms so they can adjust them when necessary.

Keep rules fluid. The stance is "trying on a shirt" to see how the shirt fits and feels, without committing to buying it first.

Example: Many agreements require consensus. People must agree to live with the rule, perhaps for a fixed time. (Even someone who completely disagrees with a strategy may be willing to give it a fair chance, so others can come to disagree with it for themselves.)

## Outcomes: SMART Goals (14)

People suggest protocols that are too fuzzy to assess, or not well enough understood to act as guides. People state ideals as if they were rules.

Therefore: Use the SMART acronym to guide the creation of effective rules:

    S – Specific
    M – Measurable
    A – Achievable
    R – Relevant
    T – Time-Boxed

Example: "Refactor better." It’s hard to argue with, but it doesn’t actually tell anybody what to do.  The team can break this up into concrete actions, such as, "Pick a smell each week, and spend an hour each Wednesday trying to find examples of it," or "When you check off a task, write Yes or No beside it to indicate if you looked for refactoring opportunities before checking in."

Related Patterns: If your goal isn’t met, you may try a Smaller Bite(15).

### Outcomes: Smaller Bites (15)

The team wanted to try something new, but they didn’t get around to it or it was too hard. This is especially a concern if an item has been on the team’s list for two or more iterations without success.

Therefore: Try something similar, but easier.

Example: "Automate at least one customer-specified test for each story" was the goal, but the team didn’t do that. They might agree to "Automate a customer-specified test for the next story we start."

This pattern helps a team converge "What we say we’ll do" with "What we really do." Once smaller bites have been mastered, the team can move back up to the bigger goals.

--------------------------------------------

## Retrospective Questions

### Four Key Questions

- What did we do well, that if we don’t discuss we might forget?
- What did we learn?
- What should we do differently next time?
- What still puzzles us?

### Valuable Retrospective Questions

- What helps you to be successful as a team?
- How did you do this sprint?
- Where and when did it go wrong in this sprint?
- What do you expect, from who?
- Which tools or techniques proved to be useful? Which not?
- What is your biggest impediment?
- If you could change 1 thing, what would it be?
- What caused the problems that you had in this sprint?
- What’s keeping you awake at night?
- Which things went smoothly in this sprint? Which didn’t?

### Asking why?

- Why did you do it like this?
- Why did this (or didn’t this) work for you?
- Why do you consider something to be important?
- Why do you feel this way?
- Why did you decide to work together on this?

### Questions for a remote retrospective

- What do you like about our team and the way that we work together?
- What can we do to improve collaboration, communication and co-working in the team?
- How do you feel about the tools that we are using?
- Do the tools support collaboration sufficiently?
- What have you learned working in this dispersed team?
- If there is one thing that you could change, what would it be?

--------------------------------------------

## Simple Retrospective Framework

1. **Set the stage:** What is the purpose of the retrospective? What is the scope of exploration?
1. **Gather data:** Collect facts and key data.
1. **Generate insights:** Interpret data, root cause analysis,; identify solutions or improvements.
1. **Decide what to do:** Prioritize which actions to take.
1. **Close the retrospective:** Summarize and review plan of action.

## Safety Check

What's your personal sense of safety on a scale of 1-5?  
1 = I'll smile and go along, not comfortable openly sharing my opinion.  
5 = I am ok talking about anything.

## Four Questions

- What did we do well that if we don't discuss we might forget?
- What did we learn?
- What should we do differently next time?
- What still puzzles us?

## Four Response Types

- I like how we...
- I wish that we would have...
- What if we...?
- I wonder if we could...?

## Scale 10 & Solution Focused Questions

- If everything we working great, what would that look like?
- On a scale of 1-10, where are we now?
- What are we doing well now that positively contributes to our score?
- Using the resources we have, what can we do to move one step closer to 10?

## Voting Guidance

- Vote for what we need to talk about, not what we want to talk about.
- Encourage people to vote based on relevance to target outcomes/goals, not based on what they happen to feel at the moment.

## Retrospective Checklist

1. What is the purpose of your retrospective?
1. What kind of outcome are you looking for?
1. Who will be invited to the retrospective?
1. Do you anticipate any problems in terms of personal safety and willingness or ability to contribute?
1. What kind of retrospective process will you use?
    1. How will you set the stage?
    1. How will you gather data?
    1. How will you generate insights?
    1. How will you decide what to do?
    1. How will you close the retrospective?
