# Testing Strategy

## Unit Testing

### JavaScript/React Testing

**Framework:** Jest + React Testing Library

**Test Coverage Target:** >80%

#### Setup
```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom
```

#### Writing Tests
```javascript
import { render, screen } from '@testing-library/react';
import HeroBlock from './HeroBlock';

test('Hero block renders with title', () => {
  const props = {
    title: 'Welcome',
    subtitle: 'Test subtitle'
  };
  render(<HeroBlock {...props} />);
  expect(screen.getByText('Welcome')).toBeInTheDocument();
});
```

#### Running Tests
```bash
# Run all tests
npm run test

# Run with coverage
npm run test -- --coverage

# Watch mode
npm run test -- --watch
```

### PHP Testing

**Framework:** PHPUnit

**Test Coverage Target:** >70%

#### Setup
```bash
composer require --dev phpunit/phpunit
```

#### Running Tests
```bash
./vendor/bin/phpunit
```

## Integration Testing

### Gutenberg Block Integration

**Tools:** Cypress, Playwright

**Test Cases:**
- [ ] Block registers in editor
- [ ] Block controls work
- [ ] Block saves correctly
- [ ] Block renders on frontend
- [ ] Block responds to settings changes

#### Test Script Example
```javascript
describe('Hero Block', () => {
  it('should render hero block in editor', () => {
    cy.visit('/wp-admin/post-new.php?post_type=page');
    cy.get('[data-type="moraqmen/hero"]').click();
    cy.get('[class*="block-editor-block"]')
      .should('contain', 'Hero Block');
  });
});
```

### Theme Integration Testing

- [ ] All blocks work together
- [ ] Theme settings apply correctly
- [ ] Menus display properly
- [ ] Widgets function
- [ ] Footer displays

## Accessibility Testing

### WCAG 2.1 AA Compliance

**Automated Tools:**
- axe DevTools Chrome Extension
- WAVE Browser Extension
- Lighthouse

**Manual Testing Checklist:**
- [ ] Keyboard navigation works
- [ ] Tab order is logical
- [ ] Focus indicators visible
- [ ] Form labels associated with inputs
- [ ] Color contrast >4.5:1 for normal text
- [ ] Images have alt text
- [ ] No audio/video auto-plays
- [ ] Error messages clear
- [ ] Help text available

### Screen Reader Testing

**Tools:** NVDA (Windows), JAWS (Windows), VoiceOver (Mac/iOS)

**Test Cases:**
- [ ] All text readable
- [ ] Images properly labeled
- [ ] Links have meaningful text
- [ ] Form fields labeled
- [ ] Headings hierarchical
- [ ] Lists properly marked
- [ ] Buttons accessible

## Performance Testing

### Lighthouse Audit

**Target Scores:**
- Performance: >90
- Accessibility: >95
- Best Practices: >95
- SEO: >95

**Run Audit:**
```bash
ligthouse https://yoursite.com --view
```

### Core Web Vitals

**Metrics to Monitor:**
- LCP (Largest Contentful Paint): <2.5s
- FID (First Input Delay): <100ms
- CLS (Cumulative Layout Shift): <0.1

### Bundle Size Analysis

```bash
# Analyze bundle size
npm run build -- --analyze

# Check for unused code
npm run lint:css
npm run lint:js
```

## Cross-Browser Testing

### Browser Matrix

| Browser | Version | Mobile | Desktop |
|---------|---------|--------|----------|
| Chrome | Latest | ✓ | ✓ |
| Firefox | Latest | ✓ | ✓ |
| Safari | Latest | ✓ | ✓ |
| Edge | Latest | | ✓ |
| Samsung Internet | Latest | ✓ | |

### Testing Tools

- **BrowserStack** - Real device testing
- **Sauce Labs** - Cross-browser automation
- **Lambdatest** - Quick browser testing

### Test Scenarios
- [ ] Page loads
- [ ] Blocks render
- [ ] Responsive design works
- [ ] Interactions function
- [ ] Forms submit
- [ ] Media displays

## Responsive Design Testing

### Breakpoints to Test

- **320px** - Mobile (iPhone SE)
- **375px** - Mobile (iPhone X)
- **768px** - Tablet (iPad)
- **1024px** - Small Desktop
- **1920px** - Large Desktop

### Test Checklist
- [ ] Content readable at all sizes
- [ ] Navigation accessible
- [ ] Images scale properly
- [ ] No horizontal scrolling
- [ ] Touch targets >48px (mobile)
- [ ] Spacing appropriate

## Testing Timeline

### Sprint Timeline (Dec 20-23, 2025)

**Daily Testing:**
- Morning: Code review + linting
- Afternoon: Block functional testing
- Evening: Integration testing + bug fixes

**Pre-Deployment (Dec 23):**
- Full regression testing
- Performance audit
- Accessibility validation
- Cross-browser testing
- Final QA sign-off

## Continuous Integration

### GitHub Actions Workflow

```yaml
name: Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
      - run: npm install
      - run: npm run lint
      - run: npm run test
      - run: npm run build
```

## Bug Reporting

### Report Template

```
Title: [Component] Brief description

Description:
Clear description of the issue

Steps to Reproduce:
1.
2.
3.

Expected Behavior:

Actual Behavior:

Environment:
- Browser:
- OS:
- Screen size:

Screenshots/Videos:
```

## Test Report Documentation

After each testing phase, document:
- [ ] Tests run
- [ ] Tests passed
- [ ] Tests failed
- [ ] Bugs found
- [ ] Performance metrics
- [ ] Accessibility score
- [ ] Coverage percentage
- [ ] Sign-off
