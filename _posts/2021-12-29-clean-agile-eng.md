---
layout: single
title: Clean Agile Summary
date: 2021-12-29 08:50:30.000000000 +09:00
type: post
header:
    teaser: "http://image.yes24.com/goods/95728889/XL"
    image: "http://image.yes24.com/goods/95728889/XL"
categories:
- IT
tags: [IT]
---

## Clean Agile Chapter 1: Introduction to Agile

When was Agile born? In prehistoric times. You set a small goal, measure your progress toward it, then set the next goal and carry it out. Small, measurable steps!

The showdown of pre-agile v.s. waterfall. Waterfall clearly held the upper hand for 30 years. Plan, then execute according to the plan, that's waterfall. Sounds great! But waterfall didn't actually work. What was the problem?

It's because of the nature of software projects. Good, bad, cheap, done. You can pick only three of these. Four is impossible. Agile pursues having all four of these together, in just the right amount that's needed.

How? You manage the project based on data. The team's velocity and the work that remains are the data Agile needs. Why does it have to be this way? Because the essence of a software project is that the deadline is fixed, but the requirements are fluid.

Let's look at how a waterfall project unfolds.
1. Start: An out-of-the-blue project contract and a release date are handed down.
2. Analysis: Let's analyze the requirements.
3. Design: Let's make the analysis clearer.
4. Development: You drift into the fantasy world of analysis and design. Meanwhile, the requirements keep changing.
5. Just before release: Overtime, weekend work, and a failed release are unavoidable.

So how does Agile differ from the waterfall model? A taste:
Agile divides work into small iteration cycles (1-2 week units) and measures work velocity and progress. This is Agile's data. By repeating 4-5 of these cycles, you can know roughly when the project will be finished. "The primary goal of Agile is to eliminate hope" (p31) "Agile is about knowing, as quickly as possible, just how screwed we are" (p31) Using this data, the manager steers the project toward the best possible outcome. Because decisions are made based on data, a rational organization can adjust the project. Even if the deadline can't be changed, you can develop the essential features first and drop the unnecessary ones.

----

## Clean Agile Chapter 2: Why Agile

Why should you choose Agile? Because of professionalism and the reasonable expectations of (customers, colleagues, and stakeholders).

- Professionalism
	- We've entered an age where we can't live without software. It matters. Examples: Toyota (p47), Volkswagen (p48).
	- The disciplines of Agile software (testing, refactoring, feedback, pair programming, simple design, etc.) will make programming an honorable profession.
	
- Reasonable expectations
	- You have to ship software that works well. Agile is the solution.
	- Delayed release? With Agile, the system is technically releasable at the end of every iteration cycle.
	- Customers don't expect development speed to slow down because of a messy legacy codebase.
	- It's resilient to requirement changes.
	- Even an old system doesn't break down. Steady improvement is possible.
	- Knowledge is shared through pair programming.
	- Data-based plan estimation is possible.
  - Study.

- The Agile Bill of Rights declaration
	- Customer
		- Has the right to know the plan.
		- Has the right to know the progress.
		- Has the right to know the requirements.
	- Developer
		- Has the right to know the priorities and requirements clearly.
		- Has the right to produce high-quality work.
		- Has the right to ask for and receive help from colleagues, managers, and customers.
		- Accepts work. It is not assigned.
		
What happens if the Agile Bill of Rights is upheld? Developers become great developers, and customers are satisfied.
Agile is not a methodology. It is a code of software ethics.

----

## Clean Agile Chapter 3: Business Practices

Let's look at the Agile practices that relate to business.

- Planning
	- Break the project into small pieces and estimate them. How small should you break them? Since estimation is inherently uncertain, only as small as necessary.
	- Trivariate analysis
		- Suited for long-term estimation of large projects.
		- Estimate by dividing into best case (5% accuracy), worst case (95%), and typical case (50%).
		- Precision is too low for day-to-day management.
	- Stories and points
		- Story: For each feature, make a very brief story from the user's perspective. Omit the details. Many stories will emerge.
		- Stories are continuously added, deleted, and changed.
		- Iteration cycle 0: Create several stories and estimate the effort points needed for one suitable story. Use this as a baseline to estimate the points for other stories.
		- Iteration cycle 1
			- The developers estimate how many points they can handle during one iteration cycle.
			- Calculate the return on investment (ROI) and select and group the high-value, low-cost stories. As many as the points the developers estimated.
			- Check the mid-cycle progress and adjust the story-handling target.
			- Summary: Now you know the effort points you can handle in one iteration cycle.
		- Iteration: Based on the effort points per cycle you've learned, estimate the points you'll handle in the next cycle. Repeat.
		- Project end: When there are no more stories worth implementing.
		- How should you write a story? INVEST
			- independent
			- negotiable
			- valuable
			- estimable
			- small
			- testable
		- How do you assign points? Every member estimates independently, and you take the average.
		- As iteration cycles repeat, you merge stories, split them (while keeping INVEST), and spike (story + estimating the story).
		- Don't assign stories; let developers choose them themselves.
		- Test writing starts from the beginning of the cycle. A story is complete only when it passes the tests.
		- End the cycle by demoing the story to stakeholders.
		- When the cycle ends, record the velocity graph and the burndown chart. Plan the next cycle based on these.
		- It's best for velocity to be steady.
			- What if velocity goes up? People lie to look like they're all working hard. Generating bad data is, in effect, a failure of the iteration cycle.
			- What if velocity drops? It's likely that code quality has dropped.

- Small releases
	- Release as often as possible. Long release cycles are nothing but an old habit. Now, thanks to git, fast releases are possible.
	- Keep shrinking the release cycle toward zero. This means shrinking the iteration cycle length too.

- Acceptance tests
	- Writing the system's requirements in the form of automated tests, as much as possible.
	- The business unit creates the requirements. But the business unit hates writing tests. So who writes the tests? If developers write them? It's not only complicated, but there's no way for the business unit to verify them.
	- The business unit writes tests describing the behavior of each user story in the proper format -> the developers automate them.
	- Write the acceptance tests before the first half of the iteration cycle ends -> integrate the tests into the continuous build. A story is complete when it passes the acceptance tests.
	- QA isn't brought in at the tail end of the project; it starts at the beginning of the project with the role of writing specifications. Its role is large.
	- Test only the parts that changed. If you test everything, QA dies.
	- QA tries to find as many defects as possible. If you push the deadline back every time a defect is found, developers will create defects on purpose. So have QA only create the tests, and let the developers run them.

- Whole team
	- Coincidence matters. You solve problems and find defects by chance.
	- Have everyone, including the customer, gather and work in one place. Synergy emerges.
	- What if you work remotely? Even so, it's not as good as working together. There have been cases where it worked well when the time zone and culture were the same.

- Conclusion
	- Keep communication between the business unit and the development unit simple and clear to reduce conflict and build trust.

----

## Clean Agile Chapter 4: Team Practices

- This chapter covers the relationships among team members and the relationship between team members and the product the team builds.

- Metaphor
	- For effective communication, you must clearly define vocabulary and terms and use them consistently.
	- Metaphors and analogies help build shared understanding. For developers, QA, managers, customers, users, all of them!
	- Using the same language will keep the project consistently connected.

- Sustainable pace
	- We were proud of working overtime -> we were programmers -> exhausted, we all quit at once.
	- When you work overtime, you make strange mistakes. Making up for mistakes is hard.
	- A software project is a marathon.
	
- Collective ownership
	- The whole team collectively owns the code.
	- What about a complex system? You build expertise while also knowing things broadly.
	- When knowledge spreads across the whole team, communication improves and you can make better decisions.
	- If you play politics, you're doomed.
	
- Continuous integration
	- Continuous integration must be done continuously. That is, merges to the main branch should happen as often as possible.
	- The continuous build must never break. If the build breaks, you have to fix it so it stops breaking as quickly as possible. If the build breaks, the tests will break too, and if you leave it alone, no one will fix it.
	
- Standup meeting
	- Attendance is not mandatory.
	- Hold it according to each team's schedule (it doesn't have to be daily).
	- 10 minutes max.
	- Each person answers 3 questions
		- What did I do since the last meeting?
		- What will I do before the next meeting?
		- What obstacles are there?
	- No need to discuss or explain. It should take 30 seconds per person.
	- If needed, add a fourth question: 'Who do you want to thank?'
	
- Conclusion
	- We looked at how a small team works as a true team. The explanation above should help with the language of communication, how to treat each other, and how to treat the project.

----

## Clean Agile Chapter 5: Technical Practices

This chapter is hard to practice. Even so, it's the core of Agile that can't be left out.

- Test-driven development
	- Be sure to do it.
	- You have to implement twice. Once for the test, and once for the code.
	- TDD principles
		- Test first.
		- Test code is only code meant to fail.
		- Production code is only code meant to pass the test.
	- Test code is the best documentation and the best example.
	- If you come to test code later, it becomes a hassle and you end up not doing it...
	- 90% test coverage is enough.
	- You must not use test coverage as a target or a goal. If you do, developers will only write tests that pass.
	- If you write tests first, well-separated design becomes possible.
	- Only when you have tests do you find the courage to improve the code.
	
- Refactoring
	- A practice of improving the structure of code while not changing its behavior.
	- Development order: first make it work -> then clean up the code.
	- Refactoring should be a routine task. Keep doing it during development.
	- Do large-scale refactoring routinely too. Even if it takes a long time, you just keep doing it.
	
- Simple design
	- Rules
		1. Pass all tests.
		2. Reveal intent.
		3. Eliminate duplication.
		4. Reduce the number of components.
	- Goal: keep the 'design weight' as light as possible.
	- When a design grows complex, the design weight increases. In exchange, you can build complex features. Strike a balance between design complexity and feature complexity.
	
- Pair programming
	- It is not an obligation.
	- Do it in short bursts. About 50% of your work is a good proportion. But it's flexible depending on the situation.
	- There's no set method.
	- The best way to become a team.
	- Many teams replace code review with pair programming.
	- It's fine to do it with several people.
	
- Conclusion
	- Hard but essential.
	- Don't ask for permission to do testing, pair programming, or refactoring. You're the expert, so you decide.

----

## Clean Agile Chapter 6: Becoming Agile

Why does Agile fail? Because it isn't Agile.

- The values of Agile
	- The 4 things Agile needs
		- Courage: needed to do the work properly.
		- Communication: frequent interaction creates insight.
		- Feedback: catch and fix mistakes early.
		- Simplicity: minimize the number of problems.
	- Introduce the Agile methodology into every cycle of the project. Technical practices are essential too. Keep doing your best.

- Transition
	- It's hard. There are many failure cases. Even so, many organizations will attempt the transition to Agile.
	
- Coaching
	- Is it necessary? No. But sometimes it can be.
	- Scrum master: should be a coach, not a manager.
	- Everyone on the team should be a participant in Agile.
	
- Large-scale Agile
	- Agile is for small to medium-sized teams. It's not for large teams.
	- Methods for large organizations have already been worked out.
	- It's because of the uniqueness of software development that a special kind of discipline (Agile) is needed.
	- There's no such thing as large-scale Agile. If there is, it's just organizing small Agile teams.

- Agile tools
	- Git.
	- You must not become dependent on tools. First, figure out your own way of working.
	- Rather than fumbling around because an ALM tool is hard, just don't use it. Physical tools are recommended. If you need one, Trello is recommended.
	
- Coaching - a different view (written by Damon Poole)
	- Changing existing ways is hard. So a coach is needed.
	
- Conclusion
	- Becoming Agile seems like you just have to follow simple principles, right? That's the hard part.

----

## Clean Agile Chapter 7: Craftsmanship

The era of Agile transition has arrived. But?

- Agile aftereffects
	- Changing culture isn't easy. There are plenty of product owners who'll tell you to stop if you're spending time refactoring.
	- If you only handle the high-priority problems, technical debt builds up.
	- There are many cases where people don't follow Agile and then shift the blame onto Agile. This is the Agile aftereffect.
	
- Differing expectations
	- Production deployment at the end of every cycle is an enormous challenge for the development team.
	- It's not enough to just develop one high-priority feature. You have to continuously build flexible, robust software.
	- To strike a balance between flexibility and robustness, you need engineering skills. Collaboration alone is not enough. Support for technical capability is needed.

- Departure
	- You have to recognize that many problems are technical problems. You should emphasize not only Agile but also technology.
	
- Software craftsmanship
	- The manifesto
		- Not only working software, but also well-crafted software.
		- Not only responding to change, but also steadily adding value.
		- Not only individuals and interactions, but also a community of professionals.
		- Not only customer collaboration, but also productive partnerships.
	- Developers should become craftspeople. Let's be the best.

- A professional can be defined by the way that person works.
- Keep looking for better practices.
- Focus on values rather than practices.
- Study.
- Decide your development method yourself.
- Find the meaning of life in your work.

----

## Clean Agile Chapter 8: Conclusion

Agile is the real deal.

---

###  Clean Agile: Agile Values and Practices for a New Generation, by Robert C. Martin, translated by Jung Ji-yong, Insight, December 4, 2020, original: Clean Agile: Back to Basics

20211015
