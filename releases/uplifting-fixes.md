## Uplifting Fixes

Some fixes are more urgent than others and "riding the train" will take too long to get the fix to users. This is where uplifts come into play. Uplifts allow a fix to be fast-tracked into the Beta, Release, or ESR channels.

---

### **Uplifts to Beta, Release, and ESR**
Uplifts to Beta, Release, and ESR are limited to high-impact bug fixes. Features and enhancements are not candidates for uplift.

**Patches nominated for uplift should:**
- Be limited to stability, security, or high-impact bugs.
- **For Beta uplifts:** Already have landed, tested, and stabilized in comm-central.
- **For Release/ESR uplifts:** Already have landed, tested, and stabilized in comm-beta.
- Include tests or provide a strong justification for the absence of tests.
- Not change any localizable strings.

**Uplifts can include:**
- Major top crash fixes.
- High volume startup crash fixes.
- Security fixes.
- Dataloss fixes.
- Fixes for high-impact regressions with a broad impact.
- Fixes for high-impact bugs in a major feature.

### **Process for Uplifts**
Requesting an uplift is done by setting the relevant flag (`approval-comm-beta`, `approval-comm-release`, or `approval-comm-esr`) on a Bugzilla patch attachment to `?`. An Uplift Request Form is automatically generated to provide the required information. All uplifts must use the Uplift Request Form.

Ensure uplift requests are submitted at least two working days prior to any scheduled release date. This will allow the release team to build the release the day before the scheduled release date, giving ample time for troubleshooting and testing prior to release day.
