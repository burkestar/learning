# Engineering Productivity (EngProd)

This page attempts to roundup articles and links discussing how other companies approach **Engineering Productivity**, **Developer Productivity**, **Engineering Efficiency** and **Developer Tools**.

These focus mostly on how to improve the productivity of engineering teams, and don’t include “personal productivity” tips for developers.

---

## Articles

- [Let a 1,000 flowers bloom](http://www.gigamonkeys.com/flowers/) then rip 999 out by the roots.
    
    - Sep 2015 article by Peter Seibel, author of the [Coders at Work](http://www.codersatwork.com/) book.
        
    - Tech Lead at Twitter for Engineering Efficiency
        
    - TLDR: as an industry we mostly don’t know how to do it and consequently massively under-invest in making our engineering orgs actually effective
        
    - See videos section below for notes.
        
- [Engineering Efficiency is now the biggest challenges to companies](https://anaxi.com/blog/2018/11/05/engineering-efficiency-is-now-the-biggest-challenge-to-companies/)
    
    - Nov 2018 article
        
    - Shortage of software engineers in the marketplace. More demand than is supplied by CS grads.
        
    - _It’s estimated there are about 3.5 million developers in the United States. Developers earn an approximate average of $100,000 per year. In that case, the efficiency loss would amount to an equivalent of $150 billion in the U.S. So it’s easy to understand why engineering productivity becomes even more important than access to new talent._
        
    - _This leads me to believe that it would be effective to use a bottom-up go-to-market strategy for any productivity tool, so developers can influence the final decision to buy a SaaS tool and reduce the sales cycle massively compared to a top-down approach._
        
- [Measuring Developer Productivity](https://hackernoon.com/measure-a-developers-impact-e2e18593ac79)
    
    - Initial POC that led to Scope product
        

- [Stripe - The Developer Coefficient](https://stripe.com/files/reports/the-developer-coefficient.pdf)
    
    - Sep 2018 survey
        
- [QA is not enough - you need to engineer productivity](https://saucelabs.com/blog/qa-is-not-enough-you-need-to-engineer-productivity)
    
    - Mar 2017 article from Saucelabs
        
    - Mission: “Reduce the time from concept to deliverable by providing our product development teams with the tools, practices and support to increase their productivity while maintaining high quality standards”
        
    - 4 goals:
        
        - Provide an easily maintainable and extensible framework that enables scrum teams to add and remove tests
            
        - Enable the automatic and early detection of failures within the software under development
            
        - Prevent the source of detected failures from moving any further downstream.
            
        - Accommodate all of this without impacting the engineers’ time
            
- [Keeping Master Green at Scale](https://eng.uber.com/research/keeping-master-green-at-scale/)
    
    - Uber Engineering article published in ACM Eurosys 2019
        

> Giant monolithic source-code repositories are one of the fundamental pillars of the back end infrastructure in large and fast-paced software companies. The sheer volume of everyday code changes demands a reliable and efficient change management system with three uncompromisable key requirements—always green master, high throughput, and low commit turnaround time. Green refers to a master branch that always successfully compiles and passes all build steps, the opposite being red. A broken master (red) leads to delayed feature rollouts because a faulty code commit needs to be detected and rolled backed. Additionally, a red master has a cascading effect that hampers developer productivity—developers might face local test/build failures, or might end up working on a codebase that will eventually be rolled back.
> 
> This paper presents the design and implementation of SubmitQueue. It guarantees an always green master branch at scale: all build steps (e.g., compilation, unit tests, UI tests) successfully execute for every commit point. SubmitQueue has been in production for over a year, and can scale to thousands of daily commits to giant monolithic repositories.

- [Why developers should embrace engineering productivity](https://otter.ly/productivity-engineering/)
    
    - By shifting left more responsibilities on to developers (they are now enabled to own their workflow end-to-end), it increased the cognitive load on these engineers.
        
- [LinkedIn Engineering Blog - Developer Productivity topics](https://engineering.linkedin.com/blog/topic/developer-productivity)
    
    - contains some articles related to developer productivity
        
- [DORA - DevOps Metrics](https://cloud.google.com/devops/)
    
- **State of DevOps**
    
    - [2019 Report](https://services.google.com/fh/files/misc/state-of-devops-2019.pdf)
        
- [Software Engineering at Google](https://arxiv.org/pdf/1702.01715.pdf)
    
    - Jan 2017
        
    - Software development
        
    - Project management
        
    - People management
        

## Videos

- [Engineering Effectiveness at Twitter](https://www.youtube.com/watch?v=8IyXcLFO9ns) (27 mins, Sep 2015)
    
    - Companion to blog article on “Let 1000 flowers bloom”
        
    - Know how to build abstractions to scale up software and scale up organizations.
        
    - Don’t have a good handle on scaling up the intersection between software and the organization. That is where engineering efficiency fits in.
        
    - Let a 1000 flowers bloom. New languages being used, new repos, developed own build system.
        
    - Fail whale at twitter during World Cup inspired them to move off monorail towards microservices.
        
    - Tooling became a challenge due to the diversity of approaches and opinions. No one owned this to fix the problem of divergence between build systems, separate repos, etc. Made decision to move into monorepo.
        
    - Hired new leader to head up Engineering Efficiency and consolidate some smaller dev productivity teams.
        
    - Why are we so bad at scaling up our tooling and processes?
        
        - Engineering efficiency is hard to measure. Can affect it though.
            
    - Quality, Speed, Joy - don’t have to pick 2 - they feed into each other.
        
    - Start with time savings (waiting for compiler, etc.).
        
    - 1% gain (5 minutes / day = 20 hours/year)
        
    - 10% gain (48 mins / day = 200 hours/year)
        
    - Flow. Do the tools help you stay in the flow or break you out of that flow. Breaking flow is 15 minutes time savings.
        
    - Tech Debt is the mind killer - compounds over time - saps our mental energy.
        
    - Senior people to set best practices, get buy-in, spread across the org.
        
    - Good tools are fun and delightful to work with.
        
    - Special team with some engineers to drop in, fix some problems that others on the team don’t have time to solve. Need to be flexible to be able to rapidly learn new code, tools, frameworks.
        
    
    - Parameters
        
        - E = total effectiveness of org
            
        - eng = # of total engineers
            
        - ee = # of engineers doing engineering effectiveness work
            
        - s = scaling factor (< 1)
            
        - b = boost (X% you want to increase)
            
    - 10k engineers scale need almost 1/3 of team on EE work
        
    - Need to standardize tools to get multiplier effect from efficiency boost, so everyone can benefit
        
    - Flowers in the garden. Let flowers grow while people experiment, and decide which ones to adopt within the garden (i.e. standardized tooling for all engineers)
        
- [Using Machine Learning for Developer Productivity at Facebook](https://www.youtube.com/watch?v=S7VJzAhzPTU) (41 mins, May 2019)
    
    - "Neural code search" that does clustering.
        
    - code recommendations
        
    - Automatic bug fixing (code generation)
        
    - "Big Code" initiatives - using code as data and applying Machine Learning.
        
    - Facebook AI tools: [https://ai.facebook.com/tools/](https://ai.facebook.com/tools/)
        
        - [https://code.fb.com/category/developer-tools/](https://code.fb.com/category/developer-tools/)
            
        - [fb.me/ncspaper](http://fb.me/ncspaper) | [fb.me/aromarecpaper](http://fb.me/aromarecpaper) | [fb.me/getafixpaper](http://fb.me/getafixpaper) | [fb.me/aromablogs](http://fb.me/aromablogs) | [fb.me/getafixblog](http://fb.me/getafixblog)
            

> Dev Tools owns most of the coding life cycle at Facebook, from the time code leaves an engineer's mind until it reaches the people who use our apps. Our mission is to increase developer efficiency so that we can continue to ship awesome products quickly. To accomplish this, we have focused on building a suite of tools and automation infrastructure that are reliable and fast, ensuring that every second of engineering time is spent on the things that matter.

- [Habits of Efficient Developers](https://www.youtube.com/watch?v=9-cyC6O81Bk) (37 mins, Jun 2018)
    
    - 1. Focus - 10-15 mins for context switch. Minimize interruptions. Long periods to focus.
        
        - Disable notifications. Reply when you get time. Asynchronous. Quick response not expected.
            
        - Interruptions you control (notifications) and those you do not control (someone stopping by your desk).
            
        - Pair programming
            
        - One thing at a time
            
    - 2. Master your IDE
        
        - Learn the functionality - play with it, read the docs
            
    - 3. No menial work - remember you are a developer. You can write a program to do the task.
        
        - Write programs for yourself.
            
        - Bash is the single constant (programming languages and other things changed over time) and worth learning
            
        - avoid GUIs
            
        - automate testing
            
        - repeatable developer environment
            
    - 4. Fast feedback
        
        - test-driven development
            
        - continuous code reviews
            
        - pair programming
            

![](https://imgs.xkcd.com/comics/is_it_worth_the_time.png "Attachment")

- [Engineering Efficiency powered by Go](https://www.youtube.com/watch?v=sxjIaF6xAK0) (15 mins, Gophercon 2017)
    
    - lineflow CLI tool built in Go with one command per step to perform the steps to create a pull request and follow their deployment process
        
- [Engineering Productivity @ Google](https://www.youtube.com/watch?v=ETtRxDEYeF4) (32 mins - 15 mins of Q&A, May 2018)
    
    - Make engineers more productive through tools, infrastructure, automation and analysis.
        
    - Remove frustration and toil
        
    
    - Instrument everything for deep analysis of bottlenecks.
        
    - How to operate as 2000 person functional discipline? Need to standard on best practices. Impact through standardization, collaboration and “verticals”.
        
    - Moving beyond “run every single test on every single change”. Applying ML. If you instrument everything, how can you make better decisions about optimizing what should be run.
        
    - How to do faster testing (unit, integration, load)
        
    - Release velocity and production stability. Close the loop with production. Need to instrument to understand if builds are stable or there are bugs.
        
    - Instrument as much as possible, but its hard to digest all the information and understand the story for what’s happening.
        
- [LinkedIn’s Engineering Productivity Tools Meetup](https://www.youtube.com/watch?v=0kbRxNiV6qs)
    
    - First talk is about Netflix internal build tools (in Go, better version of Gradle using YAML and Docker for mkdocs sites)
        
    - Second talk is about LinkedIn iOS app builds
        
    
- [What Makes Facebook Developers Productive](https://www.youtube.com/watch?v=KpweK7wBCOk) (21 mins, Dec 2017)
    
    - Remote Devserver
        
        - provisioned and ready day 1.
            
        - No setup, remote upgrades, remote access, remote resources (doesn’t slow down laptop), secure (can revoke access, switching to new laptop is trivial).
            
        - Having network access is rarely an issue
            
    - Monorepo
        
        - New folder for projects, can still have microservices - 1 per folder.
            
    - Diffs
        
        - CLI to create diff, launches web UI to collect data (test failures, notes about QA tests to verify, build status). Click button to trigger localization of strings.
            
    - Feature gating
        
        - Configuration for features is a dynamic lookup in an external tool (GateKeeper)
            
    - Dogfooding - internal testing of new features on their mobile devices
        
    - Communicating - workplace at Facebook. Groups. Similar to slack.
        
    - Hackathons - multiple times per year at each engineering site, show-and-tell at end of week. No organizer.
        
    - Fixathons - 1 week to work on bugs, legacy code improvements and dead code removal
        
    - Oncall shifts - Teams create their own dashboards and alerts. Weekly rotation. Can lookup who is on-call per feature, assign them critical bug.
        
    - Feedback -
        
- [How Google Software Engineers Work](https://www.youtube.com/watch?v=LnXk9XZjXbg)
    
- [Measuring and Managing Engineering Productivity](https://www.youtube.com/watch?v=eh8HX15Qymc) (32 mins, Jan 2017)
    
    - GitPrime, 15Five for feedback, Velocity calculations (days in blocked, time in stage)
        
    - Engineering Rubric, leveling exercise for junior vs senior level engineers, compensation/performance evaluation, etc.
        
- [Introducing Productivity Engineering](https://www.youtube.com/watch?v=u4MADwJbxpw)
    

## Books

- Release It!
    
- DevOps Handbook
    
- Continuous Delivery
    
- Accelerate
    
- Phoenix Project
    
- How Google Tests Software
    
- Developer Productivity Engineering (ebook)
    

## Software Products for Engineering Productivity

- [**Gitprime**](https://www.gitprime.com/)
    
    - [Crunchbase profile](https://www.crunchbase.com/organization/gitprime#section-overview)
        
    - Founded 2015
        
    - $12.5M Series A, acquired by Pluralsight in May 2019 for $170M
        
    - < 50 employees
        
    - Estimated ~$7M annual revenue
        
    - GitPrime aggregates data from git repos, ticketing systems, and pull requests and transforms them into easy to understand insights and reports. With visibility into the software development process, we help engineering organizations map initiatives to outcomes, adjust processes with confidence, and advocate for their teams with facts instead of feelings.
        
- [**Pinpoint**](https://pinpoint.com/)
    
    - [Crunchbase profile](https://www.crunchbase.com/organization/pinpoint-5#section-overview)
        
    - Build software better
        
    - Founded 2017
        
    - Series A, <50 employees, $16.5M raised
        
    - <$2M annual revenue
        
    - Competitors are considered BI visualization companies like Chartio, Sisense, etc.
        
    - Pinpoint is for companies that want to build software better. Our Engineering Performance Management platform synthesizes the work activity in software delivery tools, and applies proprietary AI to measure impact, anticipate risks, and unlock potential. We are on a mission to align technology and business leaders to drive a new era of high-performance engineering.
        
- [**Velocity by CodeClimate**](https://go.codeclimate.com/LI-move-faster?gclid=EAIaIQobChMI6M-05JDe5gIViJ6fCh3thAAUEAAYASAAEgJk5_D_BwE)
    
    - [Crunchbase profile](https://www.crunchbase.com/organization/code-climate)
        
    - Founded 2011
        
    - Series A, $6.5M funding
        
    - < 50 employees
        
    - Superpowers for engineering teams
        
    - Code Climate consolidates the results from a suite of static analysis tools into a single, real-time report, giving your team the information it needs to identify hotspots, evaluate new approaches, and improve code quality.
        
    - Competitors are considered to be static code analysis tools / security scanning and QA tools like Veracode and Checkmarx
        
- [**Gitclear**](https://www.gitclear.com/gitprime_alternative_for_technical_managers?gclid=EAIaIQobChMI6M-05JDe5gIViJ6fCh3thAAUEAMYASAAEgJTxvD_BwE)
    
    - [Crunchbase profile](https://www.crunchbase.com/organization/static-object)
        
    - Founded 2016
        
    - < 10 employees
        
    - Based in Seattle
        
    - For Project Managers, Lead Developers, and CTOs, Static Object provides an unparalleled degree of data-driven insight into the world of your engineers. Whether you want to know what's being worked on during a given hour, day, or year, we help you cut through gigabytes of commit data to quickly get answers to your most imperative questions.
        
- [**Waydev**](https://waydev.co/)
    
    - [Crunchbase profile](https://www.crunchbase.com/organization/waydev#section-overview)
        
    - Founded 2017
        
    - Seed funding
        
    - 7 employees
        
    - <$1M revenue
        
    - Claim 300+ companies using
        
    - Waydev analyzes your codebase from Github, Gitlab, Azure DevOps & Bitbucket to help you bring out the best in your engineers work.
        
- [**Scope**](https://scope.ink/)
    
    - Measure developer productivity and analyze team performance by visualizing the data of code contributions.
        
- [Microsoft Intellicode](https://visualstudio.microsoft.com/services/intellicode/)
    
    - AI-assisted development
        
    - [https://venturebeat.com/2019/05/20/microsoft-wants-to-apply-ai-to-the-entire-application-developer-lifecycle/](https://venturebeat.com/2019/05/20/microsoft-wants-to-apply-ai-to-the-entire-application-developer-lifecycle/)
        
- [Jellyfish](https://jellyfish.co/)
    
    - Series A funding 2020
        
    - [Crunchbase profile](https://www.crunchbase.com/organization/jellyfish-445f)
                
    - Aggregates primarily Jira and Github, integrates the data and presents interactive visualizations. Can rollup to team and organizational level, or drill-down to individual. Categorizes work and can see resource allocation % over time. Calculates simple DORA metrics like cycle time, etc.
        