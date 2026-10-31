# DMWorkbench (build repo)

This repository holds the build that is published at `https://dmworkbench.aegisopstech.me` (Cloudflare). It contains only the **database edition** of the CFO workbench for Diminishing Musharaka contracts.

| File | Purpose |
| --- | --- |
| `index.html` | The page that is served. A copy of the database edition. |
| `Diminishing Musharaka_ CFO Workbench V2.1.html` | The same database edition under its source name. |
| `CNAME` | Custom domain. |

The database edition needs sign-in. Everything is saved in the Supabase schema `dm`, with row level security. The page carries only the public project URL and the publishable key. The database password and the service-role key must never be put in this repository.

## Do not edit here

Files here are copied from `DMWorkbench-Code-Repo` (`workbench\Diminishing Musharaka_ CFO Workbench V2.1.html`). Change the source in the code repo, then copy it to `index.html` and to the named file here, commit and push. The standalone (browser-only) edition stays in the code repo and is not published from here.

## Supabase settings for the live site

In Supabase Authentication, set the Site URL and the redirect URLs to `https://dmworkbench.aegisopstech.me`, so password-reset and sign-in redirects return to the live page. Password reset uses an emailed code, so the Reset Password email template must show `{{ .Token }}`.
