# ElectricityMap - Quality and Evolution

<!-- 
- The overall software quality processes that apply to your system - srinath
- The key elements of the system’s continuous integration processes - srinath
- The rigor of the test processes and the role of test coverage - yifei
- Hotspot components from the past (previously changed a lot) and the future (needed for roadmap) - sunwei
- The code quality, with a focus on hotspot components - sunwei
- The quality culture, as evidenced in actual discussions and tests taking place in architecturally significant feature and pull requests (identify and analyze at least 10 such issues and 10 such pull requests) - everyone
- An assessment of technical debt present in the system - andrew -->
## Software Quality
A large part of software quality stems from the quality of the code itself. Projects that lend themselves to large amounts of spaghetti code[^spaghettiwiki] suffer from shoddy programming practices, difficulties in maintaining source code, and are simply not fun to work on. To prevent something like this from happening to electricitymap, the developers have established a few ground rules:
- A fixed basic structure for building a new parser. This must be followed by anyone wanting to build a new parser. 
- Fixed rules for finding and verifying data sources.
- Fixed rules for updating or adding production capacities for a given region.

Another measure of overall software quality is testing and CI (**C**ontinuous **I**ntegration) tools such as CircleCI[^circleci], but these will of course described in more detail in subsequent parts of this essay. Every PR that is opened is first reviewed by one of the core developers, who are the team at Tomorrow Co., before being approved for production. More about CI will be explained in next section.

## Continous Integration
Continuous integration is one of 12 principles of ["Extreme Programming"](https://ronjeffries.com/xprog/what-is-extreme-programming/) (often shortened to XP) that were originally laid out by Kent Beck. XP was the dominant agile method before Scrum eventually took over.[^Agile] Martin Fowler defines CI as "a software development practice where members of a team integrate their work frequently, usually each person integrates at least daily - leading to multiple integrations per day."[^MFowlerCI] There are a few checks running on electricitymap that use [GitHub CI](https://docs.github.com/en/actions/guides/about-continuous-integration#about-continuous-integration-using-github-actions) (aka GitHub Actions, which is the official name), but the primary CI service used is [CircleCI](https://circleci.com/). CI checks are run for every single PR and for each of these there is a clear overview of the results of all checks/pipelines. A sample is shown below:

![](https://i.imgur.com/tUsxUr8.png)
Figure 1: CI checks for PR #3050. 

According to a developer at Tomorrow Co. (we reached out to them on the official Slack channel for electricitymap), different CI solutions are used internally - meaning the API, redis cache, and data processing pipelines. As noted in our previous blogpost, all these systems are closed source and thus not visible externally. Further information was not provided to us. 


## Hotspot and Code Quality
Regarding an opensource project that is the size of ElectricityMap, it is important to consider the hotspot components for the project. A hotspot component of a project is the part that has been frequently changed, so it gets more attention and requires more time to be spent on it by the developers. In order to have a more visual take on what the hotspot components are in ElectricityMap, we used CodeScene.[^codescene] The results of the hotspot map are shown below:
![](https://i.imgur.com/wVzEHn7.png)
{{<image file="hotspot_map.png" caption="Hotspot map for ElectricityMap">}}
Figure 2: Hotspot map for ElectricityMap

From the figures above, we can observe that the major hotspot compoents are in the 'parses' and 'web' directories. The color of the reddots indicate the location of hotspots in the project. We can observe there are a few parsers which frequently needed attention from the developers, the most significant one is the 'ENTSOE.py', which is fetching the data from the European Network of Transmission System Operator, since it represents 42 electricity transmission system operators from 35 countries across Europe, there is quite a broad ranges of functions need to implemented and maintained by the developers. 
![](https://i.imgur.com/GvqRAWC.png)
{{<image file="hotspot_map_parser.png" caption="Hotspot map focus on parser">}}
Figure 4: Hotspot map focus on parser. 


When observing the detailed code of 'ENTSOE.py' and other dark red files from the hotspot map, they are well-documented with clean and easy-to-understand code. So the hotspot component of ElectricityMap can achieve a relatively high score on readability and the clarity of the code. However, when we are reviewing the test file for it, 'test_entsoe_quality.py', the last time this file was updated was 2 years ago, so even though this file 'ENTSOE.py' is the hotspot in ElectricityMap, its test file is not updated frequently enough to keep up with the changes. We can argue that the testing components will become the future hotspot components for the ElectricityMap project. Since the testing will ensure the best quality of the code, and testing will also inform the developers about any change that might break the system. However, it will need the coordination of the Tomorrow team ensuring the testing to become the future hotspot components. 

We also viewed the two major directories of the hotspot components with the programming languages they used, ‘parses’ and ‘web’ directories. 
![](https://i.imgur.com/7HIiQiS.png)
{{<image file="hotspot_map_language.png" caption=" Hotspot map with coloring by programming languages. (purple: Python, Green: JavaScript)">}}
Figure 5: Hotspot map with coloring by programming languages. (purple: Python, Green: JavaScript)

As we can see from the figure above, the ElectricityMap has two major components that can be differentiated by programming languages. The purple components including “parsers” are written in Python, they are mostly backend extracting energy consumption data from external APIs. The green components including “web” are written in JavaScript, they are frontend web development components using the React library. The clear division between the frontend and backend components enables the Tomorrow team and the opensource developers to maintain the project and extend extra features without affecting the performance of the entire codebase, so the ElectricityMap’s code structure has decent maintainability and extensibility.

For the next section, we will not only look at the quality of the code, but also focus on the quality of the issues and pull requests of the ElectricityMap project.

## Test Processes

In ElectricityMap, the unit-tests are stored in `parsers/tests` folder and `config/co2eq_parameters.test.js` file. `parsers/tests` test the available parsers and `co2eq_parameters.test.js` test the co2 conversion algorithm of the website.

Unfortunately, while ElectricityMap uses these pytests within their continuous integration framework, the maintainers do not explicitly require new tests for a newly added parser. For example, this [pull request](https://github.com/tmrowco/electricitymap-contrib/pull/3007/files?file-filters%5B%5D=) adds extra zones, data sources, and a new parser, but no additional tests are added. This trend is apparent for other pull requests as well. Currently, `parsers/tests` contains 93 parser files, but only 17 test files are available.

This seems to be an irresponsible decision made by the developers, but the reality is unfortunate. Most grid companies do not their data to individual users or make it publicly accessible, API keys of the data of the service provider are often needed to run these tests locally. The unavailability of API does challenge developers to make accurate and maintainable tests. In order to combate this, some developers use mock data to mitigate the problem, which can be found in `parsers/mock` folder.

As you have seen in the previous section, besides the `parser` folder, the `web` component is also a hotspot for ElectricityMap. This module covers all front-end web-elements of the project. Currently, these part of the code is not tested, only jslint is applied to restrict the coding style.

## Quality Culture
<!-- 10 PRs + 10 issues -->
<!-- 
Andrew's issues: `2942`, `2950`, `2998`.
Srinath's issues: `2699`, `738`, `2580`. -->

The Tomorrow team themselves state that there isn't any specifically chosen architectural style, though some are present by inherent need, such as client-server communication for the application to work properly. Much of the project's framework is dependent upon continuous access to a very large number of independent APIs, meaning that many facets of the code need to be written on a case-by-case basis. Nowhere else is this more evident than in the ElectricityMap regional parsers for energy production/consumption. Every region depends upon a different API that may provide information in varying formats, so every parser needs to perform the relevant API calls, data cleaning, and data processing operations in differing manners. This is where a large number of the bugs and issues stem from. 

When addressing new bugs, the Tomorrow team is communicative with the community on how code should be formatted, particularly on the front end. Many pull requests have to do with translations, and the core developers have clear standards to preserve formatting on the front end. One of the side effects of this standardized formatting is certain languages tend to have long abbreviations to save space. For example, issue `3027` contained debate over foreign language terminology with the aim of preserving continuity of formatting between languages and regions. 

With respect to larger-scale changes issued in pull requests, issues `2898`, `2942`, `2950`, and `2998` dealt with a specific method for calculating CO2-equivalent impact that the core developers ensured were uniform across every region and country. In particular, a community developer pointed out that pull request `2973` was accepted, even though the calculation standard was violated. Core developer *Corradio* admitted that mistakes were made, and that outside experts were being consulted both with respect to the data quality as well as the calculation integrity. This indicates that the core development team is focused on the integrity of their service more than the availability, which of course is an inherent trade-off present in this project due to the variability of data sources.

Issue `2699` deals with the cross border exchange of energy between Northern Norway and Sweden. One of the contributors points out how the parser currently only captures about a third of the flow between Norway and Sweden. Core developer *Corradio* agrees that this is a bug and that the long term solution would be to split Sweden into different regions since they exchange energy with their nordic neighbours. An issue that highlights the importance given by Tomorrow Co. to scientific rigor is `738`. It has been opened solely for the purpose of finding *peer reviewed* studies of a standardized approach to redefine LifeCycle **GHG-intensity** (**G**reen **H**ouse **G**as) factors of various power plant categories per geographical zone (country, region).


## Technical Debt Assessment
Technical debt, defined as the incurred future cost of implementing quicker and/or easier solutions to problems in the short term in place of more rigorous solutions that would take more effort to achieve, is present in most software systems; ElectricityMap is no exception. Technical debt may also accrue "interest" such that as time goes on, the effort required to address the short-term solutions grows, meaning future refactoring would be more costly and time-consuming. One of the most prevalent issues with EM is issues with their regional APIs, where every zone on the map has its own nuances in how CO2-equivalent impact (CO2eq) is calculated. When an API is altered or becomes inaccessible, some regions may need to adjust how the information is calculated and displayed. However, due to the lack of ability to generalize parsers, many of the issues on the repository have to due with side-effects of changes to parsers or APIs.

After a review of numerous closed issues and merge requests, it appears that the technical debt of EM is relatively low, with a few exceptions. Of the previous 50 accepted pull requests dating back approximately 6 weeks, we have identified only 3 that contain obvious technical debt: `3055`, `3037`, and `3009`. For example, in issue `3055` a user identified a problem with previously implemented React hooks that led to a crash, and previous linting tests were unable to catch the error. The pull request implements a hotfix that fixes trivial cases, as manually testing every dependency is time-consuming, so the resolution was to merge the hotfix with a comment for later. Such an issue could be considered a minor debt, as the amount of manual testing scales linearly with every new implemented dependency. Similarly, pulls `3037` and `3009` both implement fixes for known issues, but leave open the possibility of future changes by leaving deprecated parsers in place, should the developers wish to revert in the future, thereby eliminating "interest" on any debt incurred.

## References

[^codescene]: CodeScene code visualization tool https://codescene.io/

[^MFowlerCI]: (Definition of CI)[https://martinfowler.com/articles/continuousIntegration.html]

[^Agile]: [Extreme Programming - Martin Fowler](https://www.martinfowler.com/bliki/ExtremeProgramming.html)

[^spaghettiwiki]: [Definition of spaghetti code from Wikipedia](https://en.wikipedia.org/wiki/Spaghetti_code)

[^circleci]: [CircleCI](https://circleci.com/)