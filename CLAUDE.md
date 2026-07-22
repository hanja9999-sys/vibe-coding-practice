# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This is a personal "vibe coding" practice folder (바이브코딩연습). It is not a git repository and has no build tooling, package manager, or test framework. Each deliverable is a single, self-contained `.html` file meant to be opened directly by double-clicking on a Windows PC — no internet connection, no external libraries/CDNs, no server.

## Working conventions for this repo

- **One file per app.** Everything (HTML structure, `<style>`, `<script>`) lives in a single `.html` file. Do not split into separate `.css`/`.js` files or introduce a bundler/build step — the whole point is a file that works standalone when double-clicked.
- **Zero external dependencies.** No CDN scripts/fonts, no npm packages, no network calls. Only inline CSS/JS and fonts already installed on Windows (e.g. `Batang`, `궁서/Gungsuh`, `Malgun Gothic`) may be used.
- **Korean-first UI.** User-facing text, labels, and error/status messages are written in Korean and should stay that way unless the user asks otherwise.
- **Mobile-friendly by default.** Layouts use responsive units/flex so pages remain usable on a phone-sized viewport, even though the primary target is a desktop double-click launch.
- **Persistence via `localStorage` only**, wrapped in `try/catch` since some `file://` contexts restrict storage access.
- **Filenames may contain Korean text and parenthetical suffixes** (e.g. `랜덤_발표자_뽑기(실습_20260722).html`) — treat the existing filename as significant and don't rename files casually.

## Verifying changes

There is no test suite or lint config. To verify a change:
1. Open the `.html` file directly (e.g. via the Browser pane / `file://` URL, or by double-clicking in Explorer).
2. Exercise the actual UI flow in-browser (click buttons, type input, reload to check persistence) rather than relying on static review — these are UI-only apps, so the only meaningful verification is running them.

## Architecture notes (per-file pattern)

Each app file follows the same internal shape, illustrated by `랜덤_발표자_뽑기(실습_20260722).html` (random presenter picker):

- `<style>` block defines a small set of CSS custom properties (`:root { --... }`) for the color/theme palette, then component styles below. When asked to reskin/retheme an app, change the `:root` variables and related rules rather than hardcoding new colors throughout.
- All JS is wrapped in a single IIFE `(function(){ "use strict"; ... })()` at the bottom of the file — no globals, no modules.
- State is a plain object (e.g. `state = { drawnThisRound: [], lastDrawn: null }`) synced to `localStorage` via explicit `save*ToStorage()` / `loadFromStorage()` functions, called after every mutation.
- UI updates go through small, targeted render functions (e.g. `renderProgress()`) called after state changes, rather than a reactive framework — plain DOM manipulation via `getElementById`/`textContent`/`classList`.
- Business logic that has non-obvious rules (e.g. the "no immediate repeat" draw logic, "round resets when everyone has been picked") lives in dedicated functions (`pickCandidate()`, `pruneDrawnAgainstCurrentNames()`) — read these fully before changing draw/selection behavior, since the rules were arrived at through explicit back-and-forth with the user and aren't just "pick a random name."
