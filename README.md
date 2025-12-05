# Seeq Engineering Blog

This repository hosts the Seeq technical blog, powered by GitHub Pages and Jekyll.

## Site URL

🌐 [https://seeq12.github.io/seeqBlog](https://seeq12.github.io/seeqBlog)

## Local Development

### Prerequisites
- Ruby 2.7+
- Bundler
- Jekyll

### Setup
```bash
# Install dependencies
bundle install

# Run local server
bundle exec jekyll serve

# Visit http://localhost:4000/seeqBlog
```

## Adding a New Blog Post

1. Create a new file in `_posts/` with the format: `YYYY-MM-DD-title.md`
2. Add front matter:
```yaml
---
layout: post
title: "Your Post Title"
date: 2025-12-05
categories: [engineering, tutorial]
author: "Your Name"
---
```
3. Write your content in Markdown
4. Commit and push to main branch
5. GitHub Pages will automatically build and deploy

## Project Structure

```
seeqBlog/
├── _config.yml          # Site configuration
├── _posts/              # Blog posts
├── _layouts/            # Page layouts
├── _includes/           # Reusable components
├── assets/              # CSS, JS, images
├── _authors/            # Author profiles
├── index.md             # Home page
└── README.md            # This file
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on writing and submitting blog posts.

## License

Copyright © 2025 Seeq Corporation. All rights reserved.
