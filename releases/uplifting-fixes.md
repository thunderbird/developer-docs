## Uplifting Fixes

Some fixes are more urgent than others and "riding the train" will take too long to get the fix to users. This is where uplifts come into play. Uplifts allow a fix to be fast-tracked into the Beta, Release, or ESR channels.

---

### **Uplifts to Beta**
Uplifts to Beta are limited to bug and security fixes only. Features and enhancements will be included in the next merge.

**Patches nominated for uplift to Beta should:**
- Be limited to bug and security fixes.
- Have already landed on comm-central.
- Have been tested and stabilized on comm-central.
- Include tests or provide a strong justification for the absence of tests.
- Not change any localizable strings.

**Uplifts to Beta can include:**
- Proven performance improvements.
- Top crash fixes.
- Security fixes.
- Fixes for recent regressions.
- Low-risk improvements early in the Beta cycle.

### **Uplifts to Release and ESR**
Uplifts to Release and ESR are limited to stability and security fixes only. Features and enhancements will be included in the next merge.

**Patches nominated for uplift to Release or ESR should:**
- Be limited to stability and security fixes only.
- Have already landed in comm-beta.
- Have been tested and stabilized on comm-beta.
- Include tests or provide a strong justification for the absence of tests.
- Not change any localizable strings.

**Uplifts to Release and ESR can include:**
- Major top crash fixes.
- High volume startup crash fixes.
- Security fixes.
- Fixes for regressions with a broad impact.
- Fixes for problems in a major feature.

### **Process for Uplifts**
Requesting an uplift is done by setting the relevant flag (`approval-comm-beta`, `approval-comm-release`, or `approval-comm-esr`) on a Bugzilla patch attachment to `?`. The Uplift Request Form (see below) is automatically generated to provide the required information. All uplifts must use the Uplift Request Form.

Ensure uplift requests are submitted at least two working days prior to any scheduled release date. This will allow the release team to build the release the day before the scheduled release date, giving ample time for troubleshooting and testing prior to release day.

---

### **Uplift Request Form**

**[User impact if declined]**
- Provide details on how an end user would be impacted with or without this change. In addition to the Steps to Reproduce (STR), explain the deeper implications for users.

**[Is this code covered by automated tests?]**
- Options: Yes / No / Unknown.

**[Has the fix been verified in Daily? (or Beta for an ESR uplift?)]**
- Indicate whether the fix has been verified in a Daily or Beta build, and by whom (e.g., the reporter or QA).

**[Needs manual test from QA?]**
- Options: Yes / No.
- If "Yes," include the steps to reproduce either by referencing an existing comment with STR or elaborating directly.

**[List of other uplifts needed]**
- If this patch depends on other changes not present on the target branch, list those dependencies here.
- Ensure approval is also requested for dependent patches.

**[Risk to taking this patch]**
- Options: Low / Medium / High.
- Provide a justification as to why the benefits of the uplift outweighs the associated risks.

Examples:
- **Low**: A one-line CSS change impacting only the settings page.
- **Medium**: Due to code complexity and integration with other areas, there might be regressions in related functionality.
- **High**: The patch involves complex changes with a high likelihood of fallout or regressions, which may require extensive manual testing or mitigation strategies.

**[Why is the change risky/not risky? (and alternatives if risky)]**
- If the change is risky, explain the potential impact. In other words, describe the potential negative impacts that could occur if the change leads to a regression. What mitigations were taken to reduce the risk? What alternative solutions were considered?

**[String changes made/needed]**
- Answer "none" if no string changes were made.
- If there are string changes, add details and justification to help with l10n review.
