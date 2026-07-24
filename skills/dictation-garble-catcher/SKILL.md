---
name: "Dictation Garble Catcher"
description: "Companion to braindump/superbraindump. Flags a word or phrase in a dictated prompt that doesn't fit context and is phonetically close to a known proper noun from project context, and confirms the intended term before acting on it, instead of silently running with the literal transcription or silently 'correcting' it without asking. Use whenever a raw dump reads as voice-dictated (run-on phrasing, odd word choices that don't fit meaning) and contains a term that looks like it could be a mishear."
---

# Dictation Garble Catcher

Voice dictation occasionally substitutes a phonetically similar word for the intended one, especially for proper nouns and compound terms it hasn't seen before, an unrelated-sounding word standing in for a business or product name (e.g. a leisure activity transcribed in place of a store name that sounds similar), or a plain-English phrase standing in for a compound term (e.g. "the called skill floor" for "skill-forge").

## How to spot a likely garble

A word or phrase is a dictation-garble candidate when **both** are true:
1. It doesn't fit the sentence's meaning as literally written (a word describing something with zero connection to the conversation's actual topic).
2. It's phonetically close to a term already established in project context (a business name, a repo name, a product name from NeuralVault/CLAUDE.md/recent conversation) that *would* fit the sentence.

## Procedure

1. **Before acting on a raw dump**, scan for terms that don't semantically fit their sentence.
2. **For each candidate, check phonetic similarity** against known proper nouns already established in this project's context (business names, product names, repo names, people's names already mentioned).
3. **If a plausible match is found**, state the likely correction and proceed on that basis, flagged clearly rather than silently substituted. For example: "(reading '[transcribed word]' as '[likely intended term]'. Let me know if that's wrong)." That way a genuine unusual request isn't accidentally overridden.
4. **If no confident match is found** but the term still doesn't fit, ask rather than guess. Don't force a correction onto a term that's simply unfamiliar rather than garbled.
5. **Never silently rewrite the term with no acknowledgment**: the user should always be able to see and correct the substitution if it's wrong, same as any other assumption surfaced during a braindump refinement pass.

## Relationship to braindump

This isn't a replacement for `braindump`'s own assumption-flagging (Step 3 of that skill). It's a narrower, earlier pass specifically for word-level transcription errors, which are a different failure mode than genuine ambiguity in what the user wants. Run this check as part of the same read-through, not as a separate turn.
