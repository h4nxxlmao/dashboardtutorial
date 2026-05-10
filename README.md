# Singularity Loader System

Dashboard-controlled Roblox Lua delivery.

Players/users only run:

```lua
loadstring(game:HttpGet("https://scripts.getsingularity.lol/loader"))()
```

The loader detects `game.PlaceId`, downloads the latest released build for that place, and runs it.

## Dashboard Links

- Login: `https://scripts.getsingularity.lol/login`
- Dashboard: `https://scripts.getsingularity.lol/dashboard`
- Universal loader: `https://scripts.getsingularity.lol/loader`
- Per-game release: `https://scripts.getsingularity.lol/load/PLACE_ID`

## Roles

`admin`

- Create dashboard users
- Create/edit projects
- Save scripts
- Release builds
- Publish older versions
- Delete builds/projects/scripts

`developer`

- Create/edit projects
- Save scripts
- Release builds
- Publish newer builds
- View projects/scripts

## First Admin Setup

Only needed once.

1. In Supabase, create an Auth user with email/password.
2. Copy that user's UUID.
3. Run:

```sql
insert into public.dashboard_users (user_id, username, email)
values ('YOUR_AUTH_USER_ID', 'owner', 'owner@example.com');

insert into public.user_roles (user_id, role)
values ('YOUR_AUTH_USER_ID', 'admin');
```

Then login with:

```txt
username: owner
password: the Supabase Auth password
```

After that, create all other users from the dashboard.

## Creating Users

Admin only.

1. Open `/dashboard`.
2. Go to **Dashboard users**.
3. Enter:
   - username
   - email
   - password
   - role
4. Click **Create user**.

The new user logs in with the username and password you created.

## Creating A Project

Use **Build studio**.

Required fields:

- **Game name**: display name, like `Rivals`
- **Place ID**: Roblox `game.PlaceId`, like `17625359962`
- **Script name**: saved source name, like `Main`
- **Lua source**: script code

Click **Save script** to store the source without releasing.

Click **Release build** to save the source and publish it as a live build.

## Script Library

Each project can have multiple saved scripts.

1. Select a project or enter its Place ID in **Script library**.
2. Click **Load scripts**.
3. Click **Load** on a saved script to edit it in the editor.
4. Click **Save script** to update it.
5. Click **Delete** to remove that saved source.

Saved scripts are source records. They are not live until you release a build.

## Releasing Builds

When you click **Release build**, the dashboard:

1. Saves the script.
2. Optionally obfuscates it.
3. Uploads the release to Vercel Blob.
4. Records the build in Supabase.
5. Marks that version as latest.

The live URL becomes:

```txt
https://scripts.getsingularity.lol/load/PLACE_ID
```

Project cards have an **Open** button that opens this URL in a new tab.

## Versions

Leave **Version override** empty to auto-create the next version.

You can also set custom versions:

```txt
1
2
0.0.1
beta-7
client_hotfix_2026
```

Do not include `v`.

Correct:

```txt
0.0.1
```

Wrong:

```txt
v0.0.1
```

Publishing the same version again replaces that build.

## Obfuscation Options

Use **Obfuscate before publishing** when the Lua source should be obfuscated by the server.

Use **Publish raw text** when the script is already obfuscated by a developer.

If both are confusing, use this rule:

- Already obfuscated: check **Publish raw text**
- Not obfuscated: check **Obfuscate before publishing**

## Project Controls

Each project card includes:

- **Edit**: fills Build studio with that project.
- **Scripts**: loads saved scripts for that project.
- **Open**: opens `/load/{placeId}` in a new tab.
- **Delete project**: admin only, deletes the project and stored release files.

Each build row includes:

- **Live**: current live version.
- **Publish**: make that version live.
- **Delete**: admin only, remove that build.

## Roblox Usage

Final user command:

```lua
loadstring(game:HttpGet("https://scripts.getsingularity.lol/loader"))()
```

Do not send users `/load/{placeId}` directly unless testing.

## Environment Variables

Required in Vercel:

```env
SUPABASE_URL=
SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
BLOB_READ_WRITE_TOKEN=
PUBLIC_BASE_URL=https://scripts.getsingularity.lol
ALLOWED_ORIGINS=https://scripts.getsingularity.lol
BLOB_ACCESS=public
```

Optional:

```env
LUA_OBFUSCATOR_ARGS=--force --luau-safe
```

## Deployment

After edits:

```powershell
git add -A
git commit -m "Update dashboard"
git push -u origin main
```

Then redeploy in Vercel if it does not auto-deploy.

If the dashboard still looks old, hard refresh:

```txt
Ctrl + F5
```

## Troubleshooting

`builds_blob_path_check`

Your database still has an old numeric-only version constraint. Run:

```sql
alter table public.builds
drop constraint if exists builds_blob_path_check;

alter table public.builds
add constraint builds_blob_path_check
check (blob_path ~ '^scripts/[0-9]{1,20}/v[A-Za-z0-9._-]{1,80}\.lua$');
```

`spawn python ENOENT`

Python is not available in Vercel Node functions. Use the Node obfuscator or publish raw/pre-obfuscated scripts.

Blob access errors

Set:

```env
BLOB_ACCESS=public
```

and make sure the Vercel Blob store is public.
