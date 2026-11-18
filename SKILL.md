---
name: cozy-pal-onboarding
description: Set up Cozy Pal on a Mac from scratch or resume an existing setup. Check the official download options for Cozy Deck and Cozy Pal, connect the local Paseo daemon, verify one coding provider, and register a project the user selects. Use for first-time Cozy Pal setup and provider or project setup on that same Mac.
---

# Set up Cozy Pal

Run local checks on the Mac that will run the user's coding agents. A cloud terminal, a different computer, or an SSH session to another host cannot prove local installation. If you lack access to the target Mac, give the user the next concrete action and ask what they see. Use their language.

## Onboarding flow

Start at the first unfinished step. Tool setup and iPhone pairing can proceed in either order.

```mermaid
flowchart TD
    Start["Check current download options on the Cozy Deck website"] --> Pal{"Cozy Pal installed on the target Mac?"}
    Pal -->|No| DMG["Download the official DMG directly and install Cozy Pal"]
    Pal -->|Yes| Open["Open Cozy Pal"]
    DMG --> Open

    Open --> Service{"Local Paseo service detected?"}
    Service -->|No| Paseo{"Paseo desktop app installed?"}
    Paseo -->|No| Install["Download and install Paseo"]
    Install --> OpenPaseo["Open Paseo and check again"]
    Paseo -->|Yes| OpenPaseo
    OpenPaseo --> Service
    Service -->|Yes| Provider{"At least one selected provider enabled and ready?"}
    Provider -->|No| Configure["Choose a provider, install or enable its CLI, and sign in if needed"]
    Configure --> Provider
    Provider -->|Yes| Project["Select or register one existing project folder"]
    Project --> Models{"Selectable models available for this provider and project?"}
    Models -->|No| Fix["Check the provider account, CLI, and model access in Paseo"]
    Fix --> Models
    Models -->|Yes| Select["Confirm the provider's default or choose a model"]
    Provider -->|Lookup failed| Retry["Retry the local check. Keep setup incomplete if it still fails"]
    Models -->|Lookup failed| Retry

    Open --> Phone{"iPhone already paired?"}
    Phone -->|No| Deck{"Cozy Deck installed on the iPhone?"}
    Deck -->|Yes| Scan["Scan the Cozy Pal code in Cozy Deck, or pair later"]
    Deck -->|No| Channel{"iPhone download option confirmed?"}
    Channel -->|App Store| Store["Open the official App Store listing"]
    Channel -->|TestFlight| Invite["Open the current TestFlight invite"]
    Channel -->|Unavailable or unknown| Check
    Store --> Scan
    Invite --> Scan
    Phone -->|Yes| Paired["Reuse the existing pairing"]
    Scan --> Check
    Paired --> Check
    Select --> Check{"Local setup complete and iPhone paired?"}
    Check -->|Yes| Verify["Verify project, provider, and model in Cozy Deck"]
    Check -->|No| Pending["Show the unfinished step and resume from there"]
```

## Check current download options

Open the [official Cozy Deck product page](https://sundaydesk.app/cozy-deck/) at the start of onboarding. Read the links and their surrounding labels, including the setup section. If an HTTP fetch cannot read the page, try opening it in a browser. Check the two apps separately.

- **Cozy Deck for iPhone.** If the website links to the public Cozy Deck listing on `apps.apple.com`, confirm the Apple page is for Cozy Deck and give the user that link to open on their iPhone. When the website offers both App Store and TestFlight, use the App Store for normal setup and TestFlight when the user wants the beta. If the website offers TestFlight, read the current `testflight.apple.com/join/…` link directly from the website, confirm the Apple invite is for Cozy Deck, then share it and explain how to join and install with TestFlight. If Apple says the beta is full, closed, or unavailable, report that state and use a verified App Store link if one exists.
- **Cozy Pal for Mac.** Read the current DMG link, version if shown, and supported macOS from the product page or its [Cozy Pal setup guide](https://sundaydesk.app/products/cozy-deck/setup/). Use the current official DMG URL directly when installation is needed. Cozy Pal's availability is independent of the iPhone release channel.
- If you cannot verify an iPhone download option, share the official product page and mark the iPhone install as pending. Continue with Mac setup. Take Apple links from the current website and avoid inferring a URL from a cached invite code, an App Store search result, or a release announcement alone.

## 1. Check and install Cozy Pal

- Check the target Mac against the system requirements on the official website before installing. If the requirements cannot be verified, the bundled DMG below is for Apple silicon and macOS 14 or later.
- Look for Cozy Pal in Applications, including the user's Applications folder. Reuse an existing working installation and its pairing identity. If it is absent, download the current official DMG directly. If the official website cannot be read, the [bundled Cozy Pal 1.0.0 DMG link](https://sundaydesk.app/downloads/cozy-pal/CozyPal-1.0.0.dmg) is a fallback. Confirm that it still serves the DMG and verify the app before running it. Mount the DMG with Finder, copy **Cozy Pal.app** to Applications, eject the image, and open the installed app. If acting with terminal tools, verify the downloaded app with the system's code-signing and Gatekeeper checks before running it. Hand macOS security prompts to the user. If the download is unavailable or verification fails, report the failure and the official source instead of substituting a mirror.
- Open First-Time Setup from Cozy Pal if needed. Earlier releases offer Pairing instead. Verify that the installed app opens.

## 2. Detect or install Paseo

- Check both the installed desktop app and whether Cozy Pal detects the local Paseo service. An installed app may be closed, and an absent `paseo` shell command does not imply desktop Paseo is missing.
- Open an existing Paseo installation or use the [official Paseo download](https://paseo.sh/download). The desktop app starts its own daemon. Follow the [Paseo getting-started guide](https://paseo.sh/docs). Avoid starting a second daemon for a working desktop installation.
- Return to Cozy Pal and confirm Paseo is detected. If necessary, use its tool detection action. If detection still fails, check that Paseo has opened its local host and that both apps are on the same Mac.

## 3. Make one provider usable

- Reuse the user's preferred installed coding tool. If they have not chosen one, ask which existing account or provider they want to use. Paseo's built-in adapters still require the underlying agent CLI and its authentication. Follow [supported providers](https://paseo.sh/docs/supported-providers) and the selected vendor's installation and login guidance. If the account needs login, let the user complete it in the vendor's UI or CLI. Keep passwords, API keys, and tokens out of the prompt, transcript, and logs.
- Use the selected provider's Diagnostic in Paseo's local host settings. If the matching CLI is available, use the installed version's help and the documented `paseo provider diagnostic <provider-id>` command against the same local daemon. Confirm Paseo itself sees the provider and its models. If the tool works in the terminal but not in Paseo, use the daemon's diagnostic to check its command and environment.
- When Paseo is running but no provider is ready, go to Settings → local host → Providers. Help the user choose one provider and fix the specific issue reported by Diagnostic, then check again. A failed lookup leaves readiness unconfirmed.
- Keep working provider settings. Use [custom provider configuration](https://paseo.sh/docs/custom-providers) only when the user needs a custom endpoint, model, or executable. Apply the smallest change to the chosen provider.

## 4. Register one existing project

- Ask for the exact local project folder if the user has not identified one. Resolve the directory on the Mac running Paseo. Do not pick a repository solely from the current terminal directory or enroll every folder.
- Check Paseo's existing projects first and reuse the matching folder. Add the folder in Paseo on the local host or, if the installed CLI supports the documented syntax, use `paseo project create "<absolute-project-path>"` followed by `paseo project ls`. Verify the CLI targets the same local daemon Cozy Pal detected. The [Paseo CLI reference](https://paseo.sh/docs/cli) and [workspace guide](https://paseo.sh/docs/workspaces) cover version-specific commands and the distinction between projects and workspaces.
- Verify the project appears on that host. In the project's new-session setup, check that the provider offers at least one selectable model. Confirm the default if one is offered, or ask the user to choose a model. If the list is empty, return to provider diagnostics and check model access. A test run in the repository is optional and should be scoped to what the user requested.

## 5. Hand off to iPhone

If the iPhone is already paired, reuse the pairing. If Cozy Deck is missing on the iPhone, give the user the verified App Store listing or TestFlight invite from the official website. Once Cozy Deck is installed, guide the user to open it, choose Add Host, and scan the code in **Cozy Pal**. The user can pair before tool setup is complete or come back to pairing later. Keep the live code on the Mac. If the phone is available, verify the selected project, provider, and model in Cozy Deck's new-session flow. Confirm that pairing, provider readiness, and project visibility each have their own evidence. If interrupted, relay the remaining action and resume from the observed state.
