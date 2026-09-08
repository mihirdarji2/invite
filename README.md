# Digital invite — setup

One folder, published free on GitHub Pages. Each guest opens a link with their own
name on it and sees only the events they are invited to.

```
invite/
  index.html
  guests.json          ← regenerated from your sheet whenever the list changes
  assets/*.webp        ← your six cards, optimised
```

---

## 1. Publish it

1. Go to **github.com** and create a repository. Name it `invite`. Make it **Public**
   (private repos can't use Pages on a free account). Skip the README option.
2. On the empty repo page click **uploading an existing file**, then drag in
   `index.html`, `guests.json` and the whole `assets` folder. Commit.
3. **Settings ▸ Pages**. Under *Source* pick **Deploy from a branch**, branch `main`,
   folder `/ (root)`. Save.
4. Wait about a minute. Your invite is at:

   ```
   https://mihirdarji2.github.io/invite/
   ```

Opening it with no guest in the link shows a holding page, which is correct. To
check the cards actually load, use a real link from the **Invite Links** tab once
you have run the steps below.

## 2. Connect it to the sheet

In Apps Script, add **Invite.gs** as a new script file, then set the first line to
your real address:

```js
const INVITE_BASE = 'https://mihirdarji2.github.io/invite';
```

Run these once, in order:

| Function | What it does |
|---|---|
| `addInviteColumns` | Adds `Invite Name`, `Token`, `Invite Opened` to the Guests tab and gives every guest a token |
| `generateInviteNames` | Turns the long household strings into card-friendly names |
| `exportInviteData` | Writes `guests.json` to your Drive and logs a download link |
| `inviteLinks` | Builds an **Invite Links** tab with every guest's URL and a ready WhatsApp message |

**Read down the `Invite Name` column before sending anything.** It converts
`Bhagwandas Maganbhai Darji & Family Alpesh & Family` into `Bhagwandas Darji & Family`,
which is right most of the time and occasionally not. Correcting a cell by hand is
permanent; `generateInviteNames` only ever fills blanks.

Then download `guests.json` from the link it gives you and replace the one in the repo.

## 3. Sending

The **Invite Links** tab has a ready message per guest. Copy the WhatsApp column
straight into a chat.

Whenever you change who is invited to what, or add guests, run `exportInviteData`
again and replace `guests.json` in the repo. Nothing else changes and links stay valid.

---

## Optional: know when someone opens it

1. In `index.html`, set `TRACKER` to your Apps Script `/exec` URL.
2. In `Code.gs`, add this as the first line inside `doGet(e)`:

   ```js
   if (e && e.parameter && e.parameter.seen) return noteOpened_(e.parameter.seen);
   ```

3. Deploy a new version.

The first time a guest opens their invite, the timestamp lands in the
`Invite Opened` column. Useful for knowing your digital invites actually arrived,
rather than guessing.

---

## Notes

**Sizes.** The six cards were 13.8 MB as PNGs and are 1.2 MB as WebP at 1080px wide,
which is sharper than any phone displays. If you re-export a card from your design
tool, run it through the same conversion or the invite gets slow on a village
connection.

**Tokens.** The link is `?g=G041&t=x7k2`. Without the token the invite refuses to
open, so nobody can walk the list by guessing IDs. Tokens are stored in the sheet
and never change.

**Guests with no events.** Anyone with 0 in all three event columns is left out of
`guests.json` entirely, since there is nothing to invite them to.

**Only a real link opens.** The bare URL, a missing token, a wrong token and an
unknown ID all land on the same quiet holding page with the couple's names and a
line explaining that invitations open from their own link. Nothing about the guest
list is revealed by what the page does, so there is no way to probe it.

That also means there is no general-purpose link to drop in a family group. Every
person needs their own from the **Invite Links** tab.

**Changing the cards.** Replace the `.webp` file in `assets`, keeping the same
filename, and regenerate the matching `-tiny.webp` blur placeholder. The names are
`cover`, `names`, `gaam`, `sangeet`, `wedding`, `family`.

**The QR codes are gone from the Sangeet and Wedding cards.** They were painted out
of the source images and replaced with an "Open location in Maps" button under each
card, which is easier to tap on a phone than pointing a second camera at a screen.
The map links live in the `EVENTS` array near the top of `index.html`, one line each.

**Event dates** also live in that `EVENTS` array. Sangeet is Monday 1 February, taken
from the card itself. Change a date there and both the strip above the card and the
calendar file update together.
