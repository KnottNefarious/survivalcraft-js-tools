# survivalcraft-js-tools
Single-file HTML tool for running JavaScript and teleporting in Survivalcraft API 1.9.3.1 via the in-game JavaScript Remote Control.

# Survivalcraft JS Runner & Teleporter

A single-file HTML tool for running JavaScript against **Survivalcraft API 1.9.3.1** on Android, using the game's built-in JavaScript Remote Control server.

It gives you a real text editor (no character limit), a preset dropdown of useful commands, and a one-tap teleporter.

---

## What it does

- **JS Runner tab** — write or paste any JavaScript the game's API accepts, then send it and see the result.
- **Preset dropdown** — pick from preloaded commands with plain-English descriptions. Selecting one loads its code into the editor so you can review, edit, or just run it.
- **Teleporter tab** — type X/Y/Z and teleport. Also includes nudge buttons, "Where am I?", and save/goto spawn.

---

## Requirements

- **Survivalcraft API 1.9.3.1** (Android). The stock Play Store version doesn't include the JavaScript Remote Control.
- **Chrome for Android** (or a Chromium-based browser).
- The game must be **open with a world loaded** and **Remote Control toggled ON**.
- The game must stay in the **foreground** or in **split-screen** while you run scripts. If Android pauses the game, its server dies.

---

## Setup

### 1. Turn on Remote Control in the game

- Launch Survivalcraft API and load a world.
- Open the in-game menu.
- Find **JavaScript Remote Control**.
- Toggle it **ON**.
- Note the **port** (default `7765`) and the **password** shown on screen.

### 2. Enable Chrome's command-line file on non-rooted devices

- Open Chrome → `chrome://flags`.
- Search for **"Enable command line on non-rooted devices"**.
- Set it to **Enabled** and relaunch Chrome.

### 3. Create the Chrome command-line file

This file disables Chrome's web security so it will talk to the game's local server.

You need a way to run a shell command on your phone. **aShell You** with **Shizuku** works. So does **ADB from a PC**.

Run this exact command:

```bash
echo "chrome --disable-web-security --user-data-dir=/data/user/0/com.android.chrome/app_chrome/Default" > /data/local/tmp/chrome-command-line
```

4. Force-stop and reopen Chrome

Settings → Apps → Chrome → Force stop. Then reopen Chrome.

5. Verify the flag is active

Visit chrome://version in Chrome. Under Command Line, you should see:

```
chrome --disable-web-security --user-data-dir=...
```

If it's there, you're ready.

6. Split-screen Chrome with the game

Android will suspend the game if you switch apps, killing the Remote Control server. Put Chrome and Survivalcraft in split-screen so both stay running.

---

Using the tool

1. Open survivalcraft-js-runner.html in Chrome (from your Downloads folder or wherever you saved it).
2. Confirm the password and port at the top match what the game shows.
3. JS Runner tab — pick a preset from the dropdown, or type your own code, then tap Run.
4. Teleporter tab — enter X/Y/Z and tap Teleport.

The output box shows the raw JSON returned by the game.

---

Known limitations

These are limits of the game's API, not the tool:

· One-shot scripts only. Frame handlers register but never fire in this build, so nothing runs automatically in the background. Every action requires you to tap Run.
· Some property reads deadlock the game. Reading certain properties (health, time, sky, weather, some components) can freeze the scripting engine. When this happens you must force-stop the game to recover.
· No queue or timeout. If a script hangs, the server says "There is already a script running" until you restart the game.
· No rendering. You cannot draw HUDs, overlays, or minimaps from JavaScript.
· No custom keybinds. Same reason as above — handlers don't fire.

What does work reliably

· Reading player position, velocity, name, level, health, spawn point
· Writing position (teleport) and velocity (launch, stop)
· Writing spawn point
· Enumerating subsystems, entities, and API objects

---

Troubleshooting

"Invalid password"
The header name is case-sensitive. Try password, Password, or PASSWORD. Also confirm the password shown in the game menu hasn't changed.

"There is already a script running"
A previous script deadlocked the engine. Force-stop the game, reopen, load the world, toggle Remote Control back on. Nothing else clears it — not refreshing the page, not restarting Chrome.

"FETCH FAILED" in the output box
Usually means the game isn't in split-screen and has been suspended. Put it back into split-screen with Chrome and try again.

Teleport works but reading properties hangs
Some properties are unsafe to read. Stick to Position, Velocity, Name, Level, and Health. If any of those hang, drop them from your scripts.

Game frozen completely
Force-stop from Settings → Apps → Survivalcraft → Force stop.

---

Sharing

This tool cannot be hosted on GitHub Pages (or any HTTPS site). Browsers block HTTPS pages from calling http://127.0.0.1, and CORS blocks it even if that weren't an issue.

The intended use is:

1. Download the HTML file.
2. Open it locally in a Chrome with web security disabled.
3. Use it against a running game on the same device. (have to be split screen)

That's why it's distributed as a single file rather than a hosted page.

---