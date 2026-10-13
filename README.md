# Genie Web – Maestro smoke tests

Small UI automation suite for **chatbotai.com** (Genie web) written with [Maestro](https://maestro.dev) YAML flows.
Part of the Genie QA case study.

## What is covered

| Flow | Scenario | Checks |
|---|---|---|
| `01_home_page_loads.yaml` | Smoke | Home page loads: welcome text, chat input, model selector, Login, tool buttons |
| `02_empty_message_validation.yaml` | W-06 (negative) | Enter in an empty composer does not start a conversation |
| `03_guest_message_requires_login.yaml` | W-01 (main flow) | Guest types a prompt and sends it → login dialog appears |
| `04_youtube_summarizer_from_home.yaml` | Navigation | YouTube Summarizer opens from the tool row |
| `05_login_options.yaml` | W-02 | Login dialog offers Google, Facebook, Apple, Microsoft and e-mail |
| `06_sidebar_navigation.yaml` | Navigation | Image ("What would you like to create?"), Video and Apps open; New Chat returns to chat home |
| `07_tool_pages_open.yaml` | Navigation | Chat PDF and Math Solver pages load by URL |

Shared steps in `common/`: `open_home.yaml` (open site, wait for the chat input) and `accept_cookies.yaml` (close the cookie banner – every run starts with a clean browser).
`tools/` contains helper flows used while writing the suite to dump the page hierarchy; they are not part of the run.

**Selector strategy:** elements are located by their stable `id` attributes (e.g. `chat-input-textarea`, `chat-header-login-button`) rather than by visible copy, so tests survive text and layout changes. Text assertions are used only where the text itself is the expected result.

## Why web and why these flows

- Official Maestro runs iOS tests on the **Simulator only**; the production Genie app is an App Store build and cannot be installed on a simulator. With a simulator build the same approach would apply to iOS.
- Sending a chat message on web requires a logged-in account. Login is Google / Apple OAuth or a passwordless e-mail magic link, none of which can be completed by a UI test without a test login endpoint or an auth token. The suite therefore covers the guest-facing flows. With a test login (e.g. a session token or test-only password login) the next flow would be: ask "What is the capital of France? Answer in one word." → assert the answer contains "Paris" (keyword assertion, because answers are non-deterministic) → start a new chat.
- Chat answers are non-deterministic, so assertions check UI states, not answer text.

## Requirements

- macOS, Java 17+
- Google Chrome
- Maestro CLI (web support is in beta, Chromium only)

## Run

```bash
# install Maestro
curl -Ls "https://get.maestro.mobile.dev" | bash

# run the whole suite (config.yaml selects flows/)
maestro test .

# run one flow
maestro test flows/01_home_page_loads.yaml

# record a single flow as MP4
maestro record flows/03_guest_message_requires_login.yaml
```

Screenshots are written to `screenshots/`.

## Results

Last run: 13 Oct 2026 – Maestro 2.11.0, macOS, Chromium – **7/7 passed**. Demo video: https://drive.google.com/drive/folders/1bDfHbEWPSddSr6QBnheT1pAlByhthW4S (Automation folder)
