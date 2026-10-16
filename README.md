# Khairunnisa Fajriyanti — QA Portfolio + Decap CMS

A static, responsive portfolio with editable content through Decap CMS. No database, Node.js application server, or build step is required for the public site. Portfolio content is stored in `content/portfolio.json`; CMS edits are committed to your GitHub repository, then Netlify redeploys the static site.

## What is included

- `index.html` — public portfolio website, styled in the pastel blue/navy design.
- `content/portfolio.json` — all editable portfolio content and theme values.
- `admin/index.html` — Decap CMS admin application loader.
- `admin/config.yml` — Decap collection and field definitions.
- `assets/uploads/` — destination for any media uploaded through the CMS in future.
- `netlify.toml` — static publishing configuration.

## Recommended no-database deployment

Use **GitHub + Netlify static hosting + Decap CMS with the GitHub backend**. This avoids Supabase and a separate database. Decap writes content changes into GitHub files, and Netlify rebuilds/redeploys the static site after each commit.

### 1. Create the GitHub repository

1. Create a new repository, for example `khairunnisa-qa-portfolio`.
2. Upload the *contents* of this folder to the repository root (so `index.html`, `content/`, and `admin/` are at the root).
3. Make sure your production branch is `main`.
4. Your GitHub account must have write access to this repository, because the Decap GitHub backend writes content commits to it.

### 2. Deploy the website on Netlify

1. Sign in at https://app.netlify.com/ and select **Add new project / Import an existing project**.
2. Connect the GitHub repository.
3. Leave the build command empty and set the publish directory to `.` (the repository root). The included `netlify.toml` already declares `publish = "."`.
4. Deploy. Note the generated site URL, such as `https://your-site-name.netlify.app`.

### 3. Configure GitHub OAuth through Netlify

The CMS login needs an OAuth provider. For a small personal site, use Decap's documented GitHub backend with Netlify's OAuth provider. **Do not use Git Gateway for a new setup**: Netlify now documents Git Gateway as deprecated.

1. In GitHub, open **Settings → Developer settings → OAuth Apps → New OAuth App**.
2. Set **Homepage URL** to your actual Netlify site URL.
3. Set **Authorization callback URL** to `https://api.netlify.com/auth/done`.
4. Create the app, copy its **Client ID**, and generate a **Client Secret**. Keep the secret private.
5. In Netlify, open the deployed project's **Project configuration → Security → OAuth** area.
6. Under Authentication Providers, install/configure **GitHub** using that Client ID and Client Secret.

Official instructions:
- Decap GitHub backend: https://decapcms.org/docs/github-backend/
- Netlify OAuth provider: https://docs.netlify.com/manage/security/secure-access-to-sites/oauth-provider-tokens/
- Decap CMS configuration: https://decapcms.org/docs/configuration-options/

### 4. Update Decap's repository configuration

Edit `admin/config.yml` and replace:

```yaml
backend:
  name: github
  repo: YOUR_GITHUB_USERNAME/YOUR_REPOSITORY
  branch: main
  base_url: https://api.netlify.com
  auth_endpoint: auth
  site_domain: YOUR_SITE_NAME.netlify.app
```

Use your actual GitHub owner/repository and Netlify subdomain. For example, if the repository URL is `https://github.com/yanti/khairunnisa-qa-portfolio`, use `repo: yanti/khairunnisa-qa-portfolio`. `site_domain` should be the hostname only, without `https://` or a trailing slash.

Commit and push the updated `admin/config.yml`. Netlify will redeploy automatically.

### 5. Open the CMS

Go to `https://YOUR_SITE_NAME.netlify.app/admin/`, sign in with the GitHub account that has write access to the repository, and open **Portfolio Website → Portfolio Content**.

You can edit:
- Profile, introduction, email, LinkedIn, GitHub, and CV URL.
- Skill groups and skill tags.
- Projects, testing approach, findings, outcome/status, and learning.
- Testing artifacts and their table rows.
- QA approach steps, work experience, and organizational experience.
- Theme colors and card corner radius.

Save/publish your changes. Decap commits them to the configured branch; Netlify redeploys the website so the public content matches the updated JSON file.

## Local preview

Because this page fetches JSON, do not open `index.html` as a `file://` URL. Run a local HTTP server from the repository root instead:

```bash
python -m http.server 8000
```

Then open http://localhost:8000. The public site will load the checked-in `content/portfolio.json`. The production CMS login uses the configured GitHub/Netlify OAuth flow; local CMS editing/authentication is not enabled by default.

## Content and NDA safety

All project case studies are anonymized. The CCTV/AI case describes application-visible behavior, not AI model evaluation. The Drone GCS section describes discovery and test planning only due to limited access. The ERP workflow outcome is labelled as unknown where follow-up could not be confirmed. Do not upload proprietary screenshots, internal URLs, staff data, customer information, credentials, contracts, or source documents covered by NDA.

## Troubleshooting

- **CMS cannot log in:** check the GitHub OAuth callback URL, the Client ID/Secret configured in Netlify, the repository and branch, and that your GitHub account has write access.
- **CMS loads but content cannot be saved:** verify `repo` and `branch` are correct, and confirm the authenticated GitHub account can push to the repository.
- **Site shows a content-load message:** confirm `content/portfolio.json` is present at the repository root and the site URL is served over HTTP/HTTPS.
- **Changes do not appear immediately:** wait for the Netlify deployment triggered by the CMS commit to finish, then hard-refresh the public site.
- **Image uploads:** Decap uploads to `assets/uploads/`; only publish images you own or have permission to share.

## Notes / limitations

- This is a static-site CMS, not a drag-and-drop page builder. You can edit fields and lists; changing the page structure still requires editing `index.html`.
- Every CMS user using this GitHub backend needs repository write access. For a single-editor portfolio, this is usually fine. Do not grant access to unknown editors.
- Free hosting plans have platform-specific usage limits; this design removes the need for a separate database, but it cannot guarantee that a third-party host will never have an outage.
