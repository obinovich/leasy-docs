# Seed Scripts Documentation

This guide explains how to use the seed scripts to populate the Leasy database with initial data.

## Overview

| Script | Purpose | Safe to Re-run? |
|--------|---------|-----------------|
| `seedAdmin.js` | Create default admin account | ✅ Yes (skips if exists) |
| `seedContent.js` | Create static pages, site settings, job listings | ✅ Yes (skips if exists) |

---

## Running Seed Scripts

### Prerequisites

1. **MongoDB must be running**:
   ```bash
   sudo mongod --dbpath /data/db/
   ```
   Or use your system's MongoDB service.

2. **Node.js dependencies installed**:
   ```bash
   cd leasy-backend
   npm install
   ```

3. **.env configured** with `MONGO_URI` (or use default `mongodb://localhost:27017/leasy`).

---

## Individual Scripts

### seedAdmin.js — Create Admin Account

Creates a default admin user for the admin portal (separate from regular user accounts).

**Usage:**
```bash
cd leasy-backend
node scripts/seedAdmin.js
```

**Output:**
```
Connecting to MongoDB...
Connected to MongoDB
Admin account created: admin@leasy.com
Password: Admin123!
```

**Default Credentials:**
- Email: `admin@leasy.com`
- Password: `Admin123!`

**Safe?** ✅ Yes — skips if admin account already exists.

**What it creates:**
- One user with `isAdmin: true` flag
- Email verified automatically
- Ready to log into admin portal

---

### seedContent.js — Create Static Pages & Settings

Populates the database with site settings, published static pages, and sample job listings.

**Usage:**
```bash
cd leasy-backend
node scripts/seedContent.js
```

**Output:**
```
Connecting to MongoDB...
Connected.

✓ Site settings already exist — skipping.
✓ Static pages: 9 created, 0 already existed.
✓ Job listings: 3 created.

Done! Content seeded successfully.
```

**Safe?** ✅ Yes — skips existing records by slug/key.

**What it creates:**

#### Site Settings (singleton)
- **Key**: `global`
- **Brand**: Leasy, tagline, contact info (email, phone, address)
- **Social Links**: Facebook, Twitter, Instagram, LinkedIn
- **Footer**: Copyright text, footer navigation links
- **SEO Defaults**: Meta title, description, OG image

#### Static Pages (9 total, all published)
1. **About** (`/about`) — Company mission, values, team
2. **Help Centre** (`/help`) — Guides for renters & landlords
3. **FAQ** (`/faq`) — Common questions with answers
4. **Terms of Service** (`/terms`) — Legal T&Cs
5. **Privacy Policy** (`/privacy`) — GDPR-compliant privacy statement
6. **Safety & Trust** (`/safety`) — Fraud prevention, verification info
7. **Contact Us** (`/contact`) — Support email, phone, office address
8. **Accessibility** (`/accessibility`) — WCAG 2.1 AA compliance statement
9. **Cookies** (`/cookies`) — Cookie usage policy

#### Job Listings (3 sample jobs, all published)
1. **Senior Full-Stack Engineer** (£75k–£95k, London Hybrid)
2. **Product Designer** (£55k–£70k, Remote UK)
3. **Customer Support Specialist** (£28k–£34k, London On-site)

---

## Running All Seeds Together

To seed the entire database in one go:

```bash
cd leasy-backend

# Create admin user
node scripts/seedAdmin.js

# Create content
node scripts/seedContent.js
```

Both scripts are safe to re-run and will skip existing data.

---

## Modifying Seed Content

### To update static pages:

Edit `scripts/seedContent.js` in the `PAGES` array:

```javascript
{
  slug: 'about',
  title: 'About Us',
  contentFormat: 'markdown',  // or 'html'
  status: 'published',        // or 'draft'
  publishedAt: new Date(),
  sortOrder: 1,              // Display order
  showInNavbar: true,        // Show in navbar?
  showInFooter: true,        // Show in footer?
  metaTitle: '...',
  metaDescription: '...',
  content: `# Your content here...`
}
```

Then re-run:
```bash
node scripts/seedContent.js
```

### To update job listings:

Edit the `JOBS` array in `scripts/seedContent.js`:

```javascript
{
  title: 'Job Title',
  department: 'Engineering',
  location: 'London',
  type: 'full-time',        // full-time, part-time, contract, internship
  status: 'published',      // draft, published, closed
  publishedAt: new Date(),
  description: '...',
  requirements: '...',
  salary: '£70,000 – £90,000',
  applicationUrl: 'mailto:careers@leasy.com?subject=...'
}
```

Then re-run:
```bash
node scripts/seedContent.js
```

---

## Accessing Seeded Content

### Via Public API

All published content is available at `/api/content`:

```bash
# Get site settings
curl http://localhost:5000/api/content/settings

# Get all published pages
curl http://localhost:5000/api/content/pages

# Get a specific page by slug
curl http://localhost:5000/api/content/pages/about

# Get all published jobs
curl http://localhost:5000/api/content/jobs
```

### Via Admin Portal

1. Log in at `http://localhost:3005` with admin credentials.
2. Go to **Content** section:
   - **Site Settings** — Edit brand, contact, social links, SEO
   - **Static Pages** — View, edit, publish/draft pages
   - **Job Listings** — View, edit, close jobs

### Via Frontend

- View static pages at `/about`, `/faq`, `/terms`, `/privacy`, etc.
- View careers page (job listings) at `/careers`

---

## Troubleshooting

### "Connecting to MongoDB..." hangs

MongoDB is not running. Start it in a separate terminal:
```bash
sudo mongod --dbpath /data/db/
```

### "Cannot find module 'mongoose'"

Dependencies not installed. Run:
```bash
cd leasy-backend
npm install
```

### Seed runs but creates nothing

Check that the database connection is working. Run:
```bash
mongod --version && mongo --eval "db.version()"
```

If that fails, MongoDB is not installed. Installation instructions:
- **macOS**: `brew install mongodb-community`
- **Ubuntu**: `sudo apt-get install -y mongodb`
- **Windows**: [Download MongoDB](https://www.mongodb.com/try/download/community)

### "Site settings already exist"

The settings are already in the database. To update them:
1. Edit `DEFAULT_SETTINGS` in `seedContent.js`
2. Manually delete the old settings from the database and re-run the script, OR
3. Use the admin portal to edit settings directly (preferred)

---

## Notes

- Seed scripts are **idempotent** — safe to run multiple times.
- Data is inserted based on unique keys (`email` for users, `slug` for pages, `title` for jobs).
- To reset the database entirely, use your MongoDB client to drop the `leasy` database:
  ```bash
  mongo leasy --eval "db.dropDatabase()"
  ```
  Then re-run all seed scripts.

---

*Happy seeding!*
