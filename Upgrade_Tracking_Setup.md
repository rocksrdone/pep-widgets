# Upgrade tracking — columns to add

Answers two different questions that your notes currently blur together:

1. **Is there a better disc out there than the one I own?** — a fact, and mostly computable.
2. **Is the best that exists actually good enough?** — a judgement, and only you can make it.

Add these to the **Films** table.

---

## 1. `Best_Owned_Quality` — Integer, formula

```python
q = [r.Video_Quality for r in Releases.lookupRecords(Film=$id, Ownership="Owned") if r.Video_Quality]
return max(q) if q else 0
```

## 2. `Best_Known_Quality` — Integer, formula

Every release you've logged, owned or not.

```python
q = [r.Video_Quality for r in Releases.lookupRecords(Film=$id) if r.Video_Quality]
return max(q) if q else 0
```

## 3. `Upgrade_Available` — Reference → Releases, formula

The best disc you don't own that beats what you do own. Empty when you already have the best
logged release. Set the column type to **Reference → Releases** (show column
`Title_On_Release`) *before* pasting the formula.

```python
mine = $Best_Owned_Quality
better = [r for r in Releases.lookupRecords(Film=$id)
          if r.Ownership not in ("Owned", "Sold")
          and (r.Video_Quality or 0) > mine]
return max(better, key=lambda r: r.Video_Quality) if better else None
```

## 4. `Upgrade_Status` — Choice, entered by hand

This is the judgement call, and the reason a formula alone won't do. Values:

| Value | Means |
|---|---|
| `Not assessed` | Haven't looked into what exists. Default. |
| `Best available obtained` | I own the best that exists, and it's fine. |
| `Upgrade exists — not bought` | Something better is out there. `Upgrade_Available` usually names it. |
| `Nothing good exists` | I own the best there is, **and it isn't good enough.** Waiting on a restoration. |
| `Adequate for now` | Not the best, but good enough that I'm not spending money on it. |

`Nothing good exists` is the one that earns its keep. *Gli amori di Ercole* is exactly that case —
your own note says no uncut anamorphic version is believed to exist. That's not a gap in your
collection, it's a gap in the world, and it should read differently from a disc you simply haven't
bought yet. It's also the flag to re-check every couple of years, because boutique labels do
eventually get to these.

## 5. `Upgrade_Notes` — Text

What specifically is wrong with the best available, and what would fix it. "Rider is the best
all-rounder but is cropped on the sides and missing the bathtub shot. Would need a new scan from
the negative, or at minimum an uncropped transfer of the French print."

## 6. `Ceiling_Reached` — Toggle, formula *(optional)*

```python
$Best_Owned_Quality > 0 and $Best_Owned_Quality >= $Best_Known_Quality
```

True means you own the best disc you know about — which says nothing about whether that disc is
any good. Pair it with `Upgrade_Status` to tell the two apart.

---

## Using it

Put a filtered view on your Library page: `Upgrade_Status = "Upgrade exists — not bought"` sorted
by Priority. That's your shopping list, and unlike a wishlist of individual discs it tells you
*why* each one is worth buying.

A second view on `Nothing good exists` is your restoration watchlist. Worth revisiting when a
label announces a peplum boxset.

The film dashboard shows both as badges — green when you've got the best, red when nothing good
exists, amber when there's something to buy, and it names the specific disc when
`Upgrade_Available` finds one.
