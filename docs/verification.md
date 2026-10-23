# Checks and unfinished work

[Back to the case study](../README.md)

## Focused tests

I reran the source repository's Node tests for connection codes, request-list behaviour and the activity report before publishing this case. All 12 passed.

| Area | Assertions covered |
|---|---|
| MAX codes | Code normalization and command parsing; invalid-input rejection; random code format and hash behaviour. |
| Direct invoices | Approved direct invoices leave working lists; ordinary approved requests remain actionable for supply; recalling a direct invoice puts it back in the list. |
| Activity report | Moscow calendar boundaries; invalid filters and bounded date ranges; pagination/filter preservation; spreadsheet-formula neutralization; request-event details. |

The production repository also contains SQL regression scripts for direct invoices, MAX pairing and activity reporting, plus a versioned-backup integration test. I do not count the presence of those files as proof that every test was rerun for this publication.

## Recovery evidence

The backup integration test was run during implementation and verified historical-file recovery, preservation of extended attributes, successful-snapshot copying and exclusion of unpromoted snapshots. A separate recovery rehearsal restored an actual cloud snapshot into a temporary directory and test database.

I have not included the recovered production data, SQL dumps, credentials, staff names, invoice scans or private infrastructure configuration in this repository.

## Walkthrough evidence

The 2:42 walkthrough is a visual explanation of the implemented workflow and selected internals. Its data and interface reconstruction are illustrative. It is not presented as a live recording or an independently verified performance benchmark.

The video has no narration. Its English captions are provided both in the picture and as SRT/WebVTT, with at most two lines per cue. The music is an original quiet instrumental bed; there is no third-party track dependency.

## Limits worth keeping explicit

- **Test breadth:** focused regression tests are not a full browser end-to-end suite, security assessment or load test.
- **Notification reliability:** delivery is best-effort. There is no durable notification outbox or guarantee that a recipient has read a message.
- **Offline use:** the PWA offers an offline fallback, not offline editing and synchronization.
- **Transactions:** finance approval is atomic. Other workflows may use multiple writes and compensating cleanup; they should not all be described as atomic.
- **Materials:** movement history is used to calculate balances. I have not published capacity measurements for much larger histories.
- **Backup consistency:** the database and live files are captured separately. A restore test does not remove the need for a controlled cutover during migration.
- **Business impact:** I have not measured an attributable ROI or a percentage improvement in administrative time.

The next testing improvement I'd prioritise is a browser-level regression for the normal request route, including a failed finance selection and the two completion states.
