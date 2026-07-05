# Groundwork

A tiny static site for publishing interactive, from-first-principles technology write-ups.

## Structure

```
index.html                     ← homepage: card grid, reads the ARTICLES list near the top of its <script>
assets/
  config.js                    ← Supabase credentials, shared by every page on the site
articles/
  ml-fundamentals.html         ← write-up #1
```

Each write-up is a single self-contained HTML file — its own design, its own interactive
demos, its own optional voice narration. The homepage doesn't know or care what's inside
one; it just links to it.

## Adding a new write-up

1. Build the new page the same way this one was built (or ask me to build it) — save it as
   `articles/your-slug.html`. If it uses likes/comments, include
   `<script src="../assets/config.js"></script>` before its own script, and give it its own
   unique `ARTICLE_ID` — see `assets/config.js` and `articles/ml-fundamentals.html` for the pattern.
2. Open `index.html`, find the `ARTICLES` array near the top of its `<script>` tag, and add
   one entry:

   ```js
   {
     slug:'your-slug',
     title:'Your Title',
     tagline:'One sentence describing it.',
     category:'Distributed Systems',   // any string — new categories get their own filter chip automatically
     accent:'#2D5E8B',                  // any hex color — used for the card's top bar and label
     minutes:20,                        // rough read/explore time
     date:'2026-08-01',                 // YYYY-MM-DD
     path:'articles/your-slug.html'
   }
   ```

3. That's it — no build step. The card, its filter chip, and the count in the header all
   update automatically.

## Likes, comments, and email capture

Every article that has this feature uses the **same Supabase project** — set it up once:

1. Create a free project at [supabase.com](https://supabase.com).
2. Open the SQL editor and run the schema comment block found inside
   `articles/ml-fundamentals.html` — search that file for `SQL SETUP`.
3. Project Settings → API → copy the **Project URL** and **anon public key**.
4. Paste both into **`assets/config.js`** — this is the single, shared source of
   truth for the whole site. The homepage and every article load this one file
   and reuse the same values; you only ever update credentials in this one place.
5. Each article still has its own `ARTICLE_ID`, set separately in that article's
   own `<script>` — give every new article a unique one so their comments/likes
   don't mix.
6. New comments need approval before they're public — approve them in the Supabase Table
   Editor (Table Editor → `comments` → tick `approved`). Change that column's default to
   `true` in the SQL if you'd rather skip moderation.

Until you do this setup, likes/comments/newsletter signup still work visually on screen —
they just aren't saved anywhere, and a small on-page note says so.

## Deploying

Any static host works, free tier is enough. Full steps for GitHub Pages
(the most common path if this repo already lives on GitHub):

1. Repo → **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. **Branch**: `main` — **Folder**: `/ (root)`.
4. **Save**. First deploy can take a couple of minutes; the site then lives
   at `https://<username>.github.io/<repo-name>/`.

This repo includes a `.nojekyll` file at the root. GitHub Pages runs Jekyll
processing on every repo by default — this site doesn't need it (it's
plain static HTML/CSS/JS, not Markdown), so `.nojekyll` tells Pages to skip
that step and serve the files exactly as they are. You can ignore GitHub's
"Add a Jekyll theme" prompt if you see it; it's for sites without their own
design, which doesn't apply here.

**Getting a 404 right after enabling Pages?** In order of likelihood:
1. The Settings → Pages save didn't take — reopen it; a working config
   shows a green "Your site is live at..." banner.
2. Still building — check the repo's **Actions** tab for a "pages build and
   deployment" run (green check = done, refresh the site; red X = open it
   for the error).
3. Wrong branch/folder selected.
4. Repo needs to be public (or the account needs a plan that allows private
   Pages sites).

Other free static hosts, if preferred over GitHub Pages:
- **Cloudflare Pages** — drag-and-drop this folder, or connect the GitHub repo. Free custom domain.
- **Netlify** / **Vercel** — same idea, drag-and-drop or git-connected deploy.

No build command needed anywhere — it's all static files.
