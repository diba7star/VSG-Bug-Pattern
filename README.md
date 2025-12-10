# The Vanished Static Ghost (VSG) Bug 👻

**Discovered/Coined by:** [Your Name / danidiba]  
**Date:** December 10, 2025  

## 🧐 What is a VSG Bug?
The **Vanished Static Ghost (VSG)** is a rare and deceptive type of software regression often mistaken for a standard Heisenbug. It occurs in large, legacy systems under specific version control conditions.

### Definition
A VSG bug arises when a commit containing a critical **side-effect** (e.g., static initializer, global constructor, or module-level execution) is permanently removed from the git history via a **force-push** or aggressive history rewriting (rebase/squash), while the rest of the codebase implicitly relies on that side-effect.

### Why is it a "Ghost"?
1.  **Invisible Source:** The code causing the correct behavior no longer exists in `HEAD`.
2.  **Clean Analysis:** Static analysis tools and linters pass 100% because there are no direct symbol dependencies—only runtime side-effects.
3.  **Forensic Challenge:** Since the commit is unreachable, standard `git log` or `git blame` will yield no results. The bug appears to have "always been there" or appeared spontaneously.

## 🚩 Symptoms & Detection
* **Environment Specific:** Often appears in Production but not in local dev environments (due to caching or artifacts).
* **No Code Change:** The bug appears without any apparent logic change in the related modules.
* **Resurrected by Rollback:** Only a hard rollback to a significantly older hash fixes it.
* **Reflog Traces:** The only evidence lies in the local `git reflog` of the developer who performed the rebase, or in cached CI/CD artifacts.

## 🧪 Real-World Examples
* **Java/JNI:** A static block loading a native library (`System.loadLibrary`) was in a "cleanup" commit that got dropped. Result: Random `UnsatisfiedLinkError`.
* **C++:** A global object constructor responsible for initializing a specific Mutex was squashed out of existence. Result: Silent Deadlocks.
* **Python:** A module-level configuration setup in an `__init__.py` file was removed during a refactor rebase. Result: KeyErrors deep in the application.

## 🛡️ Remediation & Prevention
If you suspect a VSG Bug:
1.  **Stop searching the code; search the history.**
2.  Use `git reflog` on the machine that performed the last merge/rebase.
3.  Use `git fsck --unreachable` to find dangling commits.
4.  **Prevention:** Avoid logic in static initializers and forbid force-pushes on shared branches.

---
*This concept identifies a specific pattern of regression caused by history rewriting. Terms and definitions are open for community discussion.*