# Engineering Highlights

This document focuses on the engineering problems behind TBE Suite without publishing the production implementation.

## 1. Turning an operational workflow into a state machine

The original process crossed multiple tools: local beats, artwork, BeatStars, metadata, YouTube Studio and a calendar. TBE models that sequence as a guided channel workflow with explicit readiness state instead of relying on the user to remember which step comes next.

The **Prepare Channel** UI exposes six stages: assets, thumbnails, BeatStars, configuration, rendering and upload. Each stage can be skipped when already satisfied, which makes the workflow usable both for first-time setup and for repeated channel runs.

## 2. Long-running work outside HTTP requests

FFmpeg, browser automation and large uploads can take seconds or minutes and can fail independently of the web request that started them. TBE therefore dispatches work to background workers rather than keeping FastAPI requests open.

Design goals included:

- persistent progress;
- cooperative cancellation;
- recovery after navigation/reload;
- error reporting that survives the worker process;
- avoiding blocked web-server threads.

## 3. High-throughput media generation

The production pipeline renders a static/animated visual layer together with beat audio into 1080p video using FFmpeg. On the current development setup, recent sequential renders completed in roughly 0.84–1.00 seconds each, with an observed average around 0.89 seconds in one run.

The application also tracks previous render settings and exposes a variation planner so repeated batches can vary visual treatment intentionally.

## 4. External API state is not automatically authoritative

One of the more interesting YouTube problems was scheduled/private-video discovery. Different API views can expose different subsets of owner-visible videos, and an incomplete response must not silently create holes in a release calendar.

The system therefore keeps durable YouTube video state, hydrates discovered IDs, distinguishes published/scheduled states and reconciles conservatively. Missing data is treated as uncertainty rather than immediate deletion.

## 5. OAuth identity safety

A successful Google OAuth callback is not enough on its own: the authenticated identity must correspond to the intended YouTube channel. TBE verifies the authorized channel before persisting the authorization for the operation and provides a clean retry path if the wrong account was selected.

The UI also restores the original product context after OAuth instead of dropping the user on a raw callback page.

## 6. Internal capacity orchestration vs. provider limits

The application has internal orchestration around OAuth client capacity for development/operations. That internal scheduler is intentionally separated from external Google/YouTube quota errors. Provider rate limits are handled as provider errors; they are not used as a trigger to rotate clients in an attempt to bypass an external limit.

## 7. Metadata at scale

The upload configuration editor treats title, description and tags as reusable templates, supports placeholders and validates YouTube limits. Find & Replace works across all three fields simultaneously, which makes large metadata convention changes practical.

Historical templates can also be inferred from channel history, but weak coincidences are filtered out: a recurring pattern needs repeated evidence before being promoted to a reusable config.

## 8. Multi-user storage and ownership

The system evolved from a single-user local tool into a multi-user application. Storage and database ownership were refactored so channels and media belong to a user context rather than a global `channels` folder. This prevents accidental cross-user file access and keeps local media organization aligned with database ownership.

## 9. Browser automation as a bounded subsystem

BeatStars integration is treated as a fallible external subsystem. Scraping is bounded, progress is surfaced, results are cached, and partial failure does not corrupt existing link state. The public showcase intentionally does not expose the production selectors or parsing rules.

## 10. Product engineering, not just backend engineering

A large part of the project was translating operational knowledge into UI:

- command-center dashboard;
- channel readiness indicators;
- guided workflow progression;
- live thumbnail preview;
- asset management;
- render-history visibility;
- release calendar;
- OAuth error/retry UX;
- metadata validation and bulk editing.

The goal was always that the software should reduce cognitive load, not merely expose backend endpoints.
