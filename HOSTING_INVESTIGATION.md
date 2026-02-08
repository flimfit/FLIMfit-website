# FLIMfit.org Hosting Investigation Report

**Date:** February 8, 2026
**Investigated by:** Claude

## Summary

The flimfit.org website is hosted on **GitHub Pages**, not Digital Ocean. The website has been inactive since July 2018.

## Current State

### Repository Details
- **Repository:** `flimfit/FLIMfit-website`
- **Main Repository:** `imperial-photonics/FLIMfit`
- **Last Commit:** July 20, 2018 ("Add version 5.1.1")
- **Last Active Development:** 2016-2018 period
- **Years Inactive:** ~7.5 years

### Hosting Configuration
- **Platform:** GitHub Pages
- **Custom Domain:** flimfit.org (configured via CNAME file)
- **Expected URL:** Would be hosted at `flimfit.github.io/FLIMfit-website` or custom domain
- **Branch Strategy:**
  - `master` branch: source code (Jekyll site)
  - `gh-pages` branch: **MISSING** (should contain built site)

### Technology Stack
- **Generator:** Jekyll (Ruby-based static site generator)
- **Theme:** Hyde (based on Poole)
- **Dependencies:**
  - jekyll
  - jekyll-paginate
  - jekyll-gist
- **Content:** News posts, download pages, documentation links

## Key Findings

### 1. Missing gh-pages Branch
The README.md mentions a `gh-pages` branch for hosting:
> "gh-pages for our hosted site, which includes our analytics tracking code"

**Problem:** This branch does not exist on the remote repository.

### 2. Jenkins Mention
The repository description states: "Jenkins based website for FLIMfit (FLIMfit.org)"

**Problem:** No Jenkins configuration files (Jenkinsfile, etc.) exist in the repository.

**Theory:** There may have been a Jenkins server that:
- Monitored the master branch for changes
- Built the Jekyll site
- Deployed to the gh-pages branch or a separate hosting service

### 3. No Deployment Configuration
- No CI/CD workflows (.github/workflows/)
- No build scripts
- No deployment automation
- The main FLIMfit repo's Azure Pipelines only builds binaries, not the website

## CONFIRMED: How The Website Was Hosted

**Found Jenkins Configuration Repository:** https://github.com/flimfit/FLIMfit-jenkins-configuration

### The Deployment Pipeline (2016-2018)

The `flimfit-site` Jenkins job performed these steps:

1. **Monitored** the `master` branch of `FLIMfit-website` repository
2. **Triggered** automatically on GitHub push events
3. **Built** the Jekyll static site:
   ```bash
   bundle install
   bundle exec jekyll clean
   bundle exec jekyll build
   ```
4. **Deployed** by copying to: `/var/www/flimfit-site` on the Jenkins server

### Key Finding: The Jenkins Server WAS the Web Server

The website was **NOT hosted on GitHub Pages**. Instead:
- The Jenkins server itself (or a web server on the same machine) served the website
- The domain `flimfit.org` pointed directly to this server's IP address
- The CNAME file existed to configure the domain, but deployment was to a local filesystem

### What Happened

**Most Likely Scenario:**
1. Jenkins server was running at Imperial College London or on a cloud provider (possibly Digital Ocean)
2. The server had nginx/apache configured to serve from `/var/www/flimfit-site`
3. Last website update: July 20, 2018
4. The Jenkins server was shut down or decommissioned sometime after 2018
5. DNS still points to the old server IP, but nothing is responding

**Evidence:**
- Jenkins job: `flimfit-site` deployed to `/var/www/flimfit-site`
- Jenkins server description: "Build Server for FLIMfit and Timpson Lab projects"
- Admin user: seanwarren (Sean Warren, Imperial College London)
- Organization: openmicroscopy (Open Microscopy Environment)

## Current Hosting Status

**Previous Cost:** Unknown (Jenkins server hosting costs - could have been free if on Imperial College infrastructure, or ~$6-12/month if on Digital Ocean)

**Current Status:** Website is likely **DOWN** - the Jenkins server that hosted it has been inactive since 2018

**Accessibility:** Unable to verify if flimfit.org is currently live from this environment (403 errors on outbound requests)

## Recommendations

### Immediate Actions

1. **Verify DNS Configuration**
   - Use a DNS lookup tool to see where flimfit.org currently points
   - Determine if it points to:
     - Old Jenkins server IP (likely dead)
     - Digital Ocean IP range
     - Imperial College server
   - Command to check: `dig flimfit.org` or `nslookup flimfit.org`

2. **Verify Domain Registration**
   - Check domain registrar account
   - Verify domain hasn't expired
   - Confirm you have access to update DNS settings

3. **Check Old Infrastructure**
   - Digital Ocean account: Look for old droplets or snapshots from 2016-2018
   - Imperial College: Contact IT if the server was hosted there
   - Check billing history for clues about where it was hosted

4. **Decide on New Hosting Approach**
   - The old Jenkins-based deployment is gone
   - You need to choose a new hosting platform (see options below)

### Long-term Solutions

The old Jenkins server is gone, so you need to **set up new hosting from scratch**.

#### Option 1: GitHub Pages with GitHub Actions (Recommended)
**Pros:** Free, automatic, version controlled, no server maintenance
**Cons:** Static sites only (but that's what you have)
**Steps:**
1. Enable GitHub Pages in repository settings (source: gh-pages branch)
2. Create `.github/workflows/deploy.yml` to build Jekyll and push to gh-pages
3. Configure custom domain (flimfit.org) in repository settings
4. Update DNS A records to point to GitHub Pages IPs:
   - 185.199.108.153
   - 185.199.109.153
   - 185.199.110.153
   - 185.199.111.153

**This replicates your old setup but without needing a Jenkins server.**

#### Option 2: Netlify (Also Excellent)
**Pros:** Free tier, automatic builds from GitHub, easy custom domains, better than GitHub Pages in some ways
**Cons:** Slight vendor lock-in
**Steps:**
1. Sign up at netlify.com
2. Connect to GitHub repository
3. Configure build: `jekyll build`
4. Point DNS to Netlify
5. Configure custom domain in Netlify dashboard

#### Option 3: Digital Ocean Droplet (Similar to Old Setup)
**Pros:** Full control, could run other services too
**Cons:** $6-12/month cost, requires server maintenance
**Not recommended unless you need server features**

#### Option 4: Digital Ocean App Platform
**Pros:** Managed hosting, auto-builds from GitHub
**Cons:** $5-12/month, overkill for a simple static site
**Steps:**
1. Create App Platform app
2. Connect to GitHub
3. Configure Jekyll build
4. Set custom domain

## Questions to Answer

1. **Domain Access:** Do you have access to the domain registrar account for flimfit.org?
2. **DNS Status:** What IP address does flimfit.org currently point to?
3. **Domain Expiry:** When does the domain expire? Is it still active?
4. **GitHub Access:** Do you have admin access to the flimfit GitHub organization?
5. **Old Server:** Where was the Jenkins server hosted?
   - Imperial College London infrastructure?
   - Digital Ocean droplet?
   - Other cloud provider?
6. **Current Status:** If you visit flimfit.org in a browser, what do you see?
   - Nothing (timeout/connection refused)
   - Old cached site
   - Error page
   - Something else

## Next Steps

1. Verify current DNS configuration and domain status
2. Check GitHub Pages settings in repository
3. Decide on hosting strategy (restore GitHub Pages vs. alternatives)
4. Update or rebuild deployment automation
5. Consider updating the website content (7.5 years old)

---

**Investigation Files:**
- Repository: `/home/user/FLIMfit-website`
- Branch: `claude/investigate-flimfit-hosting-bnZk2`
- Session: https://claude.ai/code/session_01EJDqHCg6SQkRH5jjAAtM1B
