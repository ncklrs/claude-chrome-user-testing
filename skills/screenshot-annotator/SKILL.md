# Screenshot Annotator Skill

This skill provides visual annotation capabilities for screenshots taken during user testing. Annotations help highlight issues, confusion points, and notable elements in test screenshots.

## Purpose

Add visual markers (boxes, highlights, callouts) to screenshots before capturing them. This makes test reports more actionable by clearly showing where issues occurred.

## Annotation Types

### Box
A rectangular border around an element.

| Variant | Border Style | Use Case |
|---------|--------------|----------|
| `error` | 3px solid red | Form errors, broken functionality |
| `confusion` | 3px dashed red | User hesitation, unclear elements |
| `warning` | 3px solid orange | Frustration triggers, warnings |
| `success` | 3px solid green | Task completion, positive elements |
| `info` | 3px solid blue | General callouts |

### Highlight
A semi-transparent overlay on an element.

| Color | Opacity | Use Case |
|-------|---------|----------|
| Yellow | 40% | Draw attention to area |
| Orange | 30% | Frustration trigger match |
| Red | 25% | Critical issue area |

### Callout
A numbered circle positioned near an element, used for referencing multiple issues.

## Color Palette

```
error/confusion: #dc2626 (red-600)
warning:         #f59e0b (amber-500)
success:         #16a34a (green-600)
info:            #2563eb (blue-600)
highlight:       #fbbf24 (amber-400)
```

## How to Add Annotations

Use `browser_evaluate` to inject annotation elements before taking a screenshot. All DOM manipulation must use safe methods (createElement, appendChild, setAttribute).

### Step 1: Get Element Position

First, find the target element and get its bounding box:

```javascript
// Via browser_evaluate
const element = document.querySelector('[data-testid="submit-btn"]');
const rect = element.getBoundingClientRect();
const position = {
  x: rect.left + window.scrollX,
  y: rect.top + window.scrollY,
  width: rect.width,
  height: rect.height
};
return position;
```

### Step 2: Create Annotation Overlay

Create a container div for annotations:

```javascript
// Via browser_evaluate
(function() {
  // Remove existing overlay if present
  const existing = document.getElementById('uta-overlay');
  if (existing) existing.remove();

  // Create overlay container
  const overlay = document.createElement('div');
  overlay.id = 'uta-overlay';
  overlay.style.cssText = 'position:absolute;top:0;left:0;width:100%;height:100%;pointer-events:none;z-index:99999;';
  document.body.appendChild(overlay);
  return true;
})();
```

### Step 3: Add Box Annotation

```javascript
// Via browser_evaluate with parameters
(function(x, y, width, height, type, label) {
  const overlay = document.getElementById('uta-overlay');
  if (!overlay) return false;

  const colors = {
    error: '#dc2626',
    confusion: '#dc2626',
    warning: '#f59e0b',
    success: '#16a34a',
    info: '#2563eb'
  };

  const borderStyle = type === 'confusion' ? 'dashed' : 'solid';
  const color = colors[type] || colors.info;

  // Create box
  const box = document.createElement('div');
  box.className = 'uta-box';
  box.style.cssText = [
    'position:absolute',
    'left:' + x + 'px',
    'top:' + y + 'px',
    'width:' + width + 'px',
    'height:' + height + 'px',
    'border:3px ' + borderStyle + ' ' + color,
    'border-radius:4px',
    'box-sizing:border-box'
  ].join(';');

  // Create label if provided
  if (label) {
    const labelEl = document.createElement('div');
    labelEl.className = 'uta-label';
    labelEl.style.cssText = [
      'position:absolute',
      'top:-26px',
      'left:-3px',
      'background:' + color,
      'color:white',
      'padding:4px 8px',
      'font-size:12px',
      'font-family:system-ui,sans-serif',
      'font-weight:500',
      'border-radius:4px 4px 0 0',
      'white-space:nowrap'
    ].join(';');
    labelEl.textContent = label;
    box.appendChild(labelEl);
  }

  overlay.appendChild(box);
  return true;
})(x, y, width, height, type, label);
```

### Step 4: Add Highlight Annotation

```javascript
// Via browser_evaluate with parameters
(function(x, y, width, height, color) {
  const overlay = document.getElementById('uta-overlay');
  if (!overlay) return false;

  const colors = {
    yellow: 'rgba(251, 191, 36, 0.4)',
    orange: 'rgba(245, 158, 11, 0.3)',
    red: 'rgba(220, 38, 38, 0.25)'
  };

  const highlight = document.createElement('div');
  highlight.className = 'uta-highlight';
  highlight.style.cssText = [
    'position:absolute',
    'left:' + x + 'px',
    'top:' + y + 'px',
    'width:' + width + 'px',
    'height:' + height + 'px',
    'background:' + (colors[color] || colors.yellow),
    'border-radius:2px'
  ].join(';');

  overlay.appendChild(highlight);
  return true;
})(x, y, width, height, color);
```

### Step 5: Add Callout Annotation

```javascript
// Via browser_evaluate with parameters
(function(x, y, number, color) {
  const overlay = document.getElementById('uta-overlay');
  if (!overlay) return false;

  const colors = {
    red: '#dc2626',
    orange: '#f59e0b',
    green: '#16a34a',
    blue: '#2563eb'
  };

  const callout = document.createElement('div');
  callout.className = 'uta-callout';
  callout.style.cssText = [
    'position:absolute',
    'left:' + (x - 12) + 'px',
    'top:' + (y - 12) + 'px',
    'width:24px',
    'height:24px',
    'background:' + (colors[color] || colors.red),
    'color:white',
    'border-radius:50%',
    'display:flex',
    'align-items:center',
    'justify-content:center',
    'font-size:14px',
    'font-weight:bold',
    'font-family:system-ui,sans-serif',
    'box-shadow:0 2px 4px rgba(0,0,0,0.2)'
  ].join(';');
  callout.textContent = String(number);

  overlay.appendChild(callout);
  return true;
})(x, y, number, color);
```

### Step 6: Take Screenshot

After adding annotations, use `browser_take_screenshot` to capture the annotated page.

### Step 7: Clean Up

Remove the overlay after taking the screenshot:

```javascript
// Via browser_evaluate
(function() {
  const overlay = document.getElementById('uta-overlay');
  if (overlay) overlay.remove();
  return true;
})();
```

## Annotation Workflow

### For Manual Annotations (/annotate command)

1. Parse user's element description
2. Use `browser_snapshot` to find the element
3. Get element position via `browser_evaluate`
4. Create overlay and add annotation
5. Take screenshot with `browser_take_screenshot`
6. Clean up overlay
7. Return screenshot path and description

### For Automatic Annotations (during testing)

When the testing agent detects an annotation trigger:

1. **Confusion detected**:
   - Note the element being examined
   - Add dashed red box with label "Confusing"

2. **Error encountered**:
   - Find the error message or invalid field
   - Add solid red box with label "Error"

3. **Frustration trigger matched**:
   - Identify the triggering element
   - Add orange highlight or warning box

4. **Task completed**:
   - Mark the final action element
   - Add green box with label "Complete"

## Best Practices

1. **Label Clarity**: Use short, descriptive labels (2-4 words)
2. **Color Consistency**: Use the standard color palette
3. **Positioning**: Ensure labels don't overlap with important content
4. **Multiple Annotations**: Use callouts with numbers when annotating multiple elements
5. **Clean Up**: Always remove the overlay after screenshots

## Example: Annotating a Confusing Form Field

```markdown
1. User: "I'm confused about this field"

2. Get position:
   browser_evaluate: Get bounding box of the confusing element

3. Create overlay:
   browser_evaluate: Create annotation container

4. Add annotation:
   browser_evaluate: Add dashed red box at position with label "Confusing"

5. Capture:
   browser_take_screenshot: Save as "confusion-form-field.png"

6. Clean up:
   browser_evaluate: Remove overlay

7. Report:
   "Screenshot saved: confusion-form-field.png - User found this field confusing"
```

## Integration with User Testing Reports

Annotated screenshots should be referenced in the testing report:

```markdown
## Usability Issues Found

### Critical: Form Submit Button Hard to Find

![Annotated screenshot](screenshots/confusion-submit-btn.png)

The submit button (annotated in red) was difficult to locate due to
its placement below the fold and low contrast with the background.
```
