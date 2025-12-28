# README.io Setup Guide

This guide will help you sync this documentation to your README.io project at `saz-einvoicing.readme.io`.

## Step 1: Connect GitHub Repository

1. Go to your README.io project dashboard
2. Navigate to **Project Settings** → **GitHub**
3. Click **Connect GitHub** and authenticate
4. Select this repository: `ayodejilawrence1/sarzreadme`
5. Choose the branch: `v1.0` (or `main` if that's your default)

## Step 2: Configure Sync Settings

1. In README.io, go to **Project Settings** → **GitHub**
2. Set the **Docs Path** to: `.readme`
3. Enable **Auto-sync** if you want automatic updates on push

## Step 3: Create Categories in README.io

Before syncing, create these categories in your README.io project:

1. **Introduction** - For Overview and Getting Started
2. **API Reference** - For Authentication, QR Code, Invoice Management
3. **Guides** - For ERP Integration, Error Handling, Best Practices
4. **Resources** - For Support and Changelog

To create categories:
- Go to your README.io dashboard
- Click **+ New Category** in the sidebar
- Create each category listed above

## Step 4: Manual Page Creation (Alternative)

If auto-sync doesn't work, you can manually create pages:

1. For each markdown file in `.readme/`, create a corresponding page in README.io
2. Copy the content from each markdown file
3. Place them in the appropriate categories:
   - `overview.md` → Introduction category
   - `getting-started.md` → Introduction category
   - `authentication.md` → API Reference category
   - `qr-code-generation.md` → API Reference category
   - `invoice-management.md` → API Reference category
   - `erp-integration.md` → Guides category
   - `error-handling.md` → Guides category
   - `best-practices.md` → Guides category
   - `support.md` → Resources category
   - `changelog.md` → Resources category

## Step 5: Verify Sync

After setup:
1. Make a test commit and push to your repository
2. Check README.io to see if pages sync automatically
3. Verify all pages appear in the correct categories

## Troubleshooting

- **Pages not syncing?** Check that the `.readme` directory path is correct in README.io settings
- **Wrong categories?** You may need to manually assign categories after sync
- **Missing content?** Ensure all markdown files are committed and pushed to the repository

