# Profile README setup notes

## 1. Your own github-readme-stats instance on Vercel

The public instance at `github-readme-stats.vercel.app` is shared by everyone and often rate-limited.
The languages card in `README.md` points to the placeholder host `YOUR-STATS-APP.vercel.app`
until you deploy your own instance.

### a. Create a Personal Access Token (PAT)

Use a **fine-grained token** if you only want public stats (recommended):

1. Go to GitHub → Settings → Developer settings → Personal access tokens → **Fine-grained tokens** →
   **Generate new token**.
2. Name it `github-readme-stats` and set an expiry (90 days is fine; set a calendar reminder to rotate it).
3. Repository access: **All repositories**.
4. Repository permissions, all **Read-only**: Commit statuses, Contents, Issues, Metadata, Pull requests.
5. Generate the token and copy it. You won't see it again.

If you want private contributions counted, use a **classic** token with the `repo` and `read:user`
scopes instead. This token can read your private code, so keep it only in Vercel.

### b. Deploy

1. Fork <https://github.com/anuraghazra/github-readme-stats> to your account.
2. Sign in at <https://vercel.com> with GitHub.
3. **Add New… → Project**, then import your fork. Leave the framework preset and build settings as they are.
4. Under **Environment Variables** add:

   | Name | Value | Why |
   |---|---|---|
   | `PAT_1` | the token from step a | required |
   | `WHITELIST` | `mehulp007` | stops other people from using up your token's quota |
   | `CACHE_SECONDS` | `21600` | optional: refresh every 6 hours instead of 24 |
   | `EXCLUDE_REPO` | comma-separated repo names (see section 5) | keeps vendored p5.js code out of the language card |

5. Click **Deploy**. When it finishes, copy the production domain, for example
   `github-readme-stats-abc123.vercel.app`.
6. Check that it works by opening `https://<your-domain>/api?username=mehulp007` in a browser.
   You should see an SVG card.

### c. Swap the placeholder

Replace every `YOUR-STATS-APP.vercel.app` in `README.md` with your domain (the card URL plus one comment):

```bash
sed -i 's/YOUR-STATS-APP\.vercel\.app/<your-domain>/g' README.md
```

Commit and push. To pull upstream fixes later, use **Sync fork** on your fork's GitHub page; Vercel
redeploys automatically.

## 2. Contribution snake

`.github/workflows/snake.yml` runs daily at 00:17 UTC, on manual dispatch, and whenever the workflow
file changes. It writes `github-snake-dark.svg` (purple dots, pink snake) to the `output` branch, which
`README.md` loads from `raw.githubusercontent.com`. If the image is ever missing, run it from
**Actions → Generate contribution snake → Run workflow**.

## 3. Black theme and animated assets

The README is black-themed for every visitor: each image uses its dark variant, whatever GitHub theme
the visitor has chosen. `assets/about.svg` (the animated code card on black) and `assets/divider.svg`
(the moving gradient line) live in this repo, so they don't depend on any outside service. To change
the code card's text, edit the SVG `<tspan>` elements directly; each line fades in with its own
`animation-delay`.

## 4. Placeholders still to fill in

- LinkedIn URL: `YOUR-LINKEDIN-ID` in the Connect section
- Portfolio URL: `YOUR-PORTFOLIO-URL` in the Connect section

## 5. Languages card filters

- The `EXCLUDE_REPO` environment variable on your Vercel instance drops the 2020–21 p5.js class repos.
  Each one contains a vendored copy of the p5 library (~3.6 MB of JavaScript), which would otherwise
  make the card show 68–93% JavaScript. Keeping the list in Vercel rather than the README URL means
  private repo names are never published.
- `hide=jupyter notebook` (in the README URL) hides notebooks, whose size is mostly embedded outputs
  and images rather than code.

Remove either one if you'd rather show the unfiltered numbers.
