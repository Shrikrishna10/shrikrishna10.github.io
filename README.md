# Shrikrishna Pandit Website

Personal website built with [Hugo](https://gohugo.io/) and deployed to GitHub Pages.

## Repository Layout

- `content/` - homepage, blog posts, projects, and resume page content.
- `layouts/` - local Hugo templates used to render the site.
- `static/` - files served directly from the site root, including images and PDFs.
- `static/files/resume-profile-image.pdf` - resume PDF linked from the navigation.
- `.github/workflows/deploy.yml` - GitHub Pages build and deploy workflow.

## Local Preview

Install Hugo, then run:

```bash
hugo server
```

Open:

```text
http://localhost:1313
```

## Build

To generate the static site locally:

```bash
hugo
```

This writes the generated site to `public/`.

## Deploy

GitHub Pages is deployed by GitHub Actions. A local build is not required before deploying.

Commit and push changes to `main`:

```bash
git add .
git commit -m "Update website"
git push
```

The workflow installs Hugo, builds the site, and publishes it to GitHub Pages.

## Updating The Resume

Replace:

```text
static/files/resume-profile-image.pdf
```

The navigation points directly to:

```text
/files/resume-profile-image.pdf
```
