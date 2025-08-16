# Effective Engineer

Chapter outline
- Part 1 Adopt the Right Mindset
	- Focus on High-Leverage Activities
	- Optimize for Learning
	- Prioritize Regularly
- Part 2 Execute, Execute, Execute
	- Invest in Iteration Speed
	- Measure what you want to improve
	- Validate your ideas early and often
	- Improve your project estimation skills
- Part 3 Build Long-Term Value
	- Balance quality with pragmatism
	- Minimize Operational burden
	- Invest in your team's growth

## Chapter 1 Focus on High Leverage Activities

Leverage = Impact Produced / Time Invested

Mentoring is a high leverage activity.

Amount of value you produce per unit time can be increased in three ways:
1. By reducing the time it takes to complete a certain activity
	1. "How can I complete this activity in a shorter amount of time?"
2. By increasing the output of a particular activity
	1. "How can I increase the value produced by this activity?"
3. By shifting to higher-leverage activities
	1. "Is there something else that I could spend my time on that would produce more value?"

Output as an engineer measured:
- number of products launched
- bug fixed
- users acquired
- engineers hired
- quality improved
- revenue generated

Ways to increase leverage of development time:
- automating parts of development or testing that were manual, to iterate more quickly
- prioritizing tasks based on criticality for launch to maximize value of what you ship
- talking with customer support team to gain insights into customer's biggest pain points, use those insights to find opportunities to deliver more value with less effort
- learning a profiling tool to reduce time to identify a bottleneck in performance

Direct energy towards leverage points, not just easy wins.

## Chapter 2: Optimize for learning

Fixed mindset
- "human qualities are carved in stone", believe born with predetermined amount of intelligence
- stick with things they do well, validate their intelligence

Growth mindset
- believe can cultivate and grow intelligence and skills through effort
- view challenges and failures as opportunities to learn
- less likely to give up on their paths to success

Invest in your rate of learning
- learning follows an exponential growth curve.  Knowledge gives you a foundation, enabling you to gain more knowledge even faster.
- earlier you optimize for learning, the more time your learning has to compound.

Seek work environments conducive to learning
- fast growth
	- growth rate of core business metrics (active users, ARR,)
	- are initiatives you'd be working on high priorities, with sufficient support and resources from the company to grow?
	- how aggressively has the team been hiring in past year?
	- how quickly have strongest team members grown into positions of leadership?
- training - formal or informal onboarding programs, mentoring opportunities, 
	- Are new members expected to figure things out on their own, or is there a more formalized way of onboarding new engineers?
	- Formal or informal mentorship?
	- What steps company has taken to ensure team members continue to learn and grow?
	- What new things have team members learned recently?
- openness - reflect on failures, learn from them and adapt.  Culture of curiosity.
	- do employees know what priorities different teams are working on?
	- do teams meet to reflect on whether product changes and feature launches were worth the effort?  Do they conduct post-mortems after outages?
	- How is knowledge documented and shared across the company?
	- What are examples of lessons that the team has learned?
- pace
	- is moving quickly reflected in the company or engineering values?
	- what tools does the team use to increase iteration speed?
	- how long does it take to go from idea's conception to launch approval?
	- what percentage of time is spent on maintenance vs developing new products and features?
- people - surround yourself with smarter, more talented, more creative people.
- autonomy - freedom to choose what to work on and how to do it
	- how often do individuals switch teams and projects?
	- what breadth of the codebase can an individual be expected to work on over course of a year?
	- do engineers participate in product design discussions and influence direction?

Dedicate time on the job to develop new skills
- 20% time.  1-2 hours per day.
- study code for core abstractions written by the best engineers
- write more code
- master the programming language that you use
- send code reviews to harshest critics
- enroll in classes
- participate in design discussions on projects you're interested in
- work on a diversity of projects
- be on a team with a few senior engineers whom you can learn from
- jump fearlessly into code you don't know


Always be learning
- learn new programming languages and frameworks
- invest in skills that are high demand
- read books
- join discussion group
- attend talks, conferences, and meetups
- build strong network of relationships
- follow bloggers who teach
- write to teach
- tinker on side projects
- pursue what you love - spend less time passively surfing tv or internet, and use that time instead to work on things you're passionate about.

## Chapter 3 Prioritize regularly

- Focus on what directly produces value
- Focus on important and non-urgent
- Limit amount of work in progress
- Make prioritization a habit (weekly and daily cadence)

## Chapter 4 Invest in iteration speed

- Find out where the biggest bottlenecks in your iteration cycle are, whether they’re in the engineering tools, cross-team dependencies, approvals from decision-makers, or organizational processes. Then, work to optimize them.

## Chapter 5: Measure what you want to improve

- use metrics to drive progress (focus on the right things, can confirm you achieved your objectives, when visualized over time can guard against future regressions)
	- performance ratcheting - lower the metric further after any reductions are achieved
- pick the right metric to incentivize the behavior you want
	- good metrics can serve as North Star to align team efforts toward a common goal
	- hours worked per week vs productivity per week - avg productivity per hour goes down, errors and bug rates increase, burnout and turnover, overtime followed by periods of "undertime" as employees catch up with their lives - increases near 70 hour work weeks
	- clickthrough rates vs "long clicks" - superficially relevant links cause user to bounce back if its not what they were looking for.  Long clicks is the key metric Google uses for search result quality - the user clicks and doesn't need to return
	- avg response times vs 95th percentile - focusing on the average will shave off milliseconds from all users and save on aggregate computation time, but focusing on the extremes will bring down the worst case behaviors that are usually experienced by your power users (who have the most data and activity which requires more computation to support)
	- bugs fixed vs bugs outstanding - don't incentive developers to be less rigorous when testing new features
	- registered users vs weekly growth rate of registered users - ratio of new registered users in a week over total registered users shows whether growth is slowing down or sustainable
	- what not to measure
		- call center "average handle time" incentivizes operators to get the customer off the phone quickly, at the expense of delivering great customer service.  Instead Zappos focuses on going above and beyond for every customer.
	- economic denominator - align all employees along a single core metric - "if you could pick one and only one ratio - profit per X - to systematically increase over time, what x would have the greatest and most sustainable impact on your economic engine?"
	- actionable vs vanity metrics
		- actionable metric can be causally explained by team's efforts
		- vanity metrics may imply forward progress, but they don't necessarily reflect the actual quality of team's efforts.  Could be external factors outside the team's control that influence these metrics.
	- responsive metric updates quickly enough to give feedback about whether a given change was positive or negative, so your team can learn where to apply future efforts
	- metric needs to be robust so external factors outside team's control don't add noise
	- picking the right metric has a significant impact on behavior, so its a powerful leverage point
- instrument everything to understand what's going on
	- when establishing goals, its important to carefully choose core metrics to measure and optimize.  When it comes to day-to-day operations, measure and instrument as much as possible
	- without instrumentation, you are flying blind to how the software is functioning
	- many of the questions we want to answer are exploratory and don't know ahead of time what to measure.  Therefore we need flexible tools and abstractions to make it easy to track additional metrics
- internalize useful numbers

