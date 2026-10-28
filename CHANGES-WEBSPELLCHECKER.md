# WebSpellChecker changes for Hunspell

This document records WebSpellChecker-specific changes on top of upstream Hunspell.

### Upstream base
- Each upstream release gets its own branch, `multithreaded_<version>`, which starts at the upstream release tag. The current one is `multithreaded_1.7.6`. `multithreaded_1.7.2` stays for older releases.
- Changes come into it through pull requests from feature branches, merged with "Create a merge commit", so each change stays a separate commit with its hash. Do not use "Squash and merge" or "Rebase and merge".
- `git log v1.7.6..multithreaded_1.7.6` lists these changes. A release gets a tag such as `v1.7.6-wsc.1`.
- To update to a new upstream release, make `multithreaded_<version>` from its tag, make a feature branch from it, move the changes onto the feature branch one by one with `git cherry-pick` (or `git rebase --onto v1.7.7 v1.7.6`) and open a pull request into `multithreaded_<version>`. Other projects pin these commits by hash, so a released commit must stay reachable through its branch or tag.
- `master` is not used for releases.
- Do not use GitHub's "Sync fork": it merges upstream `master`, which is not a release.

### Configuration
- Implement an API method to disable compound hyphen suggestion behavior.
- Make the following parameters configurable through the API:
  - MAXNGRAMSUGS
  - NOSPLITSUGS
  - MAXDIFF
  - ONLYMAXDIFF.
- Add getters for these parameters.
- Turn PHONE-based suggestions on or off at runtime (`set_phone`).

### Dictionaries
- UTF-8 is the default encoding when the .aff file has no SET line.
- A Hunspell object can be created with empty .aff and .dic paths, for word lists added with `add()`. The empty .dic path is not reported as a load error.

### Suggestions
- Check found suggestions of capitalized words against KEEPCASE and FORBIDDENWORD without the global time limit, as 1.7.2 did. Since 1.7.3 the check runs under the limit, and once the time is up it drops every suggestion, so long compound words got none.
- Check the ngram suggestions against forbidden words without the time limit, for the same reason: since 1.7.3 the check dropped every ngram suggestion once the time was up.
- Give each of the three suggestion loops (simple words, 2-word and 3-or-more-word compounds) its own time budget, as 1.7.2 did. Since 1.7.3 one budget covers all three, so a slow first loop stopped the search before the ngram suggestions.

### Security
- Throw an exception for words with non-BMP Unicode characters to prevent losing the suggestmgr state.

### Multithreading
- Make the library multithreaded by synchronizing the affix manager.
- Do not touch the shared trace counter in `suggest` while tracing is off (1.7.4 added it, and threads sharing one Hunspell object raced on it).
- Keep the start time of the suggestion search per thread. Since 1.7.3 it is a `SuggestMgr` member that `suggest` and `spell` write, so threads sharing one Hunspell object overwrote each other's time limit.
