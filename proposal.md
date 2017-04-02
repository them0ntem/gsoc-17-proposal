
# GSoC 2017 Proposal to Kivy (Python Software Foundation)


**Project: KV Compiler: A compiler for the KV language**

## Table of Contents
* [Sub-organisation information](#sub-organisation-information)
* [Student Information](#student-information)
* [University Information](#university-information)
* [Platform Details](#platform-details)
* [Project Proposal Information](#project-proposal-information)
	* [Proposal Title](#proposal-title)
	* [Proposal Abstract](#proposal-abstract)
	* [Project Description](#project-description)
		* [Current Implementation](#current-implementation)
	* [Project Details](#project-details)
		* [Phase I: Working with Existing Functionality](#phase-i-working-with-existing-functionality)
		* [Phase II: Implementing binding and optimisation.](#phase-ii-implementing-binding-and-optimisation)
		* [Phase III: Augmenting Testing, Documentation and Debugging](#phase-iii-augmenting-testing-documentation-and-debugging)
		* [Additional Goals](#additional-goals)
	* [Timeline](#timeline)
		* [Pre-GSoC + Community Bonding Period](#pre-gsoc--community-bonding-period)
		* [Week 1 and Week 2 (May 30 – June 13)](#week-1-and-week-2-may-30--june-13)
		* [Week 3 and Week 4 (June 13 – June 27)](#week-3-and-week-4-june-13--june-27)
		* [Week 5 and Week 6 (June 27 – July 11)](#week-5-and-week-6-june-27--july-11)
		* [Week 7 and Week 8 (July 11 – July 25)](#week-7-and-week-8-july-11--july-25)
		* [Week 9 and Week 10 (July 25 – August 08)](#week-9-and-week-10-july-25--august-08)
		* [Week 11 and Week 12 (August 08 – August 22)](#week-11-and-week-12-august-08--august-22)
		* [Week 13 (August 22 – August 29)](#week-13-august-22--august-29)
		* [Post-GSoC](#post-gsoc)
	* [Link to a patch/code sample](#link-to-a-patchcode-sample)
* [Other Commitments](#other-commitments)
* [Why am I apt. for this project](#why-am-i-apt-for-this-project)
* [Other Schedule Information](#other-schedule-information)
* [References](#references)


## Sub-organisation information
Sub-organisation with whom you hope to work: Kivy
 
## Student Information
* Name: Manthan Sharma
* Email: manthanfoss@gmail.com
* Telephone: +91-9462235282
* Time Zone: Jaipur, India UTC+5:30
* IRC: montem@irc.freenode.net
* Source Control Username: http://www.github.com/manthansharma
* Web: http://manthansharma.com/

## University Information
* University: The LNM Institute of Information Technology, Jaipur
* Major: Computer Science and Engineering
* Current Year: 3rd Year
* Expected Graduation date: In June 2018
* Degree: B-Tech

## Platform Details

 * OS : macOS Sierra
 * Hardware Configuration : 2.2 GHz Intel Core i7/ 16GB
 * IDE :
	 * Python : PyCharm - JetBrains
	 * Editor : SublimeText 3

----------

## Project Proposal Information
### Proposal Title
Kivy: KV Compiler: A compiler for the KV language

### Proposal Abstract
This Proposal is based on description of KV Compiler: A compiler for the KV language project in the ideas page provided by Kivy Organisation. The goal of the project it to develop a compiler which compiles *kv code* into *python code*. The compiler should be modular and extensible so that compiler can be continued to improve the *kv language*.

### Project Description
Speed is of the utmost importance for any Software Development and Execution.

The KV language was initially developed as fundamental component of Kivy which allows one to describe a GUI, from the creation of a Widget tree to the actions that should be taken in response value changes and events. Over the years, it has been matured enough to become concise way to create rule bindings using the Kivy properties and events.

Internally, python code that reflects these rules are created and bound to the properties and events. These bindings are not at all optimised because upon each widget creation all of these rules are re-evaluated and bound.

With the development of KV Compiler, this process can be significantly optimised by pre-compiling the `kv` code, especially the bindings into `python` code. On the other hand, this will also lead to update and fix some of the long-standing `kv`  language issues.

#### Current Implementation
Current implementation of compiler use three phase batch binding:-
 1. First phase work as initialisation phase, children are created for rules and literals and non-literals are initialised to those values.
 2. The middle phase or binding phase creates bindings for all the rules for binding for the calling of bindings it used `__delayed_call_fn` to bind the rules in iterative.
 3. Final post initialisation phase, all the property rules are re-evaluated, all the `on_prop` observer are triggered, then all child widget dispatch event `on_kv_apply` indicating all the rules are done being applied, finally root widget dispatches `on_kv_apply` to itself.

This sets of bindings stop unnecessary dispatching and updation of property during creation of widgets. Batch Binding helps to remove any intermediate dispatching but in case there isn't any intermediate value of rules batch binding works heavy in comparison to past implementation.

#### Discussion
I initially wanted to implement my proposal with keeping optimisation as upmost priority.  However, after talks with Matthew and Dessant, it soon turned out that this was not a correct approach to move forward. As such, this proposal currently takes consistency with optimisation approach, The main idea of this approach is that the existing functionality related work should occur first with succeeded by implementing binding and optimisation and finally testing and documentation.

### Project Details
Currently, `kv-compiler` has already progressed quite far, it's in pre-alpha stage but in scope of improvement. Since the period of GSoC may lead to the further development of modules and sub-modules through other contributions. I plan on executing this proposal in three inter-mixing phases:

#### Phase I: Working with Existing Functionality
In this phase, the idea, basically is to refurbish (completely or partially) all the method that are already implemented in `KVCompiler` and `lang.py`. As such, no new functionality is expected to be implemented in `Compiler Front End` (Line reconstruction, Lexical analysis, Preprocessing, Syntax analysis, Semantic analysis). Current, alpha stage PR has conflicts that has be to resolved to rebase it with the current kivy development branch. Compiler has some bugs in showing syntax or id error in `.kv` code that has to solved to help developer find bugs in his/her code. 

> For example:  If there is some unknown id in .kv code instead of showing `Error: id not found` it's show traceback with error `NameError: global name 'level' is not defined`.
> 
> **Link:** https://gist.github.com/manthansharma/18fccae332e9dbbe0dd0c9caf33f7366

There are some small bugs like this which can be solved with some tweaks and changes in compiler code.

Testing and documentation, if required for already implemented ones and implementing documentation of binding in compiled code will also occur during this period. It will also serve as warm-up for the next two phases, which would be more code sensitive and can be initiated before and during completion of `Phase I` Submission.

#### Phase II: Implementing binding and optimisation.
This phase will primarily focus on implementing specific functionalities that aren't currently implemented in `kvcompiler.py` and `lang.py` but can be implemented within a stipulated amount of time. This includes adding  analysing the widget tree and rebind the bindings (working on `update_intermediates` and `unbind_widget` it has been mostly implemented but some changes and optimisation can be done with this.). Currently compiler is lacking in field of optimisation which can be added to it (basic idea behind this proposal). Some of the optimisation algorithm which can be implemented are

* **Constant folding:** 	At time of `property_handlers(self)` constant folding can be applied to property value and can be bind to property it depends to update with dependant property. (Max. Depth level: 1)
* **Automatic parallelisation (New feature for KV Lang):**	*Part of Additional Goals (if possible will be implemented in gsoc period.)*
* **Inline Compression** 

These are written in basis of importance from top to bottom but before adding optimisation to compiler Analysis has to be done b'coz `Accurate analysis is the basis for any compiler optimisation`. 

> This is not the final work that need to be done as Phase II is most important one for this proposal which needs to be updated constantly by discussion with mentor.

#### Phase III: Augmenting Testing, Documentation and Debugging
This phase would largely be a follow-up of the first two phases, especially the second phase. This phase will focus more on extensive testing, debugging and final integration of compiler with existing `Kivy Development Branch`  rather than implementing.

There are ways by which KV Compiler can work with real live project, We can compile all `.kv` files at execution of Kivy Code into a folder recursively or user can override this by giving path of all `.kv` files to be compiled in `Class(App)` or somewhere in main `.py` which initiate the execution of the Kivy App.

Remaining compatibility issues, if they arise, will also be dealt with during this phase. The proposal would be finished off with a final update to the documentation. Also, stop printing error traceback if error is based on `.kv lang` when kivy is not running in development mode.

#### Additional Goals
*These are `Post-GSoC` goals. I haven't work on how to implement them. These goals can be added in compiler in future. This is just basic description of implementation.*

##### Selectors in Kv language
As proposed by [Depaulicious](https://github.com/Depaulicious) in issues https://github.com/kivy/kivy/issues/2727 can be easily implemented.

In addition to proposal, selectors can be enclosed in some other brackets or can be written in different syntax compared to that of Widgets.

In the compiler, same as all other widgets selectors will be compiled to `__s` functions and called to execute the KV Selectors rule and set the property and in the post-initialisation phase `__s` function is called after inherited rules are post initialised.

##### Proposal for a Theme manager
I haven't think much on this part but can worked on it with help of selectors as proposed by [tito](https://github.com/tito) in issues https://github.com/kivy/kivy/issues/691

### Timeline

#### Pre-GSoC + Community Bonding Period
For understanding KVCompiler I need to understand all three Cython, Python, and Kivy with kivy bindings and widget behaviours for which I will be learning more about Cython, Kivy Event Dispatcher and Bindings with also optimisation of Compiler.

I will be reading about these, gain more knowledge of all and will go through required documentations (Mentioned in “References” below).

I will be in constant touch with my mentors and take suggestions. During this time, if my mentor thinks that I am ready to start programming then I will start working on [Phase I](#phase-i-working-with-existing-functionality).

#### Week 1 and Week 2 (May 30 – June 13)
##### Specific Goals
 * Rebase the work done on compiler till now with `master` branch as an initiation to Phase I.
 * Start writing some kivy code which will covers all the aspect of `Kivy` which will help in testing of current compiler or with updates that will be done in `Phase II`
##### Side Goals
 * Discuss optimisation technique that needs to be worked in initial aspect of `Phase II`.
 * Documentation will be done for already implemented modules (will also help in future goals). 
#### Week 3 and Week 4 (June 13 – June 27)
##### Specific Goals
 * Testing already written modules of compiler and updating any modules.
 * Benchmark the results obtained from testing, and make changes to the Phase II work approaches if required.
 * Using benchmark mark to decide whether to continue with batch binding or not.
##### Side Goals
 * Will start working on [Phase II](#phase-ii-implementing-binding-and-optimisation) (subject to the availability of time and if mentor thinks).
 * Make Preparation for the [Phase I](#phase-i-working-with-existing-functionality) evaluation with completion of documentation.

**My goal up till Phase 1 evaluations would be to finish off with Phase I and start implementation work of Phase II.**
#### Week 5 and Week 6 (June 27 – July 11)
##### Specific Goals
 * Implement modules needed for analysis of compiled code needed for optimisation.
 * Implement `Optimisation techniques` that were finalised during [Phase I](#phase-i-working-with-existing-functionality).
##### Side Goals
 * Write basic test and resolving bugs found for module implemented, so that it will not break during extensive testing in [Phase III](#phase-iii-augmenting-testing-documentation-and-debugging).
#### Week 7 and Week 8 (July 11 – July 25)
##### Specific Goals
 * These both weeks will mostly be same as of [Week 5 and 6](#week-5-and-week-6-june-27--july-11).
##### Side Goals
 * Make Preparation for the mid-term evaluation.
#### Week 9 and Week 10 (July 25 – August 08)
##### Specific Goals
 * Write extensive testing for compiler which covers all the aspects of `Kivy Language`.
 * Update `lang.py` if required, to support the KV Compiler in correct way and utilise most of it.
 * Work on integration of compiler for no. of `.kv` files in optimise manner which are proposed in [Phase III](#phase-iii-augmenting-testing-documentation-and-debugging).
#### Week 11 and Week 12 (August 08 – August 22)
##### Specific Goals
 * Investigate `Optimisation` algorithm in `KVCompiler` for a possible update on their usage with `.kv lang`.
 * Final check for any issues or conflicts in compiler or between `compiler` and `master` branch.
* Final benchmarking the results obtained from extensive testing and update to `Kivy` wikis.

#### Week 13 (August 22 – August 29)
* Buffer time for finishing up on documentation or any other piece of implementation that needs refactoring, or wrapping up any functionality left untouched due to delays.
* Work on the additional goals planned (subject to the availability of time).
#### Post-GSoC
Kivy is the first open-source organisation that I contributed to, and the journey has been simply amazing. Over time, I have realised that collaborating with the sharpest minds of the world is a pleasure beyond words. The experience I have gained so far is enriching in itself. I have the following plans post-GSoC:
* Keep contributing to `Kivy` and its sister projects and will continue to work on KVCompiler.
* Lastly, I also hope to represent team `Kivy` at the upcoming conventions (`PyCon India`), and talks organised at my college.

### Link to a patch/code sample

**I have implemented the following for Kivy Designer:**

 1. [Update start page design](https://github.com/kivy/kivy-designer/pull/288) (Waiting for approval)
 2. [Create temporary directory for new-project](https://github.com/kivy/kivy-designer/pull/289) (Waiting for approval)
 3. [Add New Project Wizard](https://github.com/kivy/kivy-designer/pull/293)  (Waiting for approval)
 4. [Add File Patch](https://github.com/kivy/kivy-designer/pull/295) (Merged)

**I have implemented the following for Plyer:**

 1. [Multilingual Support for TextToSpeech](https://github.com/kivy/plyer/pull/267) (Waiting for approval)
 2. [QRCode Support](https://github.com/kivy/plyer/pull/266) (Waiting for approval)
 3. [Speech to Text (WIP)](https://github.com/kivy/plyer/pull/269) (Work in Progress)

**Other contributions:** 

1. [Kivy](https://github.com/kivy/kivy/pulls?q=is:pr%20author:manthansharma)
2. [Kivy-Garden](https://github.com/kivy-garden/garden.xpopup/pulls?q=is:pr%20author:manthansharma)
3. [Kivy-Garden](https://github.com/kivy-garden/garden.xpopup/pulls?q=is:pr%20author:manthansharma)
3. [Plyer](https://github.com/kivy/plyer/pulls?q=is:pr%20author:manthansharma)

****

## Other Commitments

 1. **Do you have any other commitments during the main GSoC time period, May 23rd to August 23rd?** No.
	- **Do you have exams or classes that overlap with this period?**
I have no exam in the period of gsoc. I can commit more than 6-7 hours from Monday to Friday and every alternate weekend. My next semester classes will commence from end of July, So I will not be able to work full time except for the weekends. As it will be my 4th year, so work load from academic side will be less.
	- **Do you plan to have any other jobs or internships during this period?** No.
	- **Do you have any other short term commitments during this period?** No.
 2. **Have you applied to any other organisation? If so, do you have a preferred project/org?** No.

## Why am I apt. for this project
Familiarity with KV Compiler and its coding style. I love to code and have been doing it for past 2 years with principle language Python, Php, C and JS. I have been contributing to Kivy and its sister projects for past 3-4 months(mainly [Kivy-Designer](https://github.com/kivy/kivy-designer) and [Plyer](https://github.com/kivy/plyer)). I have learn the basic of *Compiler Designing* will be learning more in the Community Bonding Period and have understand the working of compiler already progressed [here](https://github.com/kivy/kivy/pull/3456).  I have done freelancing project using open source technologies and some python projects in Kivy, Django and PyQt some of them could be found at my GitHub profile. One 1K+ code project in Kivy could be found [here](https://github.com/manthansharma/kivy-irc) and I have also worked on compiler `2-Pass Assembler`. After some years when millions of people would be using KV Compiler to design and develop beautiful Kivy App with fast execution and build time, I want to be remembered as the guy who contributed towards it.

## Other Schedule Information
None.

## References

 1. https://github.com/kivy/kivy/pull/3456
 2. https://github.com/kivy/kivy/issues/2727
 3. https://github.com/kivy/kivy/issues/691
 5. https://kivy.org/docs/guide/lang.html
 6. https://kivy.org/docs/guide/events.html
 7. https://en.wikipedia.org/wiki/Optimizing_compiler
 8. I would also like to mention that the structure and format of this proposal is inspired from a number of outstanding proposals from previous year GSoCers and proposal template provided by Python Software Foundation.


