# Adding a Blog Post

1. Choose a publish date and slug, then create a markdown file under `_posts/` named `YYYY-MM-DD-short-title.md`.
2. Paste the front matter template below at the top of the file and edit the fields:

```yaml
---
layout: post
title: "Your Post Title"
author: Gautier Evennou
excerpt: "One sentence teaser for the home page."
tags: [vision, synthetic-data]
paper_url: https://arxiv.org/abs/your-paper
code_url: https://github.com/your_repo
# date: 2025-10-06 # optional override if the filename date is not the publish date
---
```

3. Write the post content in Markdown underneath the front matter. Use standard Markdown for headings, lists, code, etc.
4. Place images in `assets/images/` (create subfolders if helpful) and reference them with `![Alt text]({{ "/assets/images/your-image.png" | relative_url }})`.
5. Preview locally with `bundle exec jekyll serve --livereload` and visit `http://localhost:4000` to check the post.
6. Commit the new post and any assets, then push to publish.

Tip: if you want the post to stay in draft locally, move it to `_drafts/` (create the folder if missing). Jekyll will ignore drafts unless you run `bundle exec jekyll serve --livereload --drafts`.
