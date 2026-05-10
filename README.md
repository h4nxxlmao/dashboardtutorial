# Singularity Loader Dashboard Guide

This guide is for developers who publish Roblox Lua scripts through the dashboard.

Users only run:

```lua
loadstring(game:HttpGet("https://scripts.getsingularity.lol/loader"))()
```

The dashboard controls what script each Roblox `game.PlaceId` receives.

## Login

Open:

```txt
https://scripts.getsingularity.lol/login
```

Sign in with the username and password given to you.

After login, open:

```txt
https://scripts.getsingularity.lol/dashboard
```

## Dashboard Areas

**Projects**

Shows supported Roblox games/places and their released versions.

**Build studio**

Where you edit Lua source, save scripts, and release builds.

**Script library**

Shows saved scripts for the selected Place ID.

## Create Or Edit A Project

In **Build studio**, fill in:

```txt
Game name
Place ID
Script name
Lua source
```

Example:

```txt
Game name: Rivals
Place ID: 17625359962
Script name: Main
```

Use the real Roblox Place ID. Do not add leading zeros.

## Save A Script

Click:

```txt
Save script
```

This saves the source into the script library.

Saving does **not** make the script live.

## Load A Saved Script

1. Enter the project Place ID in **Script library**.
2. Click **Load scripts**.
3. Click **Load** on the script you want.

The script will open in the editor.

## Release A Build

Click:

```txt
Release build
```

This publishes the current editor code to:

```txt
https://scripts.getsingularity.lol/load/PLACE_ID
```

The universal loader will automatically use the newest released build.

## Version Override

Leave **Version override** empty to auto-create the next version.

You can also type a custom version:

```txt
1
2
0.0.1
beta-7
hotfix_1
```

Do not type `v`.

Correct:

```txt
0.0.1
```

Wrong:

```txt
v0.0.1
```

Publishing the same version again replaces that version.

## Obfuscation Options

Use **Obfuscate before publishing** if the dashboard should obfuscate the Lua before release.

Note **Obfuscator IS BROKEN** it currently throw error when executing obfuscated script.

Use **Publish raw text** if the code is already obfuscated.

Simple rule:

```txt
Already obfuscated -> Publish raw text
Not obfuscated -> Obfuscate before publishing
```

## Project Buttons

Each project has:

**Edit**

Loads the project into Build studio.

**Scripts**

Loads saved scripts for that project.

**Open**

Opens the live `/load/{placeId}` script in a new tab.

## Testing

To test a specific released project, click **Open** on the project card.

To test the real Roblox loader, execute:

```lua
loadstring(game:HttpGet("https://scripts.getsingularity.lol/loader"))()
```

The loader detects `game.PlaceId` and routes automatically.

## Common Mistakes

**I clicked Save script but `/load/...` did not update**

Use **Release build**. Save only stores the source.

**My version failed**

Use only letters, numbers, dots, underscores, or hyphens.

Good:

```txt
1.0.2
beta-3
hotfix_1
```

Bad:

```txt
v1.0.2
1/0/2
release one
```

**My script is already obfuscated**

Enable **Publish raw text** before releasing.

**The wrong game loaded**

Check the Place ID. The dashboard routes by Roblox `game.PlaceId`, not by game name.
