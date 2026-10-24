# Puzzle #20 — Supabase Auth signup/login with email confirmation

The Real World `#automate-me-this-puzzle` puzzle #20 (2026-02-02): login page backed by
Supabase's built-in Auth system, where new signups must confirm their email before they
can actually log in.

## What this is

A single static page (`public/index.html`) using `@supabase/supabase-js` directly from
the browser (no backend):

- Sign up tab calls `supabase.auth.signUp({ email, password })`.
- Log in tab calls `supabase.auth.signInWithPassword({ email, password })`.
- A logged-in panel shows the real session/user object once auth succeeds.
- No hand-rolled users table: this uses Supabase's built-in `auth.users`, not a custom
  Postgres table.

## Supabase project

Reused the existing shared project `qqncjqapqaubbbnkzhjb` (ref used across puzzle #11 and
#15) rather than a fresh dedicated project: the free-tier org already had its 2 allowed
*active* projects occupied (the shared puzzle project + the unrelated `mini-url-shortener`
project the user is actively working on in a separate session), and `create_project`
returned a hard `BadRequestException` ("2 project limit") confirming the cap, same as
puzzle #15's prior finding. Reusing the unrelated `mini-url-shortener` project would have
been worse (crams puzzle auth into someone else's real app), so the shared puzzle project
was the correct choice. No new table was added to it; this puzzle only touches that
project's built-in Auth (GoTrue), not its Postgres schema.

## Email confirmation requirement

"Confirm email" could not be checked via the Supabase *dashboard* toggle directly: no
browser session was logged into the Supabase dashboard this session, and entering the
user's real account password to log in is out of scope. Instead it was verified
*behaviorally*, which is actually the authoritative test:

1. Signed up a real test address (`krikormher+puzzle20@gmail.com`, Gmail plus-addressing
   delivers to the user's real inbox) via the deployed page.
2. Immediately tried to log in with the same credentials -> Supabase returned
   `Email not confirmed`, proving the project's auth requires confirmation before login.
3. Found the real confirmation email in the inbox and opened the real confirmation link.
4. Logged in again with the same credentials -> succeeded, session/user shown.

See `CAPABILITY_LOG.md` (task-forge memories) for the exact timestamps/results of this run.
