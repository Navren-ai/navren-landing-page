# Hosting the Navren landing page on GitHub Pages

This directory is ready to publish as a static GitHub Pages website. The landing page has no build step and no external runtime dependencies.

## Files GitHub Pages uses

- `index.html` — the production entry point GitHub Pages serves
- `.nojekyll` — tells GitHub Pages to serve the files exactly as provided
- `.github/workflows/deploy-pages.yml` — optional GitHub Actions deployment
- `navren-landing-page.html` — named design source retained for reference
- `navren-landing-page-full-preview.png` — visual QA reference, not required in production

## Recommended deployment method

For this static page, GitHub recommends publishing directly from a branch when no custom build process is required.

Use the **Deploy from a branch** method first. The included GitHub Actions workflow is available if the site later gains a build process or you prefer deployment automation.

---

# Option A: deploy directly from the main branch

## 1. Create the repository

Choose a repository name such as:

- `navren-site`
- `navren-landing-page`
- `navren.ai`

A public repository is the simplest option for GitHub Pages.

### Using GitHub CLI

Run these commands from this `Landing Page` directory:

```bash
git init
git add .
git commit -m "Launch Navren landing page"
git branch -M main
gh repo create navren-site --public --source . --remote origin --push
```

To create the repository under an organization:

```bash
gh repo create ORGANIZATION/navren-site --public --source . --remote origin --push
```

Replace `ORGANIZATION` with the GitHub organization name.

### Using the GitHub website

1. Open <https://github.com/new>.
2. Enter `navren-site` as the repository name.
3. Select **Public**.
4. Do not initialize the repository with a README, license, or `.gitignore` if you already initialized this folder locally.
5. Click **Create repository**.
6. Copy the repository URL.
7. Run:

```bash
git init
git add .
git commit -m "Launch Navren landing page"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/navren-site.git
git push -u origin main
```

## 2. Enable GitHub Pages

1. Open the repository on GitHub.
2. Open **Settings**.
3. In the left sidebar, under **Code and automation**, open **Pages**.
4. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
5. Select the `main` branch.
6. Select the `/(root)` folder.
7. Click **Save**.

GitHub will create a Pages deployment. The default URL normally follows this format:

```text
https://YOUR-USERNAME.github.io/navren-site/
```

If the repository is named `YOUR-USERNAME.github.io`, the default URL is:

```text
https://YOUR-USERNAME.github.io/
```

## 3. Verify the first deployment

Open the repository’s **Actions** tab and find the Pages deployment.

After it succeeds, check:

- The homepage loads without `/index.html` in the URL.
- The navigation links scroll to the correct sections.
- The informational calls to action scroll to the correct product sections.
- Desktop and mobile layouts render correctly.
- The browser console has no errors.


## 4. Publish future changes

After editing the page:

```bash
git add .
git commit -m "Update Navren landing page"
git push
```

Every push to `main` republishes the site automatically.

---

# Option B: deploy with GitHub Actions

Use this method if you want the deployment to be managed by an explicit workflow or if the website later adds a build step.

The workflow is already included at:

```text
.github/workflows/deploy-pages.yml
```

## Enable the workflow deployment source

1. Push this entire directory to the repository.
2. Open **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to **GitHub Actions**.
4. Open the **Actions** tab.
5. Select **Deploy Navren landing page to GitHub Pages**.
6. Run it manually, or push a change to `main`.

The workflow:

1. Checks out the repository.
2. Configures GitHub Pages.
3. Uploads the repository as a static Pages artifact.
4. Deploys the artifact to the `github-pages` environment.

No secret is required for the standard workflow. GitHub supplies the required token and deployment permissions.

---

# Connecting navren.ai

Configure the custom domain only after you control `navren.ai`.

GitHub recommends verifying a custom domain before attaching it to a repository. Domain verification helps prevent another GitHub user from claiming the domain for a Pages site.

## 1. Add the domain in GitHub

1. Open the repository.
2. Open **Settings → Pages**.
3. Under **Custom domain**, enter:

```text
navren.ai
```

4. Click **Save**.

If you publish from a branch, GitHub creates a `CNAME` file in the publishing source. The file contains one line:

```text
navren.ai
```

If you deploy using a custom GitHub Actions workflow, GitHub does not require the repository `CNAME` file; the domain is configured in the Pages settings.

## 2. Configure apex-domain DNS

At the DNS provider for `navren.ai`, create four `A` records for the root host `@`:

| Type | Host | Value |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |

Optional IPv6 records:

| Type | Host | Value |
|---|---|---|
| AAAA | `@` | `2606:50c0:8000::153` |
| AAAA | `@` | `2606:50c0:8001::153` |
| AAAA | `@` | `2606:50c0:8002::153` |
| AAAA | `@` | `2606:50c0:8003::153` |

Alternatively, if the DNS provider supports `ALIAS` or `ANAME` records at the apex, point `@` to:

```text
YOUR-USERNAME.github.io
```

Do not include the repository name in this DNS value.

## 3. Configure www.navren.ai

Create this DNS record:

| Type | Host | Value |
|---|---|---|
| CNAME | `www` | `YOUR-USERNAME.github.io` |

Then add either `navren.ai` or `www.navren.ai` as the custom domain in GitHub Pages. GitHub will redirect between the apex and `www` variant when both are configured correctly.

## 4. Enable HTTPS

After the DNS records resolve:

1. Return to **Settings → Pages**.
2. Wait for the DNS check to complete.
3. Select **Enforce HTTPS**.

DNS changes may take up to 24 hours to propagate. GitHub’s HTTPS certificate can also take time to provision after the records become visible.

## 5. Verify DNS from macOS or Linux

```bash
dig navren.ai +noall +answer -t A
dig navren.ai +noall +answer -t AAAA
dig www.navren.ai +noall +answer -t CNAME
```

The `A` response should contain the four GitHub Pages IPv4 addresses listed above.

## 6. Verify the website

```bash
curl -I https://navren.ai/
curl -I https://www.navren.ai/
```

Confirm:

- The final response is successful.
- HTTPS is active.
- One domain consistently redirects to the preferred canonical domain.
- The `<link rel="canonical">` value remains `https://navren.ai/`.
- The Open Graph asset URL resolves publicly.

---

# Redirecting navrin.ai to navren.ai

GitHub Pages supports one custom domain per Pages site. Use the DNS provider’s URL-forwarding feature, a separate redirect service, or an edge worker to redirect the defensive domain.

The required behavior is:

```text
https://navrin.ai/*  →  https://navren.ai/$1
```

Use a permanent `301` or `308` redirect and preserve the path and query string.

Examples:

```text
https://navrin.ai/              → https://navren.ai/
https://navrin.ai/privacy       → https://navren.ai/privacy
https://navrin.ai/?source=test  → https://navren.ai/?source=test
```

Do not host duplicate page content at both domains. Search engines should index only `navren.ai`.

---


# Production checklist

## Repository

- [ ] `index.html` exists at the publishing root.
- [ ] `.nojekyll` exists.
- [ ] No private notes, tokens, credentials, or unpublished documents are committed.
- [ ] Repository visibility is intentional.
- [ ] Pages deployment succeeds.

## Domain

- [ ] `navren.ai` is verified in GitHub.
- [ ] Custom domain is saved under **Settings → Pages**.
- [ ] Apex DNS records point to GitHub Pages.
- [ ] `www` points to `YOUR-USERNAME.github.io`.
- [ ] HTTPS is enforced.
- [ ] `navrin.ai` permanently redirects to `navren.ai`.

## Website

- [ ] Canonical URL is correct.
- [ ] Open Graph image has been copied to the deployed `/assets/` path.
- [ ] Twitter card image resolves.

- [ ] Privacy and terms links are real.
- [ ] `hello@navren.ai` exists before publication.
- [ ] Mobile layout is tested.
- [ ] Keyboard navigation is tested.
- [ ] Page has no console errors.
- [ ] Lighthouse accessibility and performance checks are reviewed.

---

# Official GitHub documentation

- Publishing source: <https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site>
- Custom domains: <https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site>
- HTTPS: <https://docs.github.com/en/pages/getting-started-with-github-pages/securing-your-github-pages-site-with-https>
- Domain verification: <https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages>
