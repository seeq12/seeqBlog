# Contributing to the Seeq Engineering Blog

Thank you for your interest in contributing to the Seeq Engineering Blog! This guide will help you write and submit blog posts.

## Content Guidelines

### Topics We Cover

- Software architecture and design patterns
- Performance optimization techniques
- DevOps, CI/CD, and infrastructure
- Data science and analytics
- Real-world problem solving and case studies
- Engineering culture and best practices

### Writing Style

- **Technical but accessible**: Write for a technical audience, but explain complex concepts clearly
- **Practical focus**: Include code examples, diagrams, and real-world applications
- **Professional tone**: Maintain a professional, informative voice
- **Original content**: Share unique insights and experiences from Seeq projects

## How to Submit a Blog Post

### 1. Create Your Post

Create a new file in `_posts/` with the naming convention:
```
YYYY-MM-DD-title-with-hyphens.md
```

Example: `2025-12-05-optimizing-time-series-queries.md`

### 2. Add Front Matter

Start your post with YAML front matter:

```yaml
---
layout: post
title: "Your Post Title"
date: YYYY-MM-DD
categories: [category1, category2]
author: "Your Name"
tags: [tag1, tag2, tag3]
---
```

### 3. Write Your Content

- Use Markdown formatting
- Include code blocks with syntax highlighting:
  ````markdown
  ```python
  def example():
      return "Hello, World!"
  ```
  ````
- Add images to `assets/images/` and reference them:
  ```markdown
  ![Alt text]({{ '/assets/images/your-image.png' | relative_url }})
  ```

### 4. Preview Locally

Before submitting, test your post locally:

```bash
bundle install
bundle exec jekyll serve
```

Visit `http://localhost:4000/seeqBlog` to preview.

### 5. Submit a Pull Request

1. Create a new branch:
   ```bash
   git checkout -b post/your-post-title
   ```

2. Add your post and commit:
   ```bash
   git add _posts/YYYY-MM-DD-your-post.md
   git commit -m "Add blog post: Your Post Title"
   ```

3. Push and create a pull request:
   ```bash
   git push origin post/your-post-title
   gh pr create --title "Blog Post: Your Post Title" --body "Description of your post"
   ```

## Post Template

Here's a template to get you started:

```markdown
---
layout: post
title: "Your Compelling Title"
date: YYYY-MM-DD
categories: [category]
author: "Your Name"
tags: [tag1, tag2]
---

Brief introduction paragraph that hooks the reader and explains what they'll learn.

## Background

Provide context for the problem or topic.

## The Challenge

Describe the specific problem or challenge.

## Our Approach

Explain your solution or methodology.

### Implementation Details

Include code examples and technical details.

```language
// Code example
```

## Results

Share outcomes, metrics, or lessons learned.

## Conclusion

Summarize key takeaways and next steps.

---

*Questions or feedback? Contact [your-email@seeq.com]*
```

## Review Process

1. **Technical Review**: Another engineer will review for technical accuracy
2. **Editorial Review**: Content will be reviewed for clarity and style
3. **Approval**: Once approved, your post will be merged and published

## Best Practices

- **Proofread**: Check for spelling and grammar errors
- **Test code**: Ensure all code examples work
- **Optimize images**: Compress images before adding them
- **Link sources**: Provide references and citations where appropriate
- **SEO friendly**: Use descriptive titles and meta descriptions

## Questions?

Reach out to the engineering team or open an issue for questions about contributing.

Thank you for sharing your knowledge with the community!
