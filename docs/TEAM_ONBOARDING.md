# Team Onboarding Guide

## Quick Start for New Team Members

Welcome to the Moraqmen WordPress Theme Sprint Team! This guide will help you get up to speed quickly.

## Environment Setup Checklist

### Step 1: Clone the Repository
```bash
git clone https://github.com/Moraqmen-LLC/moraqmen-wp-theme.git
cd moraqmen-wp-theme
```

### Step 2: Install Local Development Environment
- Download and install [Local by Flywheel](https://localwp.com/)
- Create a new site named "moraqmen-wp-theme"
- Set WordPress version to 6.4 or higher
- Set PHP version to 8.0 or higher

### Step 3: Install Dependencies
```bash
cd themes/moraqmen-wp-theme
npm install
composer install
```

### Step 4: Activate the Theme
1. Open Local by Flywheel and go to Admin
2. Navigate to Appearance > Themes
3. Activate "Moraqmen Theme"

### Step 5: Configure Git SSH
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
# Add the generated key to GitHub Settings > SSH Keys
```

## Useful Commands and Workflows

### Development Commands

**Start watching for changes:**
```bash
npm run start
```

**Build for production:**
```bash
npm run build
```

**Run tests:**
```bash
npm run test
```

**Lint code:**
```bash
npm run lint
```

### Git Workflow

**Create a feature branch:**
```bash
git checkout -b feature/block-name
```

**Commit changes:**
```bash
git add .
git commit -m "feat: Add description of changes"
```

**Push to remote:**
```bash
git push origin feature/block-name
```

**Create a pull request on GitHub and request review**

## File Structure Overview

```
moraqmen-wp-theme/
├── blocks/              # Custom Gutenberg blocks
├── src/                 # Source files (CSS, JS)
├── functions.php        # Theme functions
├── style.css            # Main theme stylesheet
├── theme.json           # Theme configuration
├── docs/                # Documentation
└── package.json         # NPM dependencies
```

## Troubleshooting Common Issues

### Issue: npm modules not installing
**Solution:** Clear npm cache and try again
```bash
npm cache clean --force
rm -rf node_modules package-lock.json
npm install
```

### Issue: Composer dependencies failing
**Solution:** Update Composer and try again
```bash
composer self-update
composer install
```

### Issue: Git SSH not working
**Solution:** Check SSH connection
```bash
ssh -T git@github.com
```

### Issue: Blocks not appearing in editor
**Solution:** Clear WordPress cache
1. Go to Local > Clear Cache
2. Refresh your browser (Ctrl+Shift+R)
3. Go back to WordPress editor

## Getting Help

- **Questions?** Ask in the team Slack channel
- **Bug found?** Create an issue in GitHub
- **Stuck?** Check the documentation or reach out to the tech lead

## Sprint Timeline

**Sprint Dates:** December 20-23, 2025
**Daily Standup:** 5:00 PM EET

Good luck and welcome to the team! 🚀
