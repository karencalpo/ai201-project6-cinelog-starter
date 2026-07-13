# PR Response Doc — CineLog Watchlist Feature

## AI Usage


## Comment 1 — Rename
**What I did:**
Renamed `save_to_watchlist()` to `add_to_watchlist()` in services/watchlist_service.py to follow the project's verb_to_noun naming convention and maintain consistency with `add_to_collection()` in collection_service.py.

**How I verified:**
I searched the codebase to identify all call sites and references. The function was only referenced in the initial test file, so I updated the import statement there. I verified the rename was complete by checking that no references to the old name remained in the codebase. I used VS Code's search function to check if any of the old references to the old function name remained.

## Comment 2 — Deduplication
**What I did:**
Added deduplication logic to `add_to_watchlist()` (lines 34-40 of watchlist_service.py) that queries the WatchlistEntry table for an existing entry matching the user_id and film_id. If a duplicate is found, the function raises `AlreadyInWatchlistError` to prevent duplicate watchlist entries.

**How I verified:**
I modeled the implementation after the identical deduplication pattern in `add_to_collection()` (collection_service.py:47-53), using the same query structure and exception-raising approach. I compared the logic side-by-side to ensure it matches.

## Comment 3 — Missing test
**What I did:**
Created tests/test_watchlist.py with a test `test_add_to_watchlist_nonexistent_film_raises()` that verifies the function correctly raises `FilmNotFoundError` when attempting to add a film that doesn't exist in the database.

**How I verified:**
I used tests/test_collection.py as my model, following its fixture structure (app, sample_user, sample_film) and test patterns (lines 98-107 show the nonexistent film test case). I modeled the watchlist test on the same pattern: create an app context, use a fake film ID, and verify the correct exception is raised via `pytest.raises()`. 

## Comment 4 — Default visibility
**My position:**
I think that default visiblity should not be `public=True`. I have changed it to `public=False`

**Reasoning:**
The default visibility should not be public, in my opinion. I think that the default should be private, or `public=False`. I think that users should have the option to opt in to making their watchlist public. It should not be a default. That way, users are not automatically opted into a watchlist that everyone could see if that was not their choice in the first place.

**Tradeoff acknowledged:**
I acknowledge that changing the default to `public=False` could have some side effects here. This may require updating any code that assumes watchlists are public by default, but the privacy benefit justifies the change.

## Comment 5 — Sort order
**My position:** 
I agree with defaulting watchlists to date added order rather than alphabetical.

**Reasoning:**
I agree with the reviewer that users would like to see what they added recently first. In this case, ordering titles in alphabetical order does not make as much sense.

**Engagement with reviewer's point:**
The reviewer makes a good point that users would like to look at their most recently title added to their watchlist rather than look at their watchlist in alphabetical order. This makes it easier for them to reference the title they want to see more recently than needing to search through an alphabetical list for the movie(s) they added recently. The code is updated to reflect this.

## Comment 6 — Rebase
**What conflicted:**
I discovered that models.py had a UUID inconsistency: WatchlistEntry.film_id was defined as db.Integer instead of db.String(36), which doesn't match the UUID system used throughout the codebase.

**How I resolved it:**
I updated line 80 of models.py to change:
film_id = db.Column(db.Integer, db.ForeignKey("film.id"), nullable=False)

to:
film_id = db.Column(db.String(36), db.ForeignKey("film.id"), nullable=False)

**How I verified no conflict remains:**
I reviewed models.py to confirm all film_id references now use String(36) consistently. The working tree is clean and the fix is committed.

## AI Usage
Comment 4 — Default visibility:
Asked AI to evaluate whether my privacy-first reasoning was sound and whether I was clearly proposing a code change vs. just documenting a decision. AI confirmed the reasoning was solid (privacy-by-default is a best practice) but pointed out I needed to be explicit about whether I was proposing a change to public=False. Based on that feedback, I rewrote my position to clearly state "I have changed it to public=False" and strengthened the tradeoff acknowledgment to address the potential code impact.

Comment 5 — Sort order:
Asked AI to review my reasoning against the maintainer's message and identify any gaps. AI confirmed the reasoning aligned well but noted the response was repetitive across the three sections. Based on that feedback, I tightened the language a little to remove duplication while keeping the core points about UX and mental models.

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->