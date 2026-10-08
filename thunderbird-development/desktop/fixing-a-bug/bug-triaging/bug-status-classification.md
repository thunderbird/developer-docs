# Bug Status Classification
When setting a bug priority and severity classification, note that you can click on Priority in the Category section to go to [the mozilla page with the different levels](https://wiki.mozilla.org/BMO/UserGuide/BugFields#priority) and when to set them.

## The Bug is REPRODUCIBLE
* Move the bug into the **correct component** if it’s not already there.
* Mark it as **NEW** if it’s still **UNCONFIRMED**.
* Add the **triaged** keyword (that will require a severity level).
* Set **Priority** and **Severity** if you feel comfortable, or ask a manager or module owner to help you define those.
* Add **other keywords** if relevant (ux, access, perf, etc.).
* If it’s a **regression**, try to find the bug that regressed that feature and add it along with the REGRESSION keyword. [https://mozilla.github.io/mozregression/](https://mozilla.github.io/mozregression/) is a good tool for you and reporters.
  * If you can’t find a regression, add the **regressionwindow-wanted** keyword.

## The Bug is NOT REPRODUCIBLE
* Ask the reporter to try in [troubleshoot mode]() without any add-on.
* Ask for more info if not already provided, like OS or their particular setup.
* Try to get more clear instructions - users tend to understand better if they are asked to make a list of actions or clicks they used.
* DON’T simply write “It works for me”, that’s not helpful and it’s just noise, and it can increase the frustration of the reporter.
* Issues are very situational, as they might be caused by errors in C++, JavaScript, translation, a custom configuration from the user, or many other things.
  * For instances in which a tab is blank or something doesn’t load as expected, ask the user to check the Error Console and report any messages in there (ctrl+shift+J or cmd+shift+J for macOS)

## The Bug is a DUPLICATE
* Bugzilla does a terrible job of suggesting potential duplicates when the user files a bug, so you will stumble upon the same issue reported by different users.
* Try to identify the duplicates and close them by adding the original bug that we use as a reference. Please give a reason for duping, a) to educate the user, b) so user doesn’t feel like we are just closing bugs and brushing them off.  Boilerplate text:
  * This issue is being investigated in bug XXXX.  If you think that bug is missing important information that will help lead to fixing or reproducing it, please add it to that bug.

