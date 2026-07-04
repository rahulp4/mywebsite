# Groundwork

A tiny static site for publishing interactive, from-first-principles technology write-ups.

## Structure

```
index.html                     ← homepage: card grid, reads the ARTICLES list near the top of its <script>
articles/
  ml-fundamentals.html         ← write-up #1
```

Each write-up is a single self-contained HTML file — its own design, its own interactive
demos, its own optional voice narration. The homepage doesn't know or care what's inside
one; it just links to it.

## Adding a new write-up

1. Build the new page the same way this one was built (or ask me to build it) — save it as
   `articles/your-slug.html`.
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
4. Paste both into the `CONFIG` block near the top of **every** file that needs it:
   `index.html` (for the newsletter signup) and each `articles/*.html` (for likes/comments).
   Each article also has its own `ARTICLE_ID` in that same block — give every new article a
   unique one so their comments/likes don't mix.
5. New comments need approval before they're public — approve them in the Supabase Table
   Editor (Table Editor → `comments` → tick `approved`). Change that column's default to
   `true` in the SQL if you'd rather skip moderation.

Until you do this setup, likes/comments/newsletter signup still work visually on screen —
they just aren't saved anywhere, and a small on-page note says so.

## Deploying

Any static host works, free tier is enough:

- **Cloudflare Pages** — drag-and-drop this folder, or connect a GitHub repo. Free custom domain.
- **Netlify** — same idea, drag-and-drop deploy.
- **Vercel** — same idea.
- **GitHub Pages** — push this folder to a repo, enable Pages in settings.

No build command needed — it's all static files. Point the host at this folder and it's live.

## A note for later, once you have several write-ups

Right now the Supabase `CONFIG` block is duplicated at the top of every HTML file — fine
for a handful of pages. If the site grows past ~5–10 write-ups, it's worth pulling that
block into one shared `assets/config.js` file and including it with a single
`<script src="../assets/config.js"></script>` tag instead, so you only ever update the keys
in one place.
