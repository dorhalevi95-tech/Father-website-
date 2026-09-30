# Therapist Diary (יומן טיפולים מאובטח)

A single-file, offline, encrypted session-records tool for a psychologist in private practice in Israel.
The UI is in Hebrew. Nothing is sent over the network, so no server, account or cloud is involved.

## How to use it

1. Download `index.html` to the clinic computer (for example to `Documents\Therapist Diary\`).
2. Open it in Chrome or Edge. Don't use incognito mode, because incognito deletes the data when the window closes.
3. Always open the **same file from the same place in the same browser**. The encrypted data is stored in
   that browser's storage for that file location.
4. Choose a long password and write the recovery key on paper. Keep the paper somewhere locked.
5. Export an encrypted backup to a dedicated USB stick every week (Settings → ייצוא גיבוי מוצפן).

## What the law requires and how the tool covers it

| Requirement | Source | In the tool |
|---|---|---|
| Record must hold identifying details of the patient **and** the therapist, the treatment given, medical history as reported, current-state diagnosis, and treatment instructions | Patient Rights Law 1996, §17(a) | Intake card and per-session fields. The therapist name and license number go into every signature. A session that took place can't be signed while any required field is empty |
| Therapist's personal notes are **not** part of the medical record | §17(a) | A separate purple "personal note" field, left out of the printed record |
| Keep the record current and preserve it | §17(b) | Drafts can't be forgotten: an unsigned-drafts reminder is shown until they're signed. Signed entries are locked |
| Corrections must not erase the original | MoH DG circular 09/2019 (record-management standards) | Signed entries are immutable. Corrections are dated addenda that record the reason and the author. Intake edits keep a change history |
| Patient's right to a copy of the record | §18 | "Print record / PDF" prints signed entries and addenda only. It leaves out drafts and personal notes |
| Confidentiality | §19–20; Psychologists Law 1977 §7 | Encryption, auto-lock, and consent/waiver fields on the intake card |
| Retention: ≥7 years from last entry; minors ≥ until age 25 | Israel Psychological Association ethics code (2017); 7-year limitation period | A "keep until" date on every file. Deleting a file is blocked until that date has passed |
| Data security for sensitive data (single-user database track) | Privacy Protection Law 1981 incl. Amendment 13 (2025); Data Security Regulations 2017 | See below. Also a security-incident log and a printable "database definitions document" |

This is a working summary, not legal advice. Have it checked by the Psychological Association or a lawyer.

## Security model

- **Encryption at rest:** all data is encrypted with AES-256-GCM using a random data key. That key is wrapped
  twice. One wrap uses a key derived from the password (PBKDF2-SHA256, 600,000 iterations, random salt). The
  other uses the recovery key (125-bit random). Plaintext is never written to disk.
- **No network:** a Content-Security-Policy with `connect-src 'none'` makes the browser refuse any
  network request. The file loads no external scripts, fonts or images, so there is no third-party code
  in the page.
- **No HTML injection:** user text is only ever inserted with `textContent`, never with `innerHTML`.
- **Auto-lock** after inactivity (default 10 minutes). Locking reloads the page, which clears all
  decrypted data from memory. A draft that's open when the diary auto-locks is saved first.
- **One tab at a time:** opening the diary in a second tab locks the first one.
- **Failed-login counter:** failed login attempts are counted and shown after the next successful login.
- **Audit log** of opens, creations, signatures, corrections, prints and backups. The log contains no
  clinical content.
- **Backups** are the same encrypted blob, and restoring one requires that backup's password.

### What the tool cannot protect against (the user's responsibility)

- Malware or a malicious **browser extension** on the computer can read whatever is on screen. Use a
  dedicated browser profile with no extensions, keep the OS updated, and run antivirus.
- An unlocked, unattended computer. Lock the screen (Win+L) and keep the auto-lock setting short.
- Weak passwords. Use a long passphrase.
- Losing both the password and the recovery key means the data is unrecoverable, by design.
- Clearing browser data or replacing the computer **without a backup** means the data is gone.
- A printed/PDF copy is **not** encrypted. Hand it to the patient directly and delete the file afterwards.
- Turn on full-disk encryption (BitLocker / FileVault) and disable Chrome's "Enhanced spell check"
  (spellcheck is already switched off in the tool's fields).
