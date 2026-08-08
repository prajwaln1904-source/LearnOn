# LearnOn

LearnOn is a personal study tracker for logging daily work, rating each day, reflecting briefly, and seeing weekly study hours and a yearly contribution chart.

## Features

- Email and password sign-in with Supabase
- Personal cloud-synced data per user
- Add completed activities with a custom subject and hours studied
- Five-level daily rating: Rough → Great
- Quick daily reflection
- Manual **Log this day** action — drafts do not count or sync until logged
- Dynamic weekly subject summary with hours, subject count, logged-day count, and progress bars
- GitHub-style yearly contribution chart with size controls
- Light matte glass mode and high-contrast dark mode

## Use the tracker

1. Add an activity: what you did, the subject, and hours spent.
2. Add as many activities as needed for the day.
3. Choose a day rating and write an optional reflection.
4. Click **Log this day** to save it to the cloud and count it in the chart.

## Run locally

Open `index.html` in a browser. For sign-in and cloud sync to work reliably, deploy it to a hosted URL instead of using a local `file://` URL.

## Deploy with Vercel

1. Put the tracker file in your repository as `index.html`.
2. Commit and push it to GitHub.
3. Import the repository into Vercel.
4. Each new GitHub commit will automatically redeploy the site.

## Supabase setup

The app uses Supabase Auth and a single private table named `study_profiles`.

Run this in **Supabase → SQL Editor** once:

```sql
create table if not exists public.study_profiles (
  user_id uuid primary key references auth.users(id) on delete cascade,
  data jsonb not null default '{"days": {}}'::jsonb,
  updated_at timestamptz not null default now()
);

alter table public.study_profiles enable row level security;

create policy "Users can read only their own study profile"
  on public.study_profiles for select
  using (auth.uid() = user_id);

create policy "Users can create only their own study profile"
  on public.study_profiles for insert
  with check (auth.uid() = user_id);

create policy "Users can update only their own study profile"
  on public.study_profiles for update
  using (auth.uid() = user_id)
  with check (auth.uid() = user_id);
```

In **Authentication → URL Configuration**, add your Vercel URL as the Site URL and Redirect URL.

## Security note

The browser uses the Supabase publishable key. This is safe for a web app when Row Level Security is enabled. Never add a Supabase `service_role` or secret key to this file.

## Files

- `index.html` — the complete LearnOn website
- `README.md` — this guide
