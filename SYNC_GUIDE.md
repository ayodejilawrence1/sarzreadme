# README.io Sync Guide

Your documentation is now structured and ready to sync with README.io!

## Quick Start

### Option 1: GitHub Auto-Sync (Recommended)

1. **In README.io Dashboard:**
   - Go to **Project Settings** → **GitHub**
   - Click **Connect GitHub** (if not already connected)
   - Select repository: `ayodejilawrence1/sarzreadme`
   - Set **Docs Path** to: `.readme`
   - Select branch: `v1.0` (or your default branch)
   - Enable **Auto-sync**

2. **Create Categories in README.io:**
   - Go to your docs dashboard
   - Create these categories:
     - **Introduction**
     - **API Reference**
     - **Guides**
     - **Resources**

3. **Push to GitHub:**
   ```bash
   git add .
   git commit -m "Add README.io documentation structure"
   git push origin v1.0
   ```

4. **Sync in README.io:**
   - Go to **Project Settings** → **GitHub**
   - Click **Sync Now** or wait for auto-sync

### Option 2: Manual Import

If auto-sync doesn't work, manually create pages:

1. In README.io, go to the appropriate category
2. Click **+ New Page**
3. Copy content from the corresponding `.readme/*.md` file
4. Paste and save

## Documentation Structure

Your documentation is organized as follows:

```
.readme/
├── README.md              → Main index (optional)
├── overview.md            → Introduction category
├── getting-started.md     → Introduction category
├── authentication.md      → API Reference category
├── qr-code-generation.md → API Reference category
├── invoice-management.md  → API Reference category
├── erp-integration.md    → Guides category
├── error-handling.md     → Guides category
├── best-practices.md     → Guides category
├── support.md            → Resources category
└── changelog.md          → Resources category
```

## Category Mapping

| File | Category | Description |
|------|----------|-------------|
| `overview.md` | Introduction | API overview and features |
| `getting-started.md` | Introduction | Setup and quick start |
| `authentication.md` | API Reference | Auth endpoints |
| `qr-code-generation.md` | API Reference | QR code API |
| `invoice-management.md` | API Reference | Invoice endpoints |
| `erp-integration.md` | Guides | ERP setup guide |
| `error-handling.md` | Guides | Error reference |
| `best-practices.md` | Guides | Best practices |
| `support.md` | Resources | Support info |
| `changelog.md` | Resources | Version history |

## Next Steps

1. ✅ Documentation files created in `.readme/` directory
2. ⏳ Connect GitHub repository in README.io
3. ⏳ Create categories in README.io
4. ⏳ Sync or import pages
5. ⏳ Verify all pages appear correctly

## Need Help?

- README.io Docs: https://docs.readme.com/main/docs/sync-with-github
- Check `.readme/SETUP.md` for detailed instructions

