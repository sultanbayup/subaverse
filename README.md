# Subaverse

Personal engineering platform built with Hugo and Hextra. Cloud infrastructure, DevOps, Kubernetes, and platform engineering — documented, written about, and showcased at [sultanbp.com](https://sultanbp.com).

## What's here

| Section | Purpose |
|---|---|
| [Blog](https://sultanbp.com/blog) | Engineering posts, troubleshooting writeups, career reflections |
| [Docs](https://sultanbp.com/docs) | Technical reference — cloud, Kubernetes, DevOps, Linux |
| [Projects](https://sultanbp.com/projects) | Portfolio entries with architecture and outcomes |
| [About](https://sultanbp.com/about) | Background and platform purpose |

## Stack

| Component | Technology |
|---|---|
| Static Site Generator | Hugo |
| Theme | Hextra |
| Hosting | GitHub Pages |
| CI/CD | GitHub Actions |
| DNS / CDN | Cloudflare |
| Analytics | GoatCounter |

## Local development

```bash
# Clone the repo
git clone https://github.com/sultanbayup/subaverse.git
cd subaverse

# Install Hugo (extended, v0.147.6+)
# https://gohugo.io/installation/

# Pull theme module
hugo mod tidy

# Start dev server
hugo server
```

Site runs at `http://localhost:1313`.

## Deployment

Every push to `main` triggers a GitHub Actions workflow that builds the site and deploys to GitHub Pages. The custom domain `sultanbp.com` is served through Cloudflare.

No manual deployment steps needed.

## Content

New content follows the archetype pattern:

```bash
hugo new blog/<name>.md
hugo new docs/<subsection>/<name>.md
hugo new projects/<name>.md
```

All new content starts as `draft: true`. Set to `draft: false` when ready to publish.

## License

Content is copyright Sultan Bayu Prasetyo. Code and configuration is MIT.
