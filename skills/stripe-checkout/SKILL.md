# Stripe Checkout Testing Skill

This skill provides guidance for testing Stripe checkout flows with any persona. It handles test card data, form detection, and payment-specific narration.

## Purpose

Enable realistic user testing of Stripe payment flows using official test cards, with persona-appropriate reactions to checkout experiences.

## Test Card Reference

Load card data from `test-cards.json`. Key scenarios:

| Scenario | Card | When to Use |
|----------|------|-------------|
| `success` | 4242...4242 | Happy path checkout |
| `decline` | 4000...0002 | Test error handling |
| `3ds-required` | 4000...3155 | Test 3D Secure flow |
| `insufficient` | 4000...9995 | Test specific decline reason |
| `amex` | 3782...0005 | Test Amex (4-digit CVC) |

### Default Test Values

Always use these unless testing specific scenarios:
- **Expiry**: 12/34 (any future date works)
- **CVC**: 123 (or 1234 for Amex)
- **ZIP**: 12345
- **Name**: Test User (or persona's name)
- **Email**: Use `--email` flag value, or default to test@example.com

## Stripe Form Detection

### Hosted Checkout (checkout.stripe.com)

When redirected to Stripe's hosted checkout:
```
URL contains: checkout.stripe.com

Indicators:
- Stripe branding visible
- Email field at top
- Card element with card icon
- "Pay" button with amount
```

### Embedded Elements

When Stripe Elements are embedded in the page:
```
Look for iframes with:
- name containing "__privateStripeFrame"
- src containing "js.stripe.com"

Common element patterns:
- Single card element (all-in-one)
- Separate: card number, expiry, CVC fields
- Payment Request Button (Apple Pay / Google Pay)
```

### Detection Script

Use `browser_evaluate` to detect Stripe:
```javascript
(function() {
  // Check for hosted checkout
  if (window.location.hostname === 'checkout.stripe.com') {
    return { type: 'hosted', detected: true };
  }

  // Check for embedded Elements
  var stripeFrames = document.querySelectorAll('iframe[name*="__privateStripeFrame"]');
  if (stripeFrames.length > 0) {
    return {
      type: 'elements',
      detected: true,
      frameCount: stripeFrames.length
    };
  }

  // Check for Payment Request Button
  var prButton = document.querySelector('[data-testid="payment-request-button"]');
  if (prButton) {
    return { type: 'payment-request', detected: true };
  }

  return { detected: false };
})();
```

## Filling Stripe Forms

### For Hosted Checkout

Fields appear in the main document - use standard form filling:

1. **Email**: Use `browser_type` on email field
2. **Card**: The card element is a single field - type full card number
3. **Expiry**: Type MM/YY format
4. **CVC**: Type 3 digits (4 for Amex)
5. **Name**: Type cardholder name
6. **ZIP**: Type postal code if shown

### For Embedded Elements

Elements are in iframes - interaction requires focusing the iframe:

1. Click/focus on the card element to activate it
2. Type card number directly (it auto-formats)
3. Tab to expiry, type MM/YY
4. Tab to CVC, type digits
5. Tab to submit or click Pay button

### Auto-Fill Approach

Since card entry should be fast (per user's preference):

```
1. Get test card from scenario
2. Use browser_fill_form or browser_type to fill all fields quickly
3. No character-by-character typing
4. Submit immediately after filling
```

## Payment-Specific Narration

### Persona Expressions for Payment

Add these to persona narration when testing checkout:

**Boomer Tech-Averse**:
- "Okay, this is the payment part. Let me make sure this is secure..."
- "I see the little lock icon up there, that's good."
- "Now where do I put my card number? Is this the right field?"
- "Wait, should I put the spaces? Let me try without..."
- "Oh good, it went through! I was nervous there for a second."

**Boomer Tech-Friendly**:
- "Alright, payment time. This looks like my bank's site."
- "Nice, they take all the major cards."
- "Let me get my card from my wallet... okay, ready."
- "Good, it shows the amount clearly. That's what I expected."

**Millennial Tech-Skeptic**:
- "Let me verify this is actually Stripe... yep, secure iframe."
- "Why do they need my phone number for this?"
- "At least they're not storing my card by default."
- "Interesting they don't have PayPal. That's a choice."
- "The price breakdown is clear, no hidden fees. Respect."

**Gen Z Digital Native**:
- "Ugh no Apple Pay? That's annoying."
- "Wait I have to TYPE my card number? What year is this?"
- "Okay fine, let me find my card... where did I put it..."
- "*types quickly* Done. Let's go."
- "Nice, confirmation came through instantly. W."

**Gen Alpha Tablet Kid**:
- "Mommy needs to help with this part..."
- "Lots of numbers! Like math!"
- "Why can't I just tap? Tap tap tap!"

**Impulse Buyer**:
- "Just take my money! Where's Apple Pay?"
- "Ugh, account creation? SKIP. Guest checkout, please."
- "Finally. Pay now. Done. Moving on."

**Comparison Shopper**:
- "Let me verify the total one more time..."
- "SSL certificate... Stripe... okay, this is legit."
- "I should screenshot this for my records."

### Frustration Triggers (Payment)

Watch for these and react appropriately:
- No express checkout options (Apple Pay, Google Pay)
- Required account creation before payment
- Unexpected fees appearing at checkout
- Shipping cost not shown until payment page
- Payment form errors with unclear messages
- 3D Secure popups (especially unexpected)
- Slow payment processing (>3 seconds)
- No order confirmation displayed
- Receipt email not arriving

### Positive Reactions (Payment)

Note and appreciate these:
- Apple Pay / Google Pay available
- Guest checkout option
- Clear price breakdown with no surprises
- Stripe badge / security indicators visible
- Instant confirmation with order number
- Receipt email arrives quickly
- Saved payment option for returning users

## 3D Secure Handling

When `3ds-required` card triggers authentication:

1. **Detect Modal**: Look for Stripe 3DS iframe/modal
2. **Narrate**: Express surprise/concern based on persona
3. **Complete**: In test mode, there's usually a "Complete authentication" button
4. **Continue**: Return to confirmation flow

Persona reactions to 3DS:
- Tech-averse: "Oh no, what's this popup? Is something wrong?"
- Tech-friendly: "Ah, extra security. Like my bank does."
- Tech-skeptic: "3D Secure... okay, that's actually good."
- Digital native: "Ugh, another step? Just let me pay!"

## Report Section: Payment Experience

Add this section to reports when `--stripe` flag is used:

```markdown
## Payment Experience

### Checkout Summary
- **Checkout Type**: Stripe Elements (embedded) / Hosted
- **Express Options**: Apple Pay [Yes/No], Google Pay [Yes/No]
- **Guest Checkout**: [Available/Required Account]
- **Test Card Used**: [scenario] ([card number prefix]...)
- **3D Secure**: [Triggered/Not Triggered]
- **Result**: [Payment Successful/Declined/Error]

### Payment Flow Timeline
1. [Time] Reached checkout page
2. [Time] Started filling payment form
3. [Time] Submitted payment
4. [Time] Received confirmation

### Payment Friction Points
- [List issues that slowed or frustrated the payment process]

### Payment Positives
- [List things that made payment smooth or trustworthy]

### Security Perception
- [How secure did the checkout feel to this persona?]
```

## Integration with Main Testing Flow

When `--stripe` flag is active on `/user-test` or `/stripe-test` is used:

1. **Pre-checkout**: Test normal flows until reaching checkout
2. **Checkout Detection**: Identify Stripe form type
3. **Express Options Check**: Note if Apple Pay / Google Pay available
4. **Form Analysis**: Identify required fields
5. **Auto-Fill Payment**: Fill card details from test scenario
6. **Submit & Wait**: Submit and wait for response
7. **Handle 3DS**: If triggered, complete authentication
8. **Confirmation**: Verify success/failure
9. **Report**: Include Payment Experience section
