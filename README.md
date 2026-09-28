# JLPT Kanji Widget 🀄

A free kanji study tool covering **all five JLPT levels (N5 to N1)**, with about **2,100 kanji**. Each kanji comes with its readings, meaning, an example phrase in Japanese, romaji and an English translation.

It comes in three forms:

| Part | What it is | Status |
|---|---|---|
| **Web app** | A single HTML file. Auto-rotating flashcards, study mode, audio. Works in any browser and can be added to a phone's home screen. | Tested logic, actively used |
| **Anki deck** | A ready-to-import file with one card per kanji, one deck per level. | Format checked, import not tested |
| **Android widget** | Android Studio project with a home-screen widget that rotates through the kanji. | **Source only, never compiled** |

> **Read this first:** the kanji lists come from public community sources, but the **readings, example sentences and translations were written by an AI (Claude) and have not been checked by a native speaker.** Expect some awkward or wrong entries, especially among the rare N1 kanji. Corrections are very welcome (see [Contributing](#contributing)).

---

## Features

### Web app (`web/jlpt-kanji-widget-v2.html`)

- **Widget mode:** cards rotate automatically (3, 5 or 8 seconds). Tap a card for the next one.
- **Study mode:** shows only the kanji. Tap to reveal the answer, then mark it **✓ Known** or **★ Review**.
- **Progress tracking:** a progress bar per level, plus a filter for *All / Not known yet / ★ Review*.
- **Fast navigation:** ±10 and ±50 skip buttons, a random button, a position slider, and a box to **jump by number or by typing a kanji**. Swipe the card to move.
- **Audio:** reads the example phrase aloud using your device's Japanese text-to-speech, with an optional auto-read.
- **Level tabs:** All, N5, N4, N3, N2, N1. Your position is remembered for each level.
- **Keyboard shortcuts (desktop):** `←` `→` move, `k` known, `r` review, `s` listen, `space` reveal.
- **Light and dark mode**, mobile friendly, no dependencies and no internet needed.

Progress is stored in the browser's `localStorage`, so it stays on one device and can be lost if you clear site data.

### Anki deck (`anki/jlpt-kanji-anki-import.txt`)

2,098 cards. The front is the kanji. The back has the meaning, reading, example phrase, romaji and translation. Each level goes into its own deck (`JLPT Kanji::N5` … `JLPT Kanji::N1`) and is tagged (`JLPT_N5` …).

**To import:** open Anki, choose **File → Import**, and select the file. This needs a fairly recent Anki (about 2.1.55 or newer), because the file uses header lines to set the deck and tags.

### Android widget (`android/JLPTKanjiWidget/`)

A home-screen widget showing the kanji, reading and meaning, the example phrase, and its romaji and translation. A small settings screen lets you choose the level (or All) and a rotation speed of 15, 30 or 60 minutes. Tapping the widget advances to the next kanji.

---

## Quick start

### Web app

1. Download `web/jlpt-kanji-widget-v2.html`.
2. Open it in a browser (double-click on desktop, or open it from your phone's files).
3. On a phone, use **Add to Home Screen** from the browser menu to keep it like an app.

No build step and no server are needed.

### Android

Requirements: Android Studio (a recent version), JDK 17, and a device or emulator running Android 7.0 (API 24) or newer.

1. Open the `android/JLPTKanjiWidget` folder in Android Studio.
2. Let Gradle sync. This needs internet access.
3. Run the app on a device or emulator.
4. Long-press the home screen, choose **Widgets**, and drag **JLPT Kanji Widget** onto it.
5. Open the app to pick a level and rotation speed, then tap **Apply to widget**.

The project pins Android Gradle Plugin 8.5.0 and Kotlin 1.9.24. If your Android Studio complains, bump those in `build.gradle`.

---

## Repository layout

```
.
├── README.md
├── web/
│   └── jlpt-kanji-widget-v2.html      # the web app (data is embedded in the file)
├── anki/
│   └── jlpt-kanji-anki-import.txt     # Anki import file
└── android/
    └── JLPTKanjiWidget/               # Android Studio project
        └── app/src/main/
            ├── assets/kanji.tsv       # the kanji data used by the widget
            ├── java/com/example/jlptkanjiwidget/
            │   ├── KanjiData.kt            # loads kanji.tsv
            │   ├── KanjiWidgetProvider.kt  # widget rendering and rotation
            │   └── MainActivity.kt         # level and speed settings
            └── res/                        # layouts, widget config, strings
```

Older versions of the web app added features one step at a time (v1 added skip and jump navigation, v2 added study mode, progress and audio). Only v2 is needed.

---

## Data

### Counts

| Level | Kanji |
|---|---|
| N5 | 103 |
| N4 | 147 |
| N3 | 365 |
| N2 | 400 |
| N1 | 1,083 |
| **Total** | **2,098** |

Every kanji appears **once**, in the lowest level that lists it. Community lists disagree about which level some kanji belong to, so if a kanji is listed under two levels it is filed under the lower one. That is why the N4 count is lower than some published lists.

**The JLPT has no official kanji list.** It stopped publishing one in 2010, so all lists (including this one) are reconstructions. The first ~105 entries of one source's N1 list are archaic units and extremely rare characters, and were intentionally left out.

### Format in the web app

The data is a JavaScript object near the top of the `<script>` tag. Each kanji is one line with six values in a fixed order:

```js
["日", "hi / nichi", "day; sun", "今日は晴れです", "Kyou wa hare desu", "Today is sunny."],
// kanji, reading (kun / on), meaning, example phrase, romaji, phrase translation
```

- **Reading:** `kun / on` in lowercase romaji. Okurigana goes in parentheses, for example `taka(i)` or `yo(mu)`. If there is no kun reading, only the on reading is given.
- **Level:** decided by which array (`N5`, `N4`, `N3`, `N2` or `N1`) the line sits in.
- To add a kanji, add a line to the right array. Tabs, counters and rotation update automatically. A missing comma will break that level, so open the file in a browser to check.

### Format in the Android app

`android/JLPTKanjiWidget/app/src/main/assets/kanji.tsv` has one tab-separated line per kanji, with the level first:

```
N5<TAB>日<TAB>hi / nichi<TAB>day; sun<TAB>今日は晴れです<TAB>Kyou wa hare desu<TAB>Today is sunny.
```

Edit or add lines in that file. No Kotlin changes are needed.

### Keeping the three copies in sync

The web app, the Anki file and `kanji.tsv` were all generated from the same data. If you fix an entry in one, the others will not update. A small script that generates the Anki file and `kanji.tsv` from the web app's data would be a good contribution.

---

## Limitations

- **No lock-screen widget.** Android removed third-party lock-screen widgets in version 5.0 (2014), so this is a *home-screen* widget. It is the closest thing the platform allows.
- **Slow rotation on Android.** The system throttles background updates to save battery, so the widget can't refresh every few seconds. It rotates every 15, 30 or 60 minutes and when tapped.
- **Android source has never been built or run.** It was written without an Android SDK available. Expect to fix small build issues.
- **The Android app is simpler than the web app.** It has no study mode, progress tracking or audio.
- **Audio depends on your device.** The web app uses the browser's built-in speech. Voice quality varies, and some devices have no Japanese voice installed.
- **Content accuracy.** See the note at the top. Sentences are short and simple, and some are stiff or unnatural.

---

## Roadmap ideas

- Have a native speaker or a dictionary check every entry, and flag the weakest ones
- A script to generate the Anki file and the Android data from a single source
- Hiragana readings for the example phrases
- Search by English meaning or romaji
- A quiz mode with multiple choice
- Export and import of study progress between devices
- Widget next/previous buttons on Android

---

## Contributing

Fixes to readings, meanings and example sentences are the most useful contribution. Please open an issue or pull request with:

- the kanji,
- what is wrong, and
- your suggested correction (and a source if you have one).

When editing data, keep the field order and formatting described above. Keep example sentences short and natural, and please avoid copying text from copyrighted dictionaries or textbooks.

---

## Credits and sources

- **Kanji lists (which kanji belong to which level):** compiled by cross-referencing public community lists, chiefly [Nihongo Master](https://www.nihongomaster.com/) (N5, N2, N1) and [eJable](https://www.ejable.com/) (N4, N3). Thank you to those sites.
- **Readings, meanings, example phrases, romaji and translations:** written by an AI assistant (Claude, by Anthropic), with readings and meanings guided by the source lists above. Not independently verified.
- This project is not affiliated with or endorsed by the Japan Foundation or JEES (the organisers of the JLPT).

## License

_Add a license before publishing._ A permissive choice such as **MIT** for the code is common. Because the kanji lists and text come from other sources and from AI, think about how you want to license the data separately (for example CC BY-SA), and check the terms of the sources listed above.
