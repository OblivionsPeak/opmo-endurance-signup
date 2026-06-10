# Firebase Setup — TP Auth & Security Rules

The Team Principal login now uses **Firebase Authentication** instead of a
password hash in the page source, and Firestore **security rules** enforce
who can write what. Until you complete the two steps below, **TP login will
not work** and the database remains writable by anyone.

## 1. Create the TP account (~2 minutes)

1. Open the [Firebase console](https://console.firebase.google.com/) →
   project **operation-motorsport-scoring**
2. **Authentication → Sign-in method** → enable **Email/Password**
3. **Authentication → Users → Add user**
   - Email: `tp@opmo-endurance.app` (must match `TP_EMAIL` in `index.html`)
   - Password: choose a strong one — this is the new TP password
4. That's it — the TP login modal in the app signs in with this account.
   To change the TP password later, reset it from the Users tab.

> The email doesn't need to be a real mailbox; it's just the account ID.
> If you'd rather use a real address, change `TP_EMAIL` in `index.html` to match.

## 2. Deploy the security rules (~3 minutes)

1. Firebase console → **Firestore Database → Rules**
2. **⚠ This project is shared with other OpMo apps.** Don't replace the whole
   ruleset — copy the two `match` blocks from `firestore.rules` in this repo
   (`endurance_races` and `endurance_registrations`) into your existing rules,
   inside the `match /databases/{database}/documents { … }` block, alongside
   whatever match blocks your other apps already use.
3. Click **Publish**.

## What the rules enforce

| Action | Who |
|---|---|
| View races & registrations | Everyone |
| Register for a race | Everyone (always lands in the unassigned pool) |
| Withdraw own entry (unassigned) | Everyone |
| Create a race | Everyone (matches the public Add Race button) |
| Edit race, add teams, assign/unassign drivers, enter results, delete races or assigned registrations | Signed-in TP only |

Duplicate registrations are blocked structurally: each registration's document
ID is derived from the race + iRacing username, and overwriting an existing
document counts as an *update*, which the rules deny for anonymous users.

## Rotating the old password

The previous TP password (`opmo2025`) was visible in the page source and is
still in the git history — treat it as public. It no longer unlocks anything
once the steps above are done, but don't reuse it for the new account.
