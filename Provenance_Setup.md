# Provenance — recording where a file actually came from

The problem you named: some files are your own rips from a disc in your hand, and some are
downloads you can't verify. Both are worth cataloguing, but treating them as equivalent quietly
corrupts every quality comparison you make later.

Two columns fix it.

## `Provenance` — Choice, on Releases

```
Own disc rip
Own capture
Trusted rip
Unverified download
Streaming capture
Unknown
```

| Value | Means |
|---|---|
| **Own disc rip** | You put the disc in the drive. The numbers describe the disc. |
| **Own capture** | VHS, LaserDisc or TV captured by you. Yours, but analogue-limited. |
| **Trusted rip** | Someone else's, but you know its origin and it checks out as untouched. |
| **Unverified download** | Origin unknown. The numbers describe *this file*, nothing more. |
| **Streaming capture** | Off a service. Always re-encoded by definition. |
| **Unknown** | Not yet assessed. |

## `Quality_Is_Estimate` — Toggle, on Releases

Formula, so it maintains itself:

```python
$Provenance in ("Unverified download", "Streaming capture", "Unknown")
```

When this is true, `Video_Quality`, `Video_Bitrate` and `Bits_Per_Pixel` describe the file rather
than the release. That distinction matters most in exactly the place you'd forget it — the
build-sheet picking a video source, or the release dashboard ranking siblings by quality.

## How to handle the unverifiable ones

**Log them.** A poor copy still tells you the film exists in some form, still lets you check scene
availability, and still gives you something to compare against when a real disc turns up. Deleting
it loses information.

**Don't omit the stats — caveat them.** Record what ffprobe reports, then set Provenance so the
figures carry their own warning. An unverified file at 1,100 kb/s is genuinely useful information:
it tells you not to bother with it when something better appears.

**Use the checker in the workbench.** Paste the ffprobe report and it flags the giveaways: MPEG-2
is what a DVD holds, so H.264 at 720×576 means an encoder was involved; AC-3 or DTS is what discs
carry, so AAC means the audio was re-encoded too; missing chapters usually mean the file passed
through something after MakeMKV.

**Write down what you do know** in `Comparison_Notes` — where it came from, what it claims to be,
and what still needs confirming. In two years that note is the difference between re-researching
the file and trusting your earlier self.

## The one that catches people out

A re-encode of a *good* master can look better than a straight rip of a *bad* one. Bitrate and
bits-per-pixel measure the encode, not the transfer underneath. So when an unverified file looks
sharper than your disc, the honest reading is usually that it came from a better source — which
makes it a lead worth chasing rather than a release worth keeping.

That's the case to record in `Upgrade_Notes`: something better exists, and here's the evidence.
