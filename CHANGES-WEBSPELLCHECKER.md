# WebSpellChecker changes for Hunspell

This document records WebSpellChecker-specific changes on top of upstream Hunspell.

### Configuration
- Add parameter to disable additional hyphen suggestions.
- Add getters for suggestion parameters.
- Make NOSPLITSUGS parameter configurable.
- Make n-gram suggestion parameters configurable.

### Security
- Throw an exception for words with non-BMP Unicode characters to prevent losing the suggestmgr state.

### Multithreading
- Make the library multithreaded by synchronizing the affix manager.
