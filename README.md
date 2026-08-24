# pep-widgets

Three custom widgets for the peplum film library Grist document. Static HTML — no build step,
no dependencies beyond Grist's own `grist-plugin-api.js`.

| Widget | File | Attach to | Select by | Access |
|---|---|---|---|---|
| Frame Compare | `frame-compare.html` | Stills | FrameSets | Read selected table |
| Scene Matrix | `scene-matrix.html` | Films | (nothing, or a Films list) | **Full document access** |
| Stills Gallery | `gallery.html` | Stills | optional | Read selected table |

---

## 1. Publish on GitHub Pages

```bash
git init
git add .
git commit -m "Peplum Grist widgets"
git branch -M main
git remote add origin git@github.com:rocksrdone/pep-widgets.git
git push -u origin main
```

Then **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)` → Save**.
After a minute the widgets are live at:

```
https://rocksrdone.github.io/pep-widgets/frame-compare.html
https://rocksrdone.github.io/pep-widgets/scene-matrix.html
https://rocksrdone.github.io/pep-widgets/gallery.html
```

The repo can be public or private — **Pages output is public either way**, so don't put anything
private in here. These widgets contain no data; they only read it from your document at runtime.

## 2. Add one to a page in Grist

**Add New → Add Widget to Page → Custom** → pick the table under *Select Data* (and *Select By*
where the table above says so) → **Add to Page**. Then open the creator panel (green bar, top
right) → **Custom** tab → paste the URL under *Enter Custom URL* → set the access level → map the
columns it asks for.

---

## Frame Compare

The one your text files can't do. Shows every still in the selected frame set side by side, and
**pans all of them together**: pick 2×/4×/8×, then move the mouse over any image and all of them
magnify the same relative spot at once. That's how you actually judge cropping, sharpness, colour
timing and compression between two transfers.

- *Select Data*: `Stills` · *Select By*: the `FrameSets` widget on the same page.
- Column mapping: **Screenshot** → `Image`, **Caption** → `Release`, **Timecode** → `Timecode`.
- Access: *Read selected table* (needed to fetch the attachment).
- Two or three releases lay out in one row; more wrap into a grid.

The panning trick uses CSS background positioning in percentages, so images of slightly different
resolutions still track each other. Frames from a PAL and an NTSC disc of the same film will be
near-identical in framing but not pixel-identical — that's the disc, not the widget.

## Scene Matrix

Scenes down the side, releases across the top, one cell per combination. Green tick = present,
red cross = absent, amber = alternate or partial, grey dot = not recorded yet. Bottom row totals
coverage per release.

**Click any cell to cycle it** Present → Absent → Alternate take → Partially cut → Unknown. It
writes straight back to `SceneAvailability`, creating the row if it doesn't exist yet. Filling
that matrix cell by cell in the normal grid view is miserable; here it's a few clicks per scene.

- *Select Data*: `Films`. Put a Films table widget above it and it follows your cursor.
- Access: **Full document access** — it reads three tables and writes to one. It sends nothing
  anywhere; the code is in `scene-matrix.html` and it's short enough to read.
- Table and column names are in the `T` object at the top of the file. If you rename anything in
  Grist, change it there.

## Stills Gallery

Grid of every still with a live search box (name, film, caption, purpose) and a purpose filter.
Click any image for a full-size lightbox. Use it as the actor gallery by filtering
*Purpose = Actor ID*, or leave it unfiltered as a general contact sheet.

- *Select Data*: `Stills`. Leave *Select By* empty for everything, or link it to Films.
- Column mapping: **Image** → `Image`, **Person** → `Person`, **Film** → `Film`,
  **Caption** → `Caption`, **Timecode** → `Timecode`, **Purpose** → `Purpose`.

---

## Notes

- **Attachments**: Grist attachments aren't public URLs. The widgets call
  `grist.docApi.getAccessToken({readOnly:true})` and build
  `{baseUrl}/attachments/{id}/download?auth={token}`. Tokens are short-lived, so they're
  re-requested every 60 seconds. If images go blank after the widget has sat idle, reload the page.
- **Self-hosted Grist**: change the script tag in each file from
  `https://docs.getgrist.com/grist-plugin-api.js` to `/grist-plugin-api.js` on your own instance.
  `manifest.json` here can be pointed at with `GRIST_WIDGET_LIST_URL` so all three show up in the
  widget picker instead of being pasted in by URL.
- **Debugging**: right-click inside the widget → *Inspect*, or open the widget URL directly in a
  browser tab. Outside Grist it just sits there waiting for data, which confirms Pages is serving
  it correctly.
- **Nothing is tested against a live document** — these were written against the Grist plugin API
  docs. Expect one or two small fixes on first run; the console will tell you what.
