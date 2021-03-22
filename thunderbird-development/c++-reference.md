---
description: Useful links for working on Thunderbird in C++.
---
# C++ References and resources

## Coding style

- The [Firefox C++ style guide](https://firefox-source-docs.mozilla.org/code-quality/coding-style/coding_style_cpp.html) is used for Thunderbird.
- Use [clang-format](https://firefox-source-docs.mozilla.org/code-quality/coding-style/format_cpp_code_with_clang-format.html) to check (and tweak) your formatting.

## Strings

- See the [string](https://firefox-source-docs.mozilla.org/code-quality/coding-style/coding_style_cpp.html#strings) section of the Mozilla C++ code style.
- The [Mozilla Internal string guide](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Guide/Internal_strings) (archived) still the most in-depth guide to the available classes and their use.

## Arrays

- [XPCOM array guide](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Guide/Arrays) (archived and _mostly_ valid. But beware.)
- NOTE: don't use `nsIArray`/`nsIMutableArray` in new code. `nsTArray` is much more ergonomic for both C++ and Javascript.

## XPCOM

- [Mozilla XPCOM docs](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM) (archived)
- [RefPtr](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Glue_classes/nsRefPtr) (including how it differs from nsCOMPtr).
- [IDL style guide](https://firefox-source-docs.mozilla.org/code-quality/coding-style/coding_style_cpp.html#idl)
- [XPIDL](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPIDL)

## File handling

- [nsIFile](https://searchfox.org/mozilla-central/source/xpcom/io/nsIFile.idl) provides platform-independent path manipulation.

## Dealing with memory leaks

- [ASan](https://firefox-source-docs.mozilla.org/tools/sanitizer/asan.html#address-sanitizer) (Address Sanitizer)
- [Leak-hunting strategies](https://developer.mozilla.org/en-US/docs/Mozilla/Performance/Leak-hunting_strategies_and_tips) (archived, out of date, but still some useful information)
- [DMD](https://developer.mozilla.org/en-US/docs/Mozilla/Performance/DMD) "Dark Matter Detector. (Archived.)
- [Valgrind](https://web.archive.org/web/20180309013311/https://developer.mozilla.org/en-US/docs/Mozilla/Testing/Valgrind) (On archive.org. Would be nice to update this info).

## Netscape Portable Runtime (NSPR)

- Provides platform-independent low level functionality (threads, timers, I/O etc).
- [NSPR Docs](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSPR) (archived)

## LDAP

- [Mozilla LDAP SDK](https://wiki.mozilla.org/Mozilla_LDAP_SDK_Programmer%27s_Guide/LDAP_C_SDK_Function_Reference) (ancient, but mostly correct).

