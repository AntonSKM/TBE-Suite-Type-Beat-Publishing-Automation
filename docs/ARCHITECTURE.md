# Architecture Overview

This is a deliberately high-level architecture document for the public portfolio repository. Production implementation details are kept private.

## Subsystems

### React application

The frontend owns the interactive workflow: account/channel selection, readiness state, rendering controls, metadata editing, upload scheduling, calendar visualization and operational feedback.

### FastAPI application layer

The API coordinates authenticated users, channels, persistent state and job dispatch. It also handles Google OAuth callbacks and the YouTube integration boundary.

### Persistent state

Relational persistence stores user/account/channel ownership, background-job state, upload state, YouTube snapshots, reusable configurations and integration metadata. Local media is isolated by user/channel rather than placed in one global media directory.

### Background workers

Celery workers execute work that should not block web requests:

- FFmpeg rendering;
- thumbnail acquisition;
- BeatStars automation;
- YouTube upload work.

Redis acts as the broker/state coordination layer for background processing.

### External integrations

- Google OAuth 2.0
- YouTube Data API
- BeatStars browser workflow
- yt-dlp/YouTube thumbnail acquisition

## Data-flow principle

Interactive requests create or modify durable intent; workers execute expensive work; the UI polls/refreshes durable state. This keeps navigation independent of long-running processes.

## Reliability principle

External services are assumed to be incomplete or temporarily unavailable. The system therefore prefers conservative reconciliation, resumable work and explicit failure state over destructive assumptions.

## IP boundary

The public repository intentionally omits:

- source for the OAuth-client pool;
- exact YouTube reconciliation logic;
- BeatStars selectors/parsing rules;
- worker orchestration implementation;
- internal persistence/migration details;
- credentials and deployment configuration.

These components can be discussed privately in interviews while keeping the commercial implementation protected.
