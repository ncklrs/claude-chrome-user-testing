---
description: Add visual annotations (boxes, highlights, callouts) to the current page and take a screenshot. Use during user testing to highlight issues or confusion points.
---

# Annotate Command

Add visual annotations to the current browser page and capture an annotated screenshot.

## Arguments

Parse the following from `$ARGUMENTS`:

- `--element <selector>` (required): CSS selector or description of the element to annotate
- `--type <type>` (optional): Annotation type - `box`, `highlight`, or `callout` (default: box)
- `--color <color>` (optional): Color - `red`, `orange`, `green`, `blue` (default: red)
- `--style <style>` (optional): Border style - `solid` or `dashed` (default: solid)
- `--label <text>` (optional): Label text to display above the annotation
- `--number <n>` (optional): Callout number (for callout type)
- `--filename <name>` (optional): Screenshot filename (default: annotated-{timestamp}.png)

## Prerequisites

This command requires:
1. Claude running with `--chrome` flag
2. A page already loaded in the browser

If no page is loaded, inform the user:
```
No page is currently loaded. Navigate to a URL first with:
  browser_navigate to https://example.com
```

## Annotation Flow

### Step 1: Find Target Element

Use `browser_snapshot` to get the current page accessibility tree. Find the element matching the user's description or selector.

If using a CSS selector:
- Try to match directly
- If not found, suggest alternatives

If using a description (e.g., "submit button"):
- Search the snapshot for matching elements
- Look for buttons, links, or inputs with matching text/labels

### Step 2: Get Element Position

Use `browser_evaluate` to get the element's bounding box:

```
Get the bounding rectangle of the element matching: [selector]
Return: { x, y, width, height } in page coordinates
```

### Step 3: Create Annotation Overlay

Use `browser_evaluate` to create the annotation container:
- Create a div with id "uta-overlay"
- Position it absolutely over the page
- Set pointer-events to none
- Set z-index to 99999

### Step 4: Add Annotation

Based on the `--type` argument:

**Box** (default):
- Create a div with colored border
- Position at element coordinates
- Add label if provided
- Use dashed border if `--style dashed`

**Highlight**:
- Create a div with semi-transparent background
- Position at element coordinates
- No label for highlights

**Callout**:
- Create a numbered circle
- Position at top-left corner of element
- Use the `--number` value

### Step 5: Take Screenshot

Use `browser_take_screenshot` with:
- Filename from `--filename` or generate timestamp-based name
- Full page or viewport based on element visibility

### Step 6: Clean Up

Use `browser_evaluate` to remove the annotation overlay.

### Step 7: Report Result

Output:
```
Screenshot saved: [filename]
Annotation: [type] on "[element description]"
Label: [label if provided]
```

## Examples

### Basic Box Annotation
```
/annotate --element "#submit-btn" --label "Hard to find"
```
Creates a red solid box around the submit button with label "Hard to find".

### Dashed Confusion Box
```
/annotate --element ".price-display" --style dashed --label "Confusing"
```
Creates a red dashed box (indicating confusion) around the price display.

### Warning Highlight
```
/annotate --element "form.checkout" --type highlight --color orange
```
Creates an orange semi-transparent highlight over the checkout form.

### Success Annotation
```
/annotate --element "button:contains('Complete')" --color green --label "Task Complete"
```
Creates a green box around the complete button.

### Multiple Callouts
```
/annotate --element "#step1" --type callout --number 1 --color red
/annotate --element "#step2" --type callout --number 2 --color red
/annotate --element "#step3" --type callout --number 3 --color red
```
Creates numbered callouts for sequential issues.

### Custom Filename
```
/annotate --element ".error-message" --label "Validation Error" --filename checkout-error.png
```
Saves the annotated screenshot with a specific filename.

## Error Handling

### Element Not Found
```
Could not find element matching: [selector/description]

Try:
- Use browser_snapshot to see available elements
- Check if the element is visible on the current page
- Use a more specific CSS selector
```

### No Browser Session
```
No browser session active. Start one with:
  claude --chrome

Then navigate to a page:
  browser_navigate to https://example.com
```

### Annotation Failed
```
Failed to add annotation. The element may be:
- Hidden or off-screen
- Inside an iframe (not currently supported)
- Dynamically loaded (wait for it to appear)
```

## Integration with User Testing

During a `/user-test` session, annotations are added automatically at key moments:

| Trigger | Type | Color | Style | Label |
|---------|------|-------|-------|-------|
| Confusion pause | box | red | dashed | "Confusing" |
| Form error | box | red | solid | "Error" |
| Frustration trigger | highlight | orange | - | Trigger name |
| Task complete | box | green | solid | "Complete" |

You can also add manual annotations during testing by using this command directly.

## Output Format

After successful annotation:

```
Annotated Screenshot
────────────────────
File: screenshots/annotated-1704567890.png
Element: #submit-btn (Submit Order button)
Type: box (solid red)
Label: "Hard to find"

The screenshot has been saved with the annotation visible.
```
