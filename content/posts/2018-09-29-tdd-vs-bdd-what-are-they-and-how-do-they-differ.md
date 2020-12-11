---
title: 'TDD vs. BDD: What Are They and How Do They Differ?'
author: dfar

date: 2018-09-30T01:25:03+00:00
excerpt: |
  
  <![CDATA[]]>
url: /tdd-vs-bdd-what-are-they-and-how-do-they-differ/
categories:
  - Uncategorized

---
<!--[CDATA[
</p-->

Improving the testability of software is extremely important, and a difficult ideal in its own right. To achieve that, engineers and others interested in software testability have created many strategies, tools, and methodologies to improve the testability of software solutions.

With so many methodologies and techniques available, which one should you choose? Two of the most prominent methodologies I&#8217;ve encountered are&nbsp;[test-driven development][1]&nbsp;(TDD) and&nbsp;[behavior-driven development][2]&nbsp;(BDD). In this post, I&#8217;ll describe what TDD and BDD are, discuss their real-world applications, and explain how they differ. In addition, I&#8217;ll talk about considerations between TDD vs. BDD.

Here are some facts to consider when thinking about TDD vs. BDD.<figure class="wp-block-image">

![TDD vs BDD][3] </figure> 

## What Is TDD?

Test-driven development (TDD) is one of the most popular methodologies today for adding testability to a software project. Test-driven development became popular around 2003 via Kent Beck, alongside the test-first&nbsp;[extreme programming][4]principle. TDD aims to provide 100 percent test coverage in code by working with a very short feedback cycle when writing code. The short feedback cycle drives implementation, verifying functionality with automated testing. In other words, you write tests beforehand and then write functionality so that the software passes those tests.

The TDD methodology and its&nbsp;short feedback loop are defined in Kent Beck&#8217;s book&nbsp;[Test-Driven Development: By Example][5]&nbsp;with the following steps:

1. Write a failing test (red).

2. Make the test work ASAP (green).

3. Refactor the code.<figure class="wp-block-image">

![][6] </figure> 

### Step 1: Write a Failing Test

The first step with TDD is to create a &#8220;red&#8221; test that fails. This test doesn&#8217;t need to pass, or even compile at all. The primary purpose of this step is to define the functionality to test.

### Step 2: Make the Test Pass ASAP

Once you&#8217;ve established a failing test, the next step is to make it pass as quickly as possible. There are no rules in getting the implementation correct to turn the test &#8220;green.&#8221;

### Step 3: Refactor the Code

Once you have a passing test, you have the means to test the functionality created in step two. The next step is to refactor the code to a good standard, such as by removing any duplication created during the previous steps. While refactoring, you&#8217;ll have the ability to use the test you just created to ensure that the code is always working as intended.

### TDD: Benefits and Drawbacks

Now that you know the steps involved in test-driven development, let&#8217;s look at some of the benefits and drawbacks. Some of the benefits of practicing TDD include:

  * Improve code quality by virtue of writing code with testability in mind—this generally results in well-defined and decoupled code that is easy to maintain over time.
  * Provide a framework of automated tests that can provide a metric of the code quality at any point. This is great for understanding regression effects when changing functionality in the codebase.
  * Test creation alongside functionality creation. Instead of tests being an afterthought, they become as critical as writing functionality.

Of course, there are some disadvantages as well:

  * When considering the business case for writing with testability in mind, it can be difficult to explain how testing can contribute to the bottom line. This is especially difficult for teams working under unrealistic deadlines.
  * To follow TDD correctly, you need both the knowledge to actually follow the TDD methodology correctly and the discipline to commit to following the rules of the methodology. Especially as timelines tighten and deadlines loom closer, it becomes easy to push off testing as something to &#8220;address later.&#8221;&nbsp;

Now that we&#8217;ve covered the basics of TDD, let&#8217;s talk about the next testing methodology in our discussion of TDD vs. BDD, behavior-driven development.

## What Is BDD?

Behavior-driven development (BDD) combines TDD methodology with core principals from&nbsp;[object-oriented programming][7]&nbsp;and&nbsp;[domain-driven development][8].&nbsp; BDD allows both technical and non-technical teams to be involved in software development testing efforts. Writing tests in a way that explicitly defines the behavior expected for the software accomplishes this by allowing both non-technical and technical members to be involved in the test definition process.

Behavior-driven development focuses on creating user stories explaining the desired functionality. This is opposed to test-driven development, which focuses on having a feedback loop driven by written tests. In 2007, Dan North wrote a&nbsp;[guideline][9]&nbsp;for the different steps of defining a user story—let&#8217;s take a look.

### Step 1: Given&nbsp;

First, a behavior-driven test will establish the initial context for the test. This step is called &#8220;given,&#8221; which describes a situation in which functionality will be determined. This step is important in establishing the state of the application before testing for functionality. Since a behavior-driven test should still look to isolate functionality, it&#8217;s important to be specific with the context of the user story when determining functionality in the application.

### **Step 2: When**

The next step in writing a BDD test is to establish the &#8220;when&#8221; criteria. The &#8220;when&#8221; step describes the action occurring with the &#8220;given&#8221; content. Generally, this step is a verb describing something happening in a system, such as clicking a page or registering a user.

### Step 3: Then&nbsp;

Finally, the last step is to define a &#8220;then&#8221; statement for the behavior-driven test. This statement describes the final result that occurs after performing an action with the &#8220;when&#8221; statement.

### BDD: A Quick Example

Let&#8217;s look at an example. If I&#8217;m building an application that collects registrations for an event, I&#8217;d think about the different functionalities this application can have. A core function to test would be the capability to add an attendee to an already existing event. My &#8220;given&#8221; statement would look something like: g_iven that an event already exists and is selected._

Next, I would create a &#8220;when&#8221; statement to provide an action to perform for this test. Since my goal is to add an attendee to an event, my &#8220;when&#8221; statement would be: w_hen a user registers for the given event._

Lastly, I would finish the test by including a &#8220;then&#8221; statement. Once the user registers for an event, I need to confirm the user was actually registered. My statement could look like:&nbsp;_t__hen confirm the user is registered for the event._

### BDD: Some of the Benefits

Let&#8217;s go over a few benefits of using behavior-driven development:

  * Good user of establishing strong business requirements for the application. Behavior-driven tests provide a framework of tests focusing on the business functionality of the application since they are created with desired behavior in mind.
  * High collaboration between domain expert and developers. Building tests in this way&nbsp;helps&nbsp;prevent miscommunication between requirements and implementation.
  * Provides repeatable high-level tests for regression throughout the application. If you&#8217;ve ever run into a case where business users forget the functionality they&#8217;re asking for, this benefit will work really well for you.

Now that we have a better understanding of behavior-driven testing, let&#8217;s look at the differences between TDD vs. BDD.

## TDD vs. BDD: Understanding the Difference

When looking at the difference between TDD vs. BDD, it&#8217;s important to understand that you don&#8217;t have to choose just one or the other—you can use TDD and BDD together to cover your organization&#8217;s testing needs. By using aspects of both TDD and BDD, you&#8217;ll be able to have low-level tests for the details of the codebase, and higher-level tests that touch on the direct functionality desired by the customer. Additionally, the different sectors of your organization can have different involvement in the testing process. A different workflow in test creation can involve different team members, depending on their technical capability.

First, consider an environment where the development team is driving internal functionality. Test-driven development centers around tests defined by the development team. TDD&#8217;s short feedback cycle works well within a development team since the tests created with this methodology tend to be small and quick.

Second, an environment with more collaboration between the domain experts and development team will benefit from a stronger behavior-driven development process. This allows for tests written in simple English that makes sense for a non-technical user. In addition, behavior-driven tests make for great acceptance tests. These tests define the desired functionality without considering implementation details, allowing them to include non-technical users in creation.

## Using TDD and BDD in Harmony

This post should have given you a good understanding of what test-driven development and behavior-driven development are. When considering whether to implement TDD vs. BDD in your workflow, both can create an effective test creation strategy. If you aren&#8217;t using one (or both!) of these methodologies, give them each a try and see if they help you with creating better tests for your software.

]]>

 [1]: https://blog.ncrunch.net/post/test-driven-development-first-principles-explanation.aspx
 [2]: https://en.wikipedia.org/wiki/Behavior-driven_development
 [3]: https://blog.ncrunch.net/image.axd?picture=2018%2f9%2fTDD_vs_BDD_What_Are_They_and_How_Do_They_Differ.jpg
 [4]: https://en.wikipedia.org/wiki/Extreme_programming
 [5]: https://www.amazon.com/Test-Driven-Development-Kent-Beck/dp/0321146530
 [6]: https://blog.ncrunch.net/image.axd?picture=2018%2f9%2f___refactor_flow_chart-(1).jpg
 [7]: https://en.wikipedia.org/wiki/Object-oriented_programming
 [8]: https://en.wikipedia.org/wiki/Domain-driven_design
 [9]: https://dannorth.net/whats-in-a-story/