# 🚀 CLIENT PROJECT QUICK START GUIDE
## When Figma Design is Ready to Develop

**Status:** Production-ready with Figma integration  
**Prerequisites:** Figma design complete + design tokens exported  
**Time to Start:** 2 hours setup + implementation  

---

## 🗓️ PHASE 1: REPOSITORY SETUP (30 minutes)

### Step 1.1: Create GitHub Repository

```bash
# From terminal
cd ~/Projects

# Clone template
git clone https://github.com/khmoraqmen/moraqmen-wordpress-theme.git
cd moraqmen-wordpress-theme

# Rename directory
cd ..
mv moraqmen-wordpress-theme moraqmen-theme-{client-name}
cd moraqmen-theme-{client-name}

# Remove git history
rm -rf .git
git init
```

### Step 1.2: Setup New GitHub Repository

```bash
# On GitHub.com:
# 1. Create new repository: moraqmen-theme-{client-name}
# 2. Do NOT initialize with README

# Back in terminal:
git remote add origin https://github.com/khmoraqmen/moraqmen-theme-{client-name}.git
git branch -M main

# Push initial setup
git add .
git commit -m "Initial: Theme setup for {Client Name}"
git push -u origin main
```

### Step 1.3: Update Project Configuration

**File: package.json**
```json
{
  "name": "moraqmen-theme-{client-name}",
  "version": "1.0.0",
  "description": "WordPress theme for {Client Name}",
  "dependencies": {
    "@moraqmen/blocks": "^1.0.0",
    "tailwindcss": "^3.3.0"
  },
  "devDependencies": {
    "webpack": "^5.0.0",
    "webpack-cli": "^5.0.0",
    "webpack-dev-server": "^4.0.0",
    "@tailwindcss/typography": "^0.5.0"
  },
  "scripts": {
    "dev": "npm run tokens:sync && webpack serve --mode development",
    "build": "npm run tokens:sync && webpack --mode production",
    "tokens:sync": "npm run tokens:convert && npm run generate:theme-json",
    "tokens:convert": "node scripts/convert-tokens.js",
    "generate:theme-json": "node scripts/generate-theme-json.js"
  }
}
```

### Step 1.4: Install Dependencies

```bash
# Install npm packages
npm install

# Install Moraqmen blocks NPM package
npm install @moraqmen/blocks@latest

# Verify installation
npm list | grep moraqmen
# Output: @moraqmen/blocks@1.0.0

# Create scripts directory if needed
mkdir -p scripts

# Commit changes
git add .
git commit -m "chore: Install dependencies for {Client Name}"
git push origin main
```

---

## 🗓️ PHASE 2: DESIGN SYSTEM SETUP (1 hour)

### Step 2.1: Export Design Tokens from Figma

**In Figma:**
```
1. Open design file for {Client Name}
2. Right-click on component library panel
3. Select: "Export design tokens"
4. Save as: design-tokens.json
5. Place file in project root: moraqmen-theme-{client-name}/design-tokens.json
```

**Figma Design Tokens Should Include:**
- ✅ Colors (primary, secondary, neutral)
- ✅ Typography (font sizes, weights)
- ✅ Spacing scale
- ✅ Border radius
- ✅ Shadows (optional)

### Step 2.2: Commit Design Tokens

```bash
# Add tokens to project
cp ~/Downloads/design-tokens.json ./design-tokens.json

# Commit
git add design-tokens.json
git commit -m "chore: Add design tokens for {Client Name}"
git push origin main
```

### Step 2.3: Convert Figma Tokens to Tailwind

**Create: scripts/convert-tokens.js**
```javascript
const fs = require('fs');
const path = require('path');

try {
  const designTokens = require('../design-tokens.json');
  
  const tailwindConfig = {
    content: [
      './blocks/**/*.{js,jsx,twig}',
      './src/**/*.{js,jsx,php}',
      './theme/**/*.{js,jsx,php}',
    ],
    theme: {
      extend: {
        colors: {},
        fontSize: {},
        spacing: {},
        borderRadius: {},
        boxShadow: {},
      },
    },
  };

  // Parse colors
  if (designTokens.colors) {
    Object.entries(designTokens.colors).forEach(([name, color]) => {
      if (typeof color === 'string') {
        tailwindConfig.theme.extend.colors[name] = color;
      }
    });
  }

  // Parse typography
  if (designTokens.typography?.fontSize) {
    Object.entries(designTokens.typography.fontSize).forEach(([size, value]) => {
      tailwindConfig.theme.extend.fontSize[size] = value;
    });
  }

  // Parse spacing
  if (designTokens.spacing) {
    Object.entries(designTokens.spacing).forEach(([size, value]) => {
      tailwindConfig.theme.extend.spacing[size] = value;
    });
  }

  // Parse border radius
  if (designTokens.borderRadius) {
    Object.entries(designTokens.borderRadius).forEach(([name, value]) => {
      tailwindConfig.theme.extend.borderRadius[name] = value;
    });
  }

  // Generate tailwind.config.js
  const config = `const baseConfig = require('@moraqmen/blocks/tailwind.config');

module.exports = {
  ...baseConfig,
  content: ${JSON.stringify(tailwindConfig.content, null, 2)},
  theme: {
    extend: {
      colors: ${JSON.stringify(tailwindConfig.theme.extend.colors, null, 2)},
      fontSize: ${JSON.stringify(tailwindConfig.theme.extend.fontSize, null, 2)},
      spacing: ${JSON.stringify(tailwindConfig.theme.extend.spacing, null, 2)},
      borderRadius: ${JSON.stringify(tailwindConfig.theme.extend.borderRadius, null, 2)},
    },
  },
};
`;

  fs.writeFileSync(path.join(__dirname, '../tailwind.config.js'), config);
  console.log('✅ Tailwind config generated from design tokens');
} catch (error) {
  console.error('❌ Error converting tokens:', error.message);
  process.exit(1);
}
```

### Step 2.4: Generate theme.json

**Create: scripts/generate-theme-json.js**
```javascript
const fs = require('fs');
const path = require('path');

try {
  const tailwindConfig = require('../tailwind.config.js');
  const designTokens = require('../design-tokens.json');
  
  const colors = tailwindConfig.theme.extend.colors || {};
  const fontSizes = tailwindConfig.theme.extend.fontSize || {};
  
  const themeJson = {
    $schema: 'https://schemas.wp.org/wp/6.0/theme.json',
    version: 2,
    settings: {
      color: {
        palette: Object.entries(colors).map(([slug, color]) => ({
          color: color,
          name: slug
            .split('-')
            .map(w => w.charAt(0).toUpperCase() + w.slice(1))
            .join(' '),
          slug: slug,
        })),
      },
      typography: {
        fontSizes: Object.entries(fontSizes).map(([slug, size]) => ({
          size: Array.isArray(size) ? size[0] : size,
          slug: slug,
        })),
      },
    },
    styles: {
      color: {
        background: Object.values(colors)[0] || '#ffffff',
        text: Object.values(colors)[Object.values(colors).length - 1] || '#000000',
      },
    },
  };

  fs.writeFileSync(
    path.join(__dirname, '../theme.json'),
    JSON.stringify(themeJson, null, 2)
  );
  console.log('✅ theme.json generated from Tailwind config');
} catch (error) {
  console.error('❌ Error generating theme.json:', error.message);
  process.exit(1);
}
```

### Step 2.5: Run Token Sync

```bash
# Run token conversion
npm run tokens:sync

# Expected output:
# ✅ Tailwind config generated from design tokens
# ✅ theme.json generated from Tailwind config

# Verify files created
ls -la tailwind.config.js theme.json design-tokens.json

# Commit changes
git add tailwind.config.js theme.json scripts/
git commit -m "chore: Sync design tokens to Tailwind and WordPress"
git push origin main
```

### Step 2.6: Start Dev Server

```bash
# Start webpack dev server with Tailwind
npm run dev

# Expected output:
# webpack serve CLI
# <S> Compiler starting...
# <s> Compiler finished
# Watching for changes...
# Local: http://localhost:8080/
```

---

## 🗓️ PHASE 3: LINEAR WORKSPACE SETUP (30 minutes)

### Step 3.1: Create Project Epic

**In Linear:**
```
Title: Project: {Client Name}
Status: In Progress

Description:
Client: {Client Name}
Deadline: {Date}
Domain: {domain.com}

Deliverables:
- Design system (Tailwind + theme.json)
- 4-6 production blocks
- 2-3 complete pages
- QA testing
- Client handoff

Team:
- Designer: {Name}
- Developer 1: {Name}
- Developer 2: {Name}
- Tech Lead: {Name}
```

### Step 3.2: Create Block Issues

**For each Figma component, create a Linear issue:**

**Template:**
```
Title: [BLOCK] {Component Name}
Status: Todo
Priority: {1-4}
Estimate: 6 hours

Description:
Component: {Figma component name}
Figma Link: {link to component}
Design: {Brief description}
Controls: {Gutenberg controls needed}

Acceptance Criteria:
- [ ] Matches Figma design exactly
- [ ] All Gutenberg controls working
- [ ] Responsive design (mobile/tablet/desktop)
- [ ] Tailwind CSS used (NO SCSS)
- [ ] Lighthouse score > 90
- [ ] Accessibility audit passed (WCAG 2.1 AA)
- [ ] Code reviewed and approved
- [ ] Merged to main branch

Depend On:
- Design system setup
- theme.json synced
```

**Create Issues for Each Component:**
```
[ ] [BLOCK] Hero Section (6h) - Priority 1
[ ] [BLOCK] Feature Cards (5h) - Priority 1
[ ] [BLOCK] Call to Action (4h) - Priority 2
[ ] [BLOCK] Gallery (6h) - Priority 2
[ ] [BLOCK] Testimonials (5h) - Priority 3
[ ] [BLOCK] Stats/Metrics (4h) - Priority 3
```

### Step 3.3: GitHub Integration

**In Linear:**
```
Settings → Integrations → GitHub

Connect:
- Organization: khmoraqmen
- Repository: moraqmen-theme-{client-name}
- Auto-link enabled
- Auto-create branches enabled
```

---

## 🗓️ PHASE 4: FIRST BLOCK DEVELOPMENT

### Step 4.1: Developer Picks First Issue

**In Linear:**
```
1. Click on [BLOCK] Hero Section
2. Status: In Progress
3. Click "Create branch"
4. Linear creates: feature/LINEAR-1-hero-section
```

### Step 4.2: Developer Clones Branch Locally

```bash
# Fetch latest
git fetch origin

# Checkout new branch
git checkout feature/LINEAR-1-hero-section

# Verify dev server still running
npm run dev
```

### Step 4.3: Create Block Files

```bash
# Create block directory
mkdir blocks/hero
cd blocks/hero

# Create files
touch block.json index.js edit.js render.twig

# Copy template from Moraqmen blocks
cp ../../node_modules/@moraqmen/blocks/blocks/hero/block.json .
cp ../../node_modules/@moraqmen/blocks/blocks/hero/edit.js .
```

### Step 4.4: Update for Client (Using Figma)

**Modify: block.json**
```json
{
  "$schema": "https://schemas.wp.org/wp/6.0/block.json",
  "apiVersion": 2,
  "name": "moraqmen/hero",
  "title": "Hero Section",
  "category": "layout",
  "description": "Full-width hero with image and text overlay",
  "attributes": {
    "title": { "type": "string", "default": "Hero Title" },
    "subtitle": { "type": "string", "default": "Subtitle" },
    "buttonText": { "type": "string", "default": "Learn More" },
    "buttonUrl": { "type": "string", "default": "#" },
    "imageUrl": { "type": "string" },
    "imageAlt": { "type": "string" }
  }
}
```

**Create: render.twig (Using Tailwind CSS)**
```twig
{# NO SCSS - Use Tailwind classes only #}
<section class="relative overflow-hidden h-screen">
  {# Background image with overlay #}
  <div class="absolute inset-0 z-0">
    {% if imageUrl %}
      <img 
        src="{{ imageUrl }}" 
        alt="{{ imageAlt }}" 
        class="w-full h-full object-cover"
      />
    {% endif %}
    <div class="absolute inset-0 bg-black opacity-50"></div>
  </div>

  {# Content #}
  <div class="relative z-10 flex items-center justify-center h-full">
    <div class="text-center text-white max-w-2xl mx-auto px-4">
      {% if title %}
        <h1 class="text-5xl font-bold mb-4 leading-tight">
          {{ title }}
        </h1>
      {% endif %}

      {% if subtitle %}
        <p class="text-xl text-gray-200 mb-8">
          {{ subtitle }}
        </p>
      {% endif %}

      {% if buttonText and buttonUrl %}
        <a 
          href="{{ buttonUrl }}" 
          class="inline-block bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-8 rounded-lg transition-colors"
        >
          {{ buttonText }}
        </a>
      {% endif %}
    </div>
  </div>
</section>
```

### Step 4.5: Test in WordPress

```bash
# Make sure dev server running
npm run dev

# In WordPress admin:
# 1. Create new page
# 2. Add block: "Hero Section"
# 3. Fill in title, subtitle, button text
# 4. Upload image
# 5. Publish and view on front-end
# 6. Check responsive design on mobile
```

### Step 4.6: Create GitHub PR

```bash
# Stage changes
git add blocks/hero/

# Commit with Linear reference
git commit -m "feat(LINEAR-1): Develop Hero block

- Created block.json with all controls
- Implemented Gutenberg editor component
- Created responsive Twig template
- Used Tailwind CSS (no SCSS)
- Tested in local WordPress
- Lighthouse score: 95
- Accessibility: WCAG 2.1 AA compliant

Closes LINEAR-1"

# Push to GitHub
git push origin feature/LINEAR-1-hero-section
```

**On GitHub:**
```
1. GitHub detects push
2. Copilot auto-reviews code
3. Creates PR to main
4. Team reviews and approves
5. Merge to main
6. Linear issue auto-closes
```

---

## 🗓️ COMPLETE CHECKLIST - Ready to Start?

```
☐ Figma design file complete and approved by client
☐ Design tokens exported from Figma (JSON)
☐ GitHub repository created for client
☐ npm install completed successfully
☐ npm run tokens:sync completed
☐ tailwind.config.js generated
☐ theme.json generated
☐ npm run dev starts without errors
☐ WordPress admin loads with design system colors
☐ Linear workspace created
☐ Linear issues created for all components
☐ GitHub integration with Linear confirmed
☐ Team members invited to repo
☐ Team familiar with workflow
☐ First block assigned to developer
☐ Development environment tested on all machines

✅ ALL CHECKED? YOU'RE READY TO START DEVELOPMENT!
```

---

## 🎉 TIMELINE FOR FIRST BLOCK

```
MONDAY 2 PM: Developer picks first issue
MONDAY 2-6 PM: Development (4 hours)
MONDAY 6 PM: PR created + Copilot review
TUESDAY 10 AM: Team review + approval
TUESDAY 11 AM: Merge to main
TUESDAY 12 PM: First block deployed 🚀

Total: ~18 hours from design to production
```

---

## 📚 ADDITIONAL RESOURCES

**Files Included in Repository:**
- `docs/ENHANCED_WORKFLOW_ANALYSIS.md` - Full architectural details
- `docs/4_DAY_SPRINT_PLAN.md` - Complete sprint instructions
- `tailwind.config.js` - Auto-generated from Figma tokens
- `theme.json` - Auto-synced with Tailwind config
- `scripts/convert-tokens.js` - Token conversion script
- `scripts/generate-theme-json.js` - Theme.json generation script

**Tailwind CSS References:**
- https://tailwindcss.com/docs
- https://tailwindcss.com/docs/responsive-design
- https://tailwindcss.com/docs/customization

---

**You're all set! Start with a happy client and Figma design. Let's build! 🚀**