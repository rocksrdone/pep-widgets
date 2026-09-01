# MasterParts — recording what went into a fandub

Your `Masters` table has `Audio_Plan` and `Subtitle_Plan` as free text. Fine for planning, useless
once the file exists and you want to know exactly what is inside it. This is one row per
component instead.

## Create the table

**Add New → Add Empty Table**, name it **MasterParts**, then add these columns.

| Column | Type | Holds |
|---|---|---|
| `Master` | Reference → Masters, show `Version_Name` | Which build this belongs to |
| `Part_Type` | Choice | What kind of component |
| `Language` | Text | English, Italian… |
| `Lang_Code` | Text | `eng`, `ita`, `fra` — for mkvmerge |
| `From_Release` | Reference → Releases, show `Title_On_Release` | Where it came from |
| `Treatment` | Choice | What you did to it |
| `Time_Range` | Text | `whole film`, `00:41:12`, `reel 3 onward` |
| `Order` | Integer | Sort order, stepped by 10 |
| `Notes` | Text | Anything that would puzzle you later |

**Part_Type**
```
Video
Audio
Subtitle
Scene insert
Chapters
Other
```

**Treatment**
```
Untouched
Speed corrected PAL to NTSC
Speed corrected NTSC to PAL
Resynced by hand
OCR from image subs
Translated
Retimed
Re-encoded
Volume matched
Cut to fit
```

Also add one column to **Masters**:

| Column | Type |
|---|---|
| `Build_Log` | Text, wrap on |

The widget writes the assembled summary there, so the whole story sits on the master row.

---

## What it gives you

**A readable account** of the file, built from the rows:

```
Gli amori di Ercole (1960)
Composite cut v1

VIDEO
  - from Surf Video IT DVD, at whole film

AUDIO
  - Italian, from Surf Video IT DVD
  - English, from Alpha US DVD, speed corrected ntsc to pal  (drifts slightly after reel 3)

SUBTITLE
  - English, from Rider Films ES DVD, ocr from image subs
  - Italian, translated  (from the Spanish subs)

SCENE INSERT
  - from Alpha US DVD, cut to fit, at 00:41:12  (bathtub exit, missing from the Italian disc)
```

**Track names for mkvmerge**, so the file explains itself. Plex shows these in its audio and
subtitle menus, which means you can tell the Alpha dub from the Surf Video one without opening
anything:

```
--track-name 0:"Italian - Surf Video IT DVD"
--track-name 1:"English - Alpha US DVD - speed corrected ntsc to pal"
```

**A tags file** to embed. Save the generated XML as `tags.xml` and add `--global-tags tags.xml`
to your mkvmerge command. The build log then travels *inside* the MKV, so it survives the folder
being reorganised, the database being lost, or the file turning up on a drive in ten years with
no context.

---

## Working practice

**Log as you go, not afterwards.** The details that matter — which reel the sync drifts on, which
disc the insert came from — are obvious while you are editing and gone a week later.

**One row per track, not per language.** If you have two English audio tracks from different
discs, that is two rows. The whole point is to distinguish them.

**Scene inserts get their own rows** with a `Time_Range`. Those are the parts of a fandub nobody
else could reconstruct, and the ones most likely to need revisiting when a better source turns up.

**Keep a copy as `build notes.txt`** in the film's `x-Archive` folder alongside the Premiere
project. Belt and braces: the database has it, the MKV has it embedded, and the folder has it in
plain text.
