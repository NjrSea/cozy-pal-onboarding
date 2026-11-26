# Troubleshoot Cozy Deck and Cozy Pal

Start with the exact screen, error text, and when it happened (include the time zone). Record the Cozy Deck, Cozy Pal, and Paseo versions and which Mac the iPhone selected. Use details already available. If the Mac is accessible, inspect it locally. Otherwise give the user the matching UI steps and ask for the smallest useful excerpt.

Check four facts in order: the QR was scanned, the phone and Mac established a pairing, the selected Mac is reachable now, and its Paseo project list can be read. Each fact needs its own evidence.

## Pairing fails or times out

| What the user sees | Check and next step |
| --- | --- |
| Cozy Pal shows no QR | Open **Pairing** on the target Mac. Check for a registration or startup error and confirm the Mac is awake, Cozy Pal is running, and the Mac has internet access. In Pal logs, look for `relayEnroll` and its outcome. If registration failed, follow the exact error before trying another scan. |
| The scanner won't open or read the code | Check iOS camera permission for Cozy Deck. Use **Add Host** in Cozy Deck and scan the live QR in **Cozy Pal**. If the code expired or was already used, refresh it in Pal and scan the new one while it is valid. |
| The code scans but pairing waits, fails, or times out | Check both devices' internet access and keep the Mac awake with Pal open. Look in Pal **Devices** and in Cozy Deck's hosts before retrying. If pairing did not finish, retry once with a fresh QR. For recurring failures, collect the evidence below for one timed attempt. If possible, compare a retry on a second ordinary network to help isolate network filtering. |
| The device was paired but the host is offline | Verify that this is the same Mac and check whether Pal is still running and the Mac is awake. Check Pal **Devices** and its relay events. If Cozy Deck says the pairing expired or was revoked, use a fresh QR from that Mac. Preserve the existing pairing while investigating an ordinary disconnect. |

Cozy Pal QR pairing can work over the internet. If the user chose **manual Paseo LAN connection**, check that the phone can reach the Mac on that network, that Cozy Deck has Local Network permission, and that the address points to the target Mac's Paseo service. Check the path the user actually chose before changing network settings.

For repeated timeouts, line up the phone and Mac evidence for the same attempt. `relayConnectFailed` on Pal indicates its relay connection failed. `helloRejected` with `pairingWindowClosed` or `nonceMismatch` points to the QR handshake; refresh the code once and record recurring rejections. On the phone, `relay.connect.helloSent` followed by `relay.connect.timeout` without `relay.connect.helloAckReceived` means the phone did not confirm the handshake. Compare that with Pal's `sessionEstablished` or rejection for the same attempt. A timeout alone can't identify which side failed. Keep keychain entries, device records, and connector identity intact during diagnosis.

## Paired, but can't see or add a project

1. In Cozy Deck, first confirm the intended Mac is reachable. In Cozy Pal on that Mac, check that Paseo is detected and enabled. Open Paseo and select that Mac's local host. A working provider or project on another Paseo host won't confirm availability on this Mac.
2. In Cozy Deck, open **New thread** and its project selector. Home groups conversations, so an empty Home can also mean the project has no visible sessions. The selector tells you what can be used for a new thread. Distinguish a successful empty list from a loading or request error.
3. If the list is empty, check the project in Paseo on the Mac. If the CLI is already installed, `paseo project ls` is a read-only check. Confirm that it targets the same daemon as Cozy Pal. Use either `--host` or `--home` for an explicit target when supported by the installed CLI.
4. If the project is missing, use project selection or directory creation in Cozy Deck or Cozy Pal on the intended Mac. Confirm the chosen location and name, then verify the project appears. If that action is missing or fails, check the installed app version and capture the exact error. Paseo registration is a fallback: reuse an existing folder or use `paseo project create "<absolute-project-path>"` with the user's chosen existing folder, then confirm with `paseo project ls`. This command registers an existing folder; it does not replace the app's directory creation flow. Check the target daemon and directory access. Recheck the result before retrying an unclear creation outcome.
5. Once Paseo lists the project on the correct Mac, return to Cozy Deck and reopen the project picker or use its **Retry** action if offered. If the phone still reports a load error or omits the project, compare the selected host, Pal's Paseo connection, and the phone's `threadCatalog` request and reply if logs are available. Capture the request outcome and tell the user which layer you've verified.
6. If the project is visible but a new thread cannot start, check the selected provider and model in Paseo's **Settings → local host → Providers → Diagnostic**. For a missing model or failed provider check, resolve that provider's install, sign-in, or model access first. If the phone is still processing a create request, check whether the thread already exists before sending again.

See [Paseo CLI projects](https://paseo.sh/docs/cli#projects) and [Paseo troubleshooting](https://paseo.sh/docs/troubleshooting).

## Collect and interpret logs

- **Cozy Pal (Mac):** Open **Advanced Settings → Diagnostics → Export Diagnostics Package**. If the installed version shows **Copy Diagnostics**, a short summary can be enough. With local access, read the relevant `deck-*.log.jsonl` files in `~/Library/Logs/CozyDeck/` or the package's `logs/` and `manifest.json`. Look at the affected time window and focus on relay registration, connection and handshake events, or Paseo bridge events for that attempt.
- **Cozy Deck (iPhone):** Use the app's diagnostics export if the build exposes it. With public builds, an exact error or a screenshot with sensitive details hidden, together with the time and host status, is enough to start. TestFlight users can send feedback from TestFlight. See [Cozy Deck support](https://sundaydesk.app/products/cozy-deck/support/).
- **Paseo (Mac):** For daemon or project problems, check `~/.paseo/daemon.log` (or `daemon.log` in the configured Paseo home). For desktop launch or provider discovery problems, check `~/Library/Logs/Paseo/main.log` and the provider's **Diagnostic** in Paseo. Use the [Paseo log guide](https://paseo.sh/docs/troubleshooting#reading-the-logs) to distinguish the two files.
- Match events by the time window and, when available, the same attempt or request ID. Summarize the last confirmed step, the first failure with its code, and the next check. If the evidence runs out at a timeout or an empty view, report the missing evidence and leave the cause unconfirmed.
- Before asking the user to share anything, narrow it to the relevant window and remove credentials, QR or pairing links, private hostnames and paths, identifiers, and conversation content. Let the user review any excerpt or bundle before sharing it with support.
