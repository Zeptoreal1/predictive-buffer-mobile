# Predictive Input Buffer — Mobile Controls Patch Kit

This is a **source patch kit**, not an installable `.geode` file. It adds two touch controls to the public Geode source project so Android users do not need a keyboard for the existing F6/K controls:

- **BUF** cycles the input-buffer stage (the same action as F6).
- **LOG** opens/closes the status and diagnostic panel (the same action as K).
- A `Mobile touch controls` setting can hide the buttons if they overlap your taps.
- A GitHub Actions workflow builds Android 32-bit and 64-bit versions remotely, so you do not need a computer or Android NDK locally.

## Important compatibility note

This patch targets the public source repository [`fraudian/Predictive-Input-Buffer-V2`](https://github.com/fraudian/Predictive-Input-Buffer-V2). Its mod ID/name/version match the uploaded file, but its current source manifest does **not** include all the extra settings found in your uploaded `.geode` (for example, the background image, practice noclip, and game-speed controls). I could not confirm that this public source is the exact source used to compile your file. Building this source may therefore produce a simpler version and will not automatically preserve those extra features.

## Apply it from your phone

1. Open the source repository above in your mobile browser and tap **Fork** to make a copy in your own GitHub account.
2. In your fork, open `src/main.cpp` and tap the pencil/edit control.
3. Follow `snippets/main-cpp-edits.md` to make the three small C++ edits. Use the GitHub editor's find function to locate the exact anchor text.
4. Replace `mod.json` with the `mod.json` file in this kit.
5. Create `.github/workflows/android-build.yml` in your fork and paste in the workflow in this kit. In GitHub's **Add file → Create new file** page, entering the full path creates the folders.
6. Commit your changes. Open the **Actions** tab and open the Android build run. The workflow creates a combined Android `.geode` artifact for 32-bit and 64-bit devices.
7. Download and extract the artifact. Copy the `.geode` file to:

   `/storage/emulated/0/Android/media/com.geode.launcher/game/geode/mods/`

8. Restart Geometry Dash/Geode and test in a practice level first. If the two touch buttons cover an area you need to tap, disable **Mobile touch controls** in the mod settings.

## If the build fails

Open the failed run under **Actions**, select the job, and copy the first actual compiler error (not just the final “process completed with exit code 1” line). The source repo may have moved on from the SDK version used in your `.geode`, so the error needs to be checked before changing more code.

## Files

- `snippets/main-cpp-edits.md` — exact C++ insertions and the Windows-only console guard.
- `mod.json` — updated metadata and the new mobile-controls setting for the source repository above.
- `.github/workflows/android-build.yml` — hosted Android build workflow.

This patch uses the upstream source project as a starting point; it is not affiliated with or endorsed by the original mod author.
