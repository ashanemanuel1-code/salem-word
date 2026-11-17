# Salem Word

Bible reader, daily devotions, reading plans and church-wide reading challenges for **House of Salem Church, Sri Lanka**.

*Read • Reflect • Grow*

| Part | Folder | Built with |
| --- | --- | --- |
| Android app (iOS-ready) | `apps/mobile` | React Native, Expo, TypeScript, Expo Router, React Native Paper, i18next |
| Admin dashboard (website) | `apps/admin` | Next.js, TypeScript, Tailwind CSS |
| Backend | `supabase` | Supabase: PostgreSQL, Auth, Storage, Row Level Security, Edge Functions |
| Shared rules and types | `packages/shared` | TypeScript (Bible book list, reading-plan rules, language fallback) |

Android package: `com.houseofsalem.salemword` · Version `1.0.0` · Languages: English, සිංහල, தமிழ்

## Start here

New to programming? Follow the guides in this order. Each step says what to click, what to type and what you should see.

1. [`docs/beginner-setup.md`](docs/beginner-setup.md) - install the tools, create the Supabase backend, run the app and the dashboard on your computer.
2. [`docs/apk-build-guide.md`](docs/apk-build-guide.md) - build the Android APK, install it on a phone, prepare the Google Play release.
3. [`docs/deployment-guide.md`](docs/deployment-guide.md) - put the code on GitHub, publish the dashboard on Vercel, switch on push notifications.
4. [`docs/admin-guide.md`](docs/admin-guide.md) - for church administrators: how to publish devotions, plans, challenges and notifications.
5. [`docs/testing-report.md`](docs/testing-report.md) - what has been tested, what has not, and known limitations.

## Quick start (if you already know the tools)

```bash
npm run setup                               # installs everything
# create a Supabase project, run supabase/migrations/*.sql in order, then supabase/seed.sql
copy apps\mobile\.env.example apps\mobile\.env          # fill in the 2 values
copy apps\admin\.env.example  apps\admin\.env.local     # fill in the 3 values
npm run mobile                              # Expo dev server (scan the QR code)
npm run admin                               # dashboard at http://localhost:3000
npm test                                    # shared logic + translations + database security
```

First Super Admin (once, in the Supabase SQL Editor, after registering in the app):

```sql
select public.bootstrap_super_admin('your-email@example.com');
```

## What you must provide yourself

| Item | Why | Where it is explained |
| --- | --- | --- |
| A Supabase project (free tier is enough to start) | Database, logins, images | beginner-setup, Stage 3 |
| An Expo account (free) | Builds the APK in the cloud | apk-build-guide |
| A GitHub and a Vercel account (free) | Source code and dashboard hosting | deployment-guide |
| A Firebase project (free) | Android push notifications | deployment-guide, "Push notifications" |
| **Permission for a Sinhala Bible translation** | No openly licensed Sinhala Bible was found. The Ceylon Bible Society and Biblica hold the rights to the common ones. | admin-guide, "Bible sources" |
| A Google Play developer account (one-time US$25) | Only for publishing on Google Play | apk-build-guide |
| An e-mail sending service (SMTP) | Supabase's built-in e-mail is limited to a few messages per hour | deployment-guide |

## Bible text and copyright

The app ships **no Bible text**. Administrators list authorised translations under *Bible sources* in the dashboard, and the app fetches chapters from that source:

- **English** - Berean Standard Bible (public domain) is set up for you through the Free Use Bible API.
- **Tamil** - the Indian Revised Version (CC BY-SA 4.0, © Bridge Connectivity Solutions) is openly licensed; add it from the dashboard's catalogue after reading the licence.
- **Sinhala** - needs written permission from the copyright holder. Until then the app shows the full book and chapter navigation with a clear "translation not available yet" message. It never shows placeholder text as Scripture.

A translation cannot be switched on until an administrator ticks "licence confirmed" - the database enforces this.

## Security in one paragraph

Every table has Row Level Security. Members can read only published content and only their own progress, bookmarks and settings. Roles (Super Admin, Admin, Editor) live in a table members cannot write to; the first Super Admin is created with a one-time SQL command. Administrators see church-wide totals, never an individual's reading, unless that member switches on sharing. The secret service-role key is used only on the dashboard's server. Administrator actions are written to an audit log that cannot be edited. `npm run test:db` proves these rules against a real PostgreSQL engine.

## Project layout

```text
salem-word/
  apps/
    mobile/            Expo app (src/app = screens, src/locales = en/si/ta.json)
    admin/             Next.js dashboard (src/app/(dash) = pages, src/app/api = server routes)
  packages/
    shared/            single source of truth, copied into both apps by `npm run sync-shared`
  supabase/
    migrations/        4 SQL files: tables, functions, security, storage
    functions/         Edge Functions: send-notification, bible (API.Bible proxy)
    seed.sql           starting categories, English Bible source, sample plan + challenge
  scripts/             automated tests
  docs/                the guides listed above
```

Edit shared code only in `packages/shared/src`, then run `npm run sync-shared`.
