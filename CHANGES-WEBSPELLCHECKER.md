# WebSpellChecker changes for Hunspell

This document records WebSpellChecker-specific changes on top of upstream Hunspell.

### Configuration
- Implement an API method to disable compound hyphen suggestion behavior.
- Make the following parameters configurable through the API: MAXNGRAMSUGS, NOSPLITSUGS, MAXDIFF, ONLYMAXDIFF.

### Security
- Throw an exception for words with non-BMP Unicode characters to prevent losing the suggestmgr state.

### Multithreading
- Make the library multithreaded by synchronizing the affix manager.
