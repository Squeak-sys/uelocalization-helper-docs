# UE Localization Helper — Documentation

[UE Localization Helper](https://www.fab.com/) is an **Unreal Engine editor plugin** that captures English UI text from the editor's **Details Panel** and third-party plugin panels at the Slate layer, and localizes it into your language **in real time** — with one-click switching back to English at any moment.

**No engine files are modified, no project structure is changed, and packaging/migration is unaffected — install and play.**

- Engine support: Unreal Engine **5.5 / 5.6**
- Plugin type: Editor-only
- Platforms: Windows

> This product was created with the assistance of generative AI tools.

---

## Installation

1. Copy the whole `UELocalizationHelper` folder into your project's `Plugins` directory:

   ```
   <YourProject>/
   └─ Plugins/
      └─ UELocalizationHelper/
   ```

2. Open the project with Unreal Engine 5.6 (5.5 also works) and let the editor rebuild the plugin.
3. After startup, a **UELocalizationHelper** button appears in the main toolbar (in the User zone). Click it to open the operation window.
4. First launch: if the project root has no `LocalizationDictionary.txt`, the plugin automatically copies `Dictionary.example.txt` as your starter dictionary.

---

## Quick Start: Your First Localization

### Step 1 — Capture

1. Open the panel you want to localize (e.g. select any actor to show its **Details Panel**).
2. Click **Start Capture**.
3. Slowly expand panels / open more views so more English text renders on screen.
4. The plugin traverses visible UI every refresh interval (default 0.5 s, adjustable), collecting and deduplicating English entries.

### Step 2 — Translate

1. Click **Stop & Export**. Captured entries are written to the project root as a new file, e.g.:

   ```
   LanguageSwitch_001.txt   (auto-numbered; old files are never overwritten)
   ```

2. Translate each line in that file with any tool you like, then save it as a dictionary, e.g. `LocalizationDictionary.txt`.

**Dictionary file format:**

```
# Lines starting with # or ; are comments
English=Chinese
```

- One pair per line: `English=Chinese` (a TAB separator also works).
- Lines starting with `#` or `;` are ignored as comments.
- Save as **UTF-8**.
### Step 3 — Apply

1. In the plugin window, click the row's **Import Dictionary** button and pick your translated file.
2. The group loads all entries and activates immediately — the Details Panel switches to your language on the spot.
3. Translations are **kept alive automatically**: text that appears later (after scrolling, expanding categories, or switching views) is re-localized without any action.

### Step 4 — Switch Anytime

- Each group has a **Translate** checkbox. Uncheck it to stop that group and restore English instantly.
- Check it again to re-apply. Nothing is lost in between.

---

## Window Reference

| UI Element | What it does |
|---|---|
| **UI Language** | Switches the plugin's own interface language (cycles through files in the `Languages` folder) |
| **Refresh interval (s)** | How often capture/localization runs. Default 0.5 s. Applied on Enter or focus loss, and persisted |
| **Group rows** | Each row = one localization group: tag (renameable), dictionary path, Translate checkbox, Import / Merge buttons, Delete button |
| **Add Dictionary** | Appends a new group row (auto-named GroupN, empty path, inactive) |
| **Start Capture / Stop & Export** | Capture pass control; export writes a new timestamped/numbered file |
| **Status bar** | Shows group count, active count, total entries, capture state, and results of the last operation |

### Import vs. Merge

- **Import Dictionary** — first load: clears the group's old entries, loads the whole file, and activates the group immediately.
- **Merge Dictionaries** — pick another dictionary file: the current group's dictionary is the base (existing translations win), missing entries are appended from the selected file, and a brand-new merged document is generated and imported automatically. **The selected source file is never modified.**

### What Gets Captured (and What Doesn't)

The capture automatically **skips**:

- Text already containing Chinese characters (already localized, or mixed like `UE5 中文`)
- Pure numbers, booleans, and "number + unit" values (slider displays)
- Asset/script paths (`/Game/`, `/Script/`, `/Engine/`, `/Plugin/`)
- Invisible windows or destroyed widgets (crash-safe)

By default, capture and localization are restricted to **Details Panel** subtrees, detected at runtime without depending on private engine headers.


---

## Configuration & Persistence

All settings are saved to `Saved/Config/UELocalizationHelper.ini` and restored on editor startup:

```ini
[Groups]
Groups=Tag="Default", DictionaryPath=".../LocalizationDictionary.txt", bActive=1)
[UI]
Language=en
[General]
RefreshInterval=0.5
```

Group changes, window close, and editor shutdown all trigger a save.

---

## Plugin UI Languages

The plugin's own interface is externalized into plain-text language files under `Plugins/UELocalizationHelper/Languages/`:

```
Languages/
├─ en.txt   # English template (keys map to themselves)
└─ zh.txt   # Chinese translations
```

**To add your own language** (e.g. Japanese): copy `en.txt` to `ja.txt`, translate the right side of each `English=Japanese` line, and save as UTF-8. The new language appears in the UI Language switcher automatically.

Notes:

- The English source text is the **translation key**; unmatched keys fall back to English.
- Placeholders like `{0}` are runtime format tokens — keep them unchanged.
- Only the plugin's own UI is translated this way. Your group tags, dictionary content, and exported file names are your data and are never rewritten.

---

## File Structure

```
UELocalizationHelper/
├─ UELocalizationHelper.uplugin   # Plugin descriptor
├─ Dictionary.example.txt         # Starter dictionary (auto-copied on first launch)
├─ Languages/
│  ├─ en.txt
│  └─ zh.txt
├─ Config/
│  └─ FilterPlugin.ini
├─ Resources/
│  └─ Icon128.png
└─ Source/UELocalizationHelper/
   ├─ UELocalizationHelper.Build.cs
   └─ Private/
      ├─ UELocalizationHelperModule.*   # Module entry, toolbar button, ticker
      ├─ LocalizationManager.*          # Core logic: capture/export/dictionary/replace
      └─ LocalizationWindow.*           # Operation window UI
```

---

## Performance

- **Idle cost: zero.** No traversal happens unless capture is active or at least one group is enabled.
- While active, a low-frequency poll (default every 0.5 s) captures or re-applies translations; the ticker itself runs at 0.05 s but skips work between intervals.

---

## Known Limitations

- Capture/localization targets **static panel text** (`STextBlock`) inside Details Panel subtrees. Rapidly changing dynamic text (live counters, streaming values) is not continuously refreshed.
- The plugin does not duplicate engine-level game localization — for shipping localized games, use Unreal's official **Game Localization** pipeline.
- Traversal relies on the public `SWidget::GetChildren()` Slate API; a future engine change may require a minor update.

---

## Support

- Found a bug or need help? Reach out via the support channel listed on the product page.
- Version history is included in the plugin package (`CHANGELOG.md`).
