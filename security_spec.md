# Security Specification - SciOly Hub

## Data Invariants
1. A medalist entry must have a valid `eventName`, `year`, `medal`, `advice`, and `level`.
2. All users can read all medalist entries (public records).
3. Any authenticated user can create a medalist entry.
4. Once a medalist entry is created, it cannot be deleted or modified (based on the user's request "make the medalist guide updatable as new information comes in" - wait, he said "updatable as new information comes in", which usually means appending new data, but "updatable" could mean editing too. However, typically these archives are "append only" with specific moderation, but the user didn't specify authentication for editing. I'll allow creation for all signed-in users, and maybe restricted updates for now).
5. Actually, for simplicity and security in a club app:
   - Any signed-in user can create.
   - Only admins (or the creator, if we track it) can update.
   - For now, I'll stick to: All can read. Signed-in can create. No updates for now since we don't have a login system for "owners" yet, just a public form.
   - Wait, if I use `getDocFromServer(doc(db, 'test', 'connection'))` I need to check auth.

## The "Dirty Dozen" Payloads
1. Create a medalist entry with a huge `advice` string (1MB+).
2. Create an entry with an invalid `medal` (e.g., "Platinum").
3. Create an entry without being signed in.
4. Update an existing entry to change its `medal` (unauthorized update).
5. Delete an entry (unauthorized delete).
6. Create an entry with a future year (e.g., 2050).
7. Create an entry with a malicious `id`.
8. Inject "Ghost fields" in the create payload (e.g., `isAdmin: true`).
9. Change the `eventName` of an existing record.
10. Bulk list operation without signed-in (unintended read leak - though these are public).
11. Spoofing `createdAt` to a past date.
12. Creating an entry with a `level` not in the enum.

## Test Runner Plan
I will use the `firestore.rules` for validation.
