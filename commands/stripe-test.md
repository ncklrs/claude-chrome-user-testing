---
description: Test Stripe checkout flows with any persona using official test cards. Simulates payment experiences with realistic user behavior and generates payment-specific reports.
---

# Stripe Checkout Test Command

Test Stripe payment flows with persona-based user simulation. Uses official Stripe test cards to simulate various payment scenarios.

## Arguments

Parse the following from `$ARGUMENTS`:

- `--url <url>` (required): Checkout or product page URL
- `--persona <id>` (optional): Persona to use (default: impulse-buyer)
- `--gender <m|f|n>` (optional): Gender variant
- `--card <scenario>` (optional): Test card scenario (default: success)
- `--email <email>` (optional): Email for checkout (default: test@example.com)
- `--tasks <tasks>` (optional): Tasks to perform before checkout

## Test Card Scenarios

| Scenario | Description | Use Case |
|----------|-------------|----------|
| `success` | Payment succeeds | Happy path testing |
| `decline` | Generic decline | Error handling |
| `insufficient` | Insufficient funds | Specific decline message |
| `lost` | Lost card | Specific decline message |
| `stolen` | Stolen card | Specific decline message |
| `expired` | Expired card | Expiry validation |
| `cvc-fail` | CVC check fails | CVC validation |
| `processing-error` | Processing error | Server error handling |
| `3ds-required` | 3D Secure required | Authentication flow |
| `3ds-optional` | 3D Secure optional | Optional auth handling |
| `fraud-high` | High fraud risk | Radar blocking |
| `visa-debit` | Visa debit card | Debit card acceptance |
| `mastercard` | Mastercard | Card brand testing |
| `amex` | American Express | Amex (4-digit CVC) |

## Pre-Flight Checklist

Before testing:

1. **Chrome Integration**: Verify `claude --chrome` is active
2. **URL Accessible**: Navigate to target URL
3. **Persona Loading**: Load specified persona (default: impulse-buyer)
4. **Test Card Ready**: Load card data from `skills/stripe-checkout/test-cards.json`

## Testing Flow

### Phase 1: Navigation to Checkout

If `--tasks` specified, complete those first:
```
"Okay, I need to [task] before I can checkout..."
[Complete pre-checkout tasks]
"Alright, now let's pay for this."
```

If URL is already checkout page, proceed directly.

### Phase 2: Checkout Page Assessment

Use `browser_snapshot` to assess the checkout:

1. **Identify Checkout Type**
   - Stripe Hosted (checkout.stripe.com)
   - Stripe Elements (embedded iframes)
   - Custom integration

2. **Check Express Options**
   - Apple Pay button?
   - Google Pay button?
   - Other express checkout?

3. **Review Required Fields**
   - Email
   - Shipping address
   - Billing address
   - Card details

4. **Persona Narration**
   - React to what you see
   - Note trust signals (SSL, Stripe badge)
   - Comment on complexity

### Phase 3: Fill Non-Payment Fields

Fill standard fields in-character:
- Email: Use test@example.com
- Name: Use persona's name
- Address: Use test address if required

```
"Let me put in my email... [types]"
"Shipping address... just my usual."
```

### Phase 4: Payment Details

Auto-fill card details from the specified scenario:

```
"Now for the payment part..."
[Auto-fill card number, expiry, CVC]
"Okay, card details are in."
```

Use these defaults unless testing specific scenarios:
- Expiry: 12/34
- CVC: 123 (or 1234 for Amex)
- ZIP: 12345

### Phase 5: Submit Payment

Click the pay button and wait for response:

```
"Alright, here goes... [clicks Pay]"
[Wait for response]
```

Handle outcomes:
- **Success**: Note confirmation, order number
- **Decline**: Note error message clarity
- **3D Secure**: Complete authentication flow
- **Error**: Note how error is displayed

### Phase 6: Post-Payment

Document the outcome:
- Confirmation page content
- Order confirmation number
- Receipt email (if immediate)
- Next steps clarity

## Report Format

Generate a report with Payment Experience section:

```markdown
# Stripe Checkout Test Report

## Session Overview
- **Persona**: [Name] ([ID])
- **URL Tested**: [URL]
- **Test Card**: [scenario]
- **Date**: [timestamp]

## Payment Experience

### Checkout Summary
- **Checkout Type**: [Hosted/Elements/Custom]
- **Express Options**: Apple Pay [✓/✗], Google Pay [✓/✗]
- **Guest Checkout**: [Available/Required Account]
- **Card Scenario**: [scenario] ([outcome])
- **3D Secure**: [Triggered/Not Triggered]
- **Result**: [Success/Declined/Error]

### Flow Timeline
| Step | Time | Notes |
|------|------|-------|
| Reached checkout | 0:00 | [First impression] |
| Filled form | 0:XX | [Any issues] |
| Submitted | 0:XX | [Response time] |
| Confirmation | 0:XX | [What was shown] |

### Friction Points
[Issues that slowed or frustrated payment]

### Positives
[Things that made payment smooth]

### Security Perception
[How secure did checkout feel?]

## Usability Issues

### Critical
[Payment-blocking issues]

### Major
[Significant friction]

### Minor
[Small improvements]

## Persona Insights
[What this persona specifically struggled with or appreciated]

## Recommendations
[Prioritized improvements]
```

## Example Usage

### Basic Success Test
```
/stripe-test --url https://shop.example.com/checkout
```

### Test Decline Handling
```
/stripe-test --url https://shop.example.com/checkout --card decline
```

### Test with Specific Persona
```
/stripe-test --url https://shop.example.com/checkout --persona boomer-tech-averse --card success
```

### Full E-commerce Flow
```
/stripe-test --url https://shop.example.com --persona comparison-shopper --tasks "find product, add to cart" --card success
```

### Test 3D Secure
```
/stripe-test --url https://shop.example.com/checkout --card 3ds-required
```

## Error Handling

### No Stripe Detected
```
"I don't see a Stripe checkout form on this page.
Looking for: checkout.stripe.com URL or embedded Stripe Elements.

Suggestions:
- Verify the URL is a checkout page
- Check if the page has finished loading
- Confirm the site uses Stripe for payments"
```

### Card Declined (Expected)
```
"The card was declined as expected for the '[scenario]' test.

Error shown: [exact error message]
Error clarity: [Clear/Vague/Missing]

This tests how your checkout handles [scenario] declines."
```

### Payment Processing Timeout
```
"Payment is taking a long time to process...
[Persona reaction based on patience level]

After [X] seconds, still no response.
This would likely cause abandonment for [persona type] users."
```

## Stripe Form Interaction Notes

### Hosted Checkout (checkout.stripe.com)
- Fields are in the main document
- Use standard `browser_type` and `browser_click`
- Card element accepts full card number

### Embedded Elements
- Card fields are in iframes
- Focus the element first, then type
- Tab moves between card/expiry/CVC

### Payment Request Button
- Apple Pay / Google Pay buttons
- Note if available but skip (test mode)
- Document availability in report
