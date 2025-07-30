# Installation Notes: Quartz Setup for `civil-society-garden`

## 1. Environment Setup

Ensure you're inside the `civil-society-garden/` directory.

### Configure with `mise`

Activate mise
add mise.local.toml file
include node v 22 in the toml file as a tool

Verify Node version:

```bash
node -v  # should be v22.x.x
```

---

## 2. Initialize Quartz and configure git

### Clone and Initialize Quartz

```bash
git clone https://github.com/jackyzha0/quartz.git
cd quartz
npm i
npx quartz create
cd ..
```

Then, I've setup git branches as follows
* my github repo is called civil-society-garden and will deploy to https://amanahuja.me/civil-society-garden/ using github pages.  
* the main branch should be called `main` on origin. The github action can deploy the site from the main branch.
* there's a quartz-specific branch `origin/v4`, which can occasionally fetch updates from upstream (https://github.com/jackyzha0/quartz.git) i.e. `upstream/v4`
* local development and testing on a branch called `dev-local`. 

```bash
git remote add upstream https://github.com/jackyzha0/quartz.git
git remote add origin git@github.com:<your-username>/civil-society-garden.git
git checkout -b main
```

## 4. Local Development

From the `quartz/` directory:

```bash
npx quartz build --serve
```

Customize `quartz.config.ts` and `quartz.layout.ts` as needed.

Quartz sync vs manual git
* `npx quartz sync` may not work with this setup, needs to be verified. 

## 5. Hosting / Deployment with GitHub Pages

See https://quartz.jzhao.xyz/hosting

In `quartz/quartz.config.ts`:

```ts
baseUrl: "https://amanahuja.me/civil-society-garden/"
```

in `.github/workflows/deploy.yml`:

```yaml
on:
  push:
    branches:
      - main
```

In Github project settings: 
* enable GitHub Pages (Repo → Settings → Pages) 
* Set `amanahuja.me/civil-society-garden` under **Settings → Pages → Custom Domain**.
* DNS Setup: For subdomain (e.g., `civil-society-garden.amanahuja.me`), add CNAME to `amanahuja.github.io`

## 6. Pulling Updates from Quartz

To update Quartz while preserving your customizations:

```bash
# Fetch updates from Quartz upstream
git checkout v4
git fetch upstream/v4

# OR Create a temporary branch to merge updates
git checkout -b temp-quartz-update
git subtree pull --prefix=quartz quartz-upstream v4 --squash

# Review changes and merge back to your working branch
git checkout dev-local
git merge temp-quartz-update
git branch -d temp-quartz-update
```

Note: This approach allows you to customize Quartz files while still receiving updates.

## Known Caveats

- GitHub Pages doesn't support trailing slashes (`/index.html` vs. `/`).
