# HLASM Plugin (Software project experience report)

The project was created in cooperation with Broadcom (we were all employed there). That was great, because we

- had an actual _place_ to work on the project together,
- Broadcom provided extra motivation; we had a chance to experience a realistic workplace and even got paid.

The project result can be viewed e.g. on VS Code Marketplace: https://marketplace.visualstudio.com/items?itemName=broadcomMFD.hlasm-language-support

The project got open-sourced by Broadcom, it is now hosted at https://github.com/eclipse/che-che4z-lsp-for-hlasm . (The code submitted to the project for evaluation did not contain commits from anyone other than from the team, OTOH you can see that opensourced projects pack up commits from various people quite fast.)

## Documentation and LaTeX
Project documentation and user documentation are saved in this repository root. Specifically, we used several ugly LaTeX tricks for the documentation that may eventually be useful for others, mainly:

- a working source code file index (files are tied to headings of chapters that describe them)
- relatively good formatting and sectioning (does not look like the usual boring LaTeX)
- inclusion of large figures and schemas
- a reasonable floating environment for code listings

The documentation should be buildable using `docs-tex/Makefile`.

## Various assorted hints

### Licensing

Working under Broadcom, we needed to check if all third party libraries are compliant with the company policies. We were lucky, but sometimes this may restrict you from using your favorite library. When working with a company, ask earlier than later.

(This may be complicated even for non-corporate projects, e.g. when using a GPL-style-licensed library in a BSD-style-licensed program.)

### Library choice

Even when not in a corporate company, try to choose external libraries that are **actively maintained** and have a large community.

For example, we made a mistake of using a library that had only a few commits per year, and ended up refactoring it out of our code because of unfixable compatibility issues.

### C++ and portability

If you choose C++ for your project and want your project to be cross-platform, allocate extra time for ensuring that. We used CMake, and it was really hard and time-consuming to make everything work on each platform.

Everytime you need to support a new platform, you will need extra research and work to ensure that everything works. This can be surprising even if porting across Linux distributions (e.g. Arch, Debians (Ubuntus), Alpine Linux, Gentoo and RPM distros all have a _completely different_ layout of libraries).

Docker images for each supported linux platform help a lot.
Automated testing helps a lot too.

**Supervisor note:** If you don't need your project to work on Windows, proper use of `autotools` and `pkg-config` can solve most portability issues before they appear. Even with Windows, it's sometimes better to treat it as a special case.

### Automation

Automate as many tasks as possible; implement the automation early in your project.

We ended up using GitHub Actions, which is free for public projects and we can definitely recommend it. Right now, each pull request in our project is built and checked on 6 different environments. Spotting mistakes is much easier that way.

### Code formatting

Define your code formatting style _early in the project_ and _enforce it_ in your pipeline. We thought that we do not need it at first, but it is inevitable that there will be some differences between code style of team members. Even code style of each programmer evolves in time. We ended up reformatting all the code close to the end of the project, changing all C++ files. We used `clang-format`, which was fine.

**Supervisor note:** Git-merging two code branches with divergent formats is almost impossible to do automatically.

### Testing

Testing is important. (Cliché, but true.)

We used Sonarcloud (free for public projects) to visualize what parts of code are not covered by tests. That proved to be way more useful than just getting the information that "your test coverage is 70%".

### Not making mistakes in C++

Use sanitizers! We used the address sanitizer and the undefined behavior sanitizer from CLang (there is also a thread sanitizer). Add these tools to the pipeline and run your tests with sanitizers. It was helpful especially when we needed to find a bug that caused crashes only on one specific platform.

If applicable to your project, use a fuzzer to enhance your application stability and find many input-parsing bugs. We used the CLang fuzzer.

**Supervisor note:** Aiming for 0 warnings from all possible static checkers and linters (`cc -Wall`, `cppcheck`, `clang-tidy`, ...) can prevent a lot of surprising errors. Even if you never actually reach zero warnings, it is very useful to check if there are new warnings or not, especially after "making a tiny cosmetic fix in the middle of a core-functionality header file". Running tests in `valgrind` and sanitizers may sound like an overkill, until you get unexplainable memory leaks.

### Writing documentation

We might have spent as much as 350 total human-hours compiling, writing and correcting the documentation.

**Supervisor note:** Writing the final documentation will take 2 times more time than you expect now. Start early.
