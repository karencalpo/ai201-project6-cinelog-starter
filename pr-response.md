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
**Reasoning:**
**Tradeoff acknowledged:**

## Comment 5 — Sort order
**My position:**
**Reasoning:**
**Engagement with reviewer's point:**

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->