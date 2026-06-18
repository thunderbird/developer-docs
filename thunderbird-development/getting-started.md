---
description: New contributor guide on how to contribute in various ways to Thunderbird.
---

# Getting Started Contributing

### Being an Open Source Contributor

When you contribute to an open source project, you are joining a community around that project. Different communities can be governed differently but in general a consensus approach is adopted instead of a top down decision making process. This can make the project progress move slower, but it comes with the added benefit that every community member's opinions are valued and are considered for the project leaders to act on.&#x20;

The Thunderbird project is very similar. We are an open source project that anyone can contribute to, in any number of ways (see [Contribution Areas](getting-started.md#contribution-areas) below for possible contribution paths). Thunderbird is also a company that employs full-time engineers to dedicate resources to constantly improving the project. There is a Thunderbird Community Council, that acts as the Community representation to sign off on things like our roadmaps and budget - you can learn more about the Thunderbird Community Council on their website, [council.thunderbird.net](https://council.thunderbird.net).

### Contribution Process



### Contribution Areas

Below you will find a list of areas where we would love to see more contributions.

Overwhelmed and not sure where to start? Give [Beta Testing](beta-testing.md) a try!

<table data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>Design</td><td><a href="design.md">design.md</a></td></tr><tr><td>Desktop App</td><td><a href="desktop.md">desktop.md</a></td></tr><tr><td>Mobile App</td><td><a href="mobile.md">mobile.md</a></td></tr><tr><td>Support &#x26; Documentation</td><td><a href="support-and-documentation.md">support-and-documentation.md</a></td></tr><tr><td>Beta Testing</td><td><a href="beta-testing.md">beta-testing.md</a></td></tr><tr><td>Translations</td><td><a href="translations/">translations</a></td></tr></tbody></table>

### Improving documentation

Perfect documentation doesn't exist and there is always room for improvement. If you read through any of our documentation and notice a typo, or something that is unclear, or some section you feel is missing.. feel free to add it or tell us about it.

* [**Source docs**](https://source-docs.thunderbird.net) - Technical documentation on the Thunderbird source code, code architectures, etc. This set of documentation lives alongside the source code so in order to submit changes to it, you'll need a bugzilla account and patches are submitted to phabricator. [Read more about contributing to these docs here](https://source-docs.thunderbird.net/en/latest/documentation/index.html).
* [**Developer docs**](https://developer.thunderbird.net/) - Developer documentation on non-code topics like how to build Thunderbird, the project's roadmap, add-ons, etc. This [developer documentation lives in GitHub](https://github.com/thunderbird/developer-docs/) so in order to submit changes to it, you'll need a GitHub account and submit a pull request ([learn how to submit a GitHub pull request](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request).
* [**User docs**](https://support.mozilla.org/en-US/) - User support documentation for all Mozilla products, including Thunderbird and Thunderbird for Android. This set of documentation is modified using Markup, directly in the website. [Read more about contributing to knowledge base articles](https://support.mozilla.org/en-US/kb/writing-guide-knowledge-base-articles?redirectslug=support-document-guide).

### User support

Users of all levels will consult the vast set of [Thunderbird knowledge base articles](https://support.mozilla.org/en-US/products/thunderbird). This user documentation location is a great place to learn, start discussions on the forum, or ask a question.

You can help by watching this space to answer questions. If you encounter a topic that is beyond your knowledge but you want to learn more, you can always [join us on Matrix](https://matrix.to/#/#thunderbird:mozilla.org) to access the Thunderbird staff to assist you.

### Beta testing

We always need more beta testers to let us know if something is not working (by filing bugzilla issues) before we release a given version. This is a great place to start contributing if you're not sure where to jump in.

Every month we have a new beta release and a new monthly release ([read more about the release cadence here](https://developer.thunderbird.net/releases/cadence)) so it is important to catch new issues quickly.

Read more about [running Thunderbird beta on our knowledge base article](https://support.mozilla.org/en-US/kb/thunderbird-beta).

### Filing bugs

If you are using any version of Thunderbird and you encounter something you feel is not working properly, let us know in a bugzilla issue.

1. [Search if your issue already exists](https://bugzilla.mozilla.org/query.cgi?format=specific) (select Thunderbird as the product). This helps us minimize the number of duplicate issues filed. If you find that your issue has already been filed by someone else, it is very helpful to us if you just take a quick look at the issue and add a comment that adds any additional information about your issue that might help us solve the problem.
2. If your issue has not been reported yet, [file a new bugzilla issue](https://bugzilla.mozilla.org/enter_bug.cgi?product=Thunderbird).

### Triaging bugs

When a new bug is filed, it's status is UNCONFIRMED. This means the issue has not been triaged (verified that the bug in fact does exist) by someone else other than the bug reporter. If an issue exists only for one person (i.e. the bug reporter), the problem is likely localized to that person's environment in some way and is not an actual Thunderbird code issue. That is why it is very helpful for another person (i.e. yourself) to see if the issue can be reproduced and make sure the issue has **reproducer steps** and **information about the environment** where it was reproduced (version of Thunderbird, operating system, etc.).

[Here is a list of UNCONFIRMED bugs that need triaging](https://bugzilla.mozilla.org/buglist.cgi?product=Thunderbird\&component=Untriaged\&resolution=---\&list_id=17499226).

The number of people triaging new bugs is low and that makes it hard to keep up with the new incoming bugs every day. Your help would be greatly appreciated!

### Code patches

If you want to fix a bug or add a feature, the way to do it is through direct code contributions.

[Read more about fixing bugs here](https://developer.thunderbird.net/thunderbird-development/fixing-a-bug).

Note that if you want to add a feature, it's best to first have a discussion about it with the Thunderbird developers to make sure that it fits with the project's direction. The best place for these discussions is [the developer mailing list on Topicbox](https://thunderbird.topicbox.com/groups/developers).

## I'm stuck and need help!

If you are trying to contribute in any way and find yourself stuck and not sure how to proceed, please [join us on Matrix](https://chat.mozilla.org/#/room/#maildev:mozilla.org) and tell us about it so we can help you.
