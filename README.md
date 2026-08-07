
<<<<<<< HEAD
=======
Plain HTML + CSS. No build step, no dependencies. Edit a file, commit, push, done.

```
index.html          Home / about
research.html       Research threads
publications.html   Papers, preprints, talks
cv.html             CV (plus optional cv.pdf)
personal.html       Non-work page
style.css           All styling — the only file that controls the look
images/             Placeholder images — replace these with your own
.nojekyll           Tells GitHub Pages to serve files as-is
CNAME               Custom domain: kpkong.com
```

---

## 1. Preview locally

From this folder:

```bash
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Refresh after each edit.

---

## 2. Fill in your content

Your name and GitHub username are already filled in throughout. What's left is the
substantive text — bio, research descriptions, real papers, CV entries — all of which is
currently plausible-sounding placeholder about particulate Fe and GP15. Read it before you
publish; none of it is sourced from your actual work.

Work through each page. The HTML has comments marking repeatable blocks —
for example, in `publications.html`:

```html
<!-- ---- COPY THIS BLOCK FOR EACH PAPER ---- -->
```

Copy that `<li class="pub">…</li>` block once per paper and edit the contents.
Same idea for `.cv-entry` in `cv.html` and `.card` in `index.html`.

### Images

`images/` currently holds generated placeholders at the right aspect ratios.
Replace them with real files of the same name and everything keeps working:

| File | Used for | Good size |
|---|---|---|
| `hero.jpg` | Homepage banner | 1800 × 900, wide crop |
| `portrait.jpg` | Your photo | 800 × 1000, vertical |
| `fig1_profiles.png` etc. | Research cards, square crop | 900 × 900 |
| `method1-3.jpg` | Circular method thumbnails | 600 × 600 square |
| `personal*.jpg`, `gallery*.jpg` | Personal page | 900 × 700 / 900 × 600 |

Compress before committing — anything over ~500 KB will make the site feel slow.
[Squoosh](https://squoosh.app) is a good browser-based option.

### CV PDF

Drop your CV into this folder as `cv.pdf`. The download link on `cv.html`
already points at it.

---

## 3. Change the look

All colors and fonts live in the `:root` block at the top of `style.css`:

```css
--ink:   #14262b;   /* body text, dark backgrounds */
--sea:   #2f6f6b;   /* links */
--sage:  #a5cca7;   /* accent — carried over from your Persona site */
--ember: #cb4f00;   /* hover state, eyebrow labels */
--paper: #fbfaf7;   /* page background */
```

Change a value there and it updates everywhere. Same for `--serif` and `--sans`.

---

## 4. Deploy to GitHub Pages

**Option A — user site (recommended).** Gives you `https://philkongo.github.io`.

1. Create a repo named exactly `philkongo.github.io` (your GitHub username, lowercase).
2. Push this folder's contents to the `main` branch:

   ```bash
   git init
   git add .
   git commit -m "Initial site"
   git branch -M main
   git remote add origin git@github.com:philkongo/philkongo.github.io.git
   git push -u origin main
   ```

3. Repo → **Settings → Pages** → Source: *Deploy from a branch*, Branch: `main`, folder `/ (root)`.
4. Wait ~1 minute. Site is live at `https://philkongo.github.io`.

**Option B — project site.** Any repo name works; the site lands at
`https://philkongo.github.io/REPO-NAME/`. Same steps, but the extra path segment
is uglier if you're not using a custom domain.

### First-time SSH setup

GitHub does not accept account passwords for Git operations. The remote above uses SSH,
which needs a key on your machine registered to your account. One-time setup:

```bash
ssh-keygen -t ed25519 -C "kyeongpi@usc.edu"   # press Enter through the prompts
pbcopy < ~/.ssh/id_ed25519.pub                # copies the PUBLIC key to clipboard
```

Paste it at <https://github.com/settings/keys> → **New SSH key**. Confirm it works:

```bash
ssh -T git@github.com     # → "Hi philkongo! You've successfully authenticated..."
```

The first connection asks you to trust `github.com`'s fingerprint — type `yes`.

Never share or commit `~/.ssh/id_ed25519` (no `.pub`) — that's the private half.

**Already ran `git remote add` with the HTTPS URL?** Switch it:

```bash
git remote set-url origin git@github.com:philkongo/philkongo.github.io.git
git remote -v      # confirm both lines start with git@github.com
```

If you'd rather stay on HTTPS, the alternatives are a personal access token
(<https://github.com/settings/tokens>, `repo` scope, used in place of the password) or
`gh auth login` via the GitHub CLI.

---

## 5. Pointing kpkong.com at GitHub

The `CNAME` file in this folder contains `kpkong.com` (the apex domain, no `www`), so
GitHub picks up the domain as soon as you push. Don't delete that file on later commits.

DNS can only point one place at a time, so moving off Persona is a repoint, not a disable.
Do it in this order to minimize downtime.

**Step 1 — get the GitHub site working first** at `philkongo.github.io`. Don't touch DNS
until you've confirmed it looks right.

**Step 2 — claim the domain in GitHub, before touching DNS.** Repo → Settings → Pages →
*Custom domain* → enter `kpkong.com` → Save.

Order matters. GitHub's docs warn that pointing DNS at them *before* claiming the domain
in your repo leaves a window where someone else could host a site on your subdomain.
GitHub also recommends verifying the domain first (Settings → Pages → *Verify domain*,
which has you add a TXT record) so no other GitHub account can ever claim it.

**Step 3 — set DNS records at your registrar.** The apex records are what actually serve
the site. The `www` CNAME is optional but recommended — GitHub will then redirect
`www.kpkong.com` → `kpkong.com` automatically, so the address works either way.

| Host | Type | Value |
|---|---|---|
| `@` | `A` | `185.199.108.153` |
| `@` | `A` | `185.199.109.153` |
| `@` | `A` | `185.199.110.153` |
| `@` | `A` | `185.199.111.153` |
| `www` | `CNAME` | `philkongo.github.io` |

The CNAME value is just `philkongo.github.io` — **no repo name, no `https://`, no
trailing path.**

Optionally add AAAA records on `@` for IPv6 (GitHub recommends keeping the A records
alongside them, given uneven IPv6 adoption):

```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

If your DNS provider supports `ALIAS` or `ANAME` records, you can use a single one on `@`
pointing to `philkongo.github.io` instead of the four A records. Plain `CNAME` is not
valid on an apex domain — that's why the A records exist.

Delete the old Persona/Cargo records at the same time, including any default record your
DNS provider adds automatically.

These values were verified against GitHub's docs on 2026-08-04, but they have changed
historically — worth a glance at
<https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site>
before you commit.

**Step 4 — remove the domain from Persona.** In your Persona site settings, detach the
custom domain. If you skip this, Persona keeps trying to renew an SSL certificate for a
domain it no longer serves, which can produce certificate warnings during the transition.

**Step 5 — enable HTTPS.** Back in GitHub Settings → Pages, tick *Enforce HTTPS*.
The checkbox stays greyed out until GitHub has provisioned a certificate, which happens
automatically once DNS resolves — GitHub says allow up to 24 hours. If it's still greyed
out well past that, remove and re-add the custom domain.

**Step 6 — verify.**

```bash
dig +short kpkong.com                   # → the four 185.199.x.153 addresses
dig +short www.kpkong.com               # → philkongo.github.io, then a GitHub IP
curl -sI https://kpkong.com | head -1   # → HTTP/2 200
```

Propagation is usually minutes but GitHub says allow up to 24 hours.

### If your domain is registered *through* Cargo/Persona

Different situation. You don't have a separate registrar to edit, so either:

- **Transfer the domain out** to a normal registrar (Namecheap, Cloudflare, Porkbun),
  then follow the steps above. Domains are locked for 60 days after registration
  or a previous transfer, so check that first.
- **Or** find the DNS/advanced settings in Cargo's dashboard and set the A records
  there, if they expose that.

Either way, don't cancel the Persona subscription until the domain is safely
transferred — cancelling can release the registration.

---

## Common gotchas

- **404 after pushing.** Make sure `index.html` is at the repo root, not inside a
  subfolder.
- **CSS not loading.** Paths are relative (`href="style.css"`), which works for both
  user and project sites. Don't change them to absolute `/style.css` unless you're
  on a user site.
- **Custom domain resets to blank.** Something deleted the `CNAME` file. Re-add the
  domain in Settings → Pages, then `git pull` so your local copy has it.
- **Changes not showing.** GitHub Pages builds take up to a minute; then hard-refresh
  (Cmd-Shift-R). Check the Actions tab for build failures.
- **`Password authentication is not supported`.** Your remote is still HTTPS. Run the
  `git remote set-url` command in the SSH setup section above.
- **`Permission denied (publickey)`.** The key isn't registered, or `ssh-agent` doesn't
  have it. Run `ssh-add ~/.ssh/id_ed25519`, then `ssh -T git@github.com` to test.
>>>>>>> f4781ea (Update bio and publications)
