# Plan: Condition-Based Billing & Payment Step

## Objective

Add a **billing and payment details** step **only for Condition-based** consultations. After the patient answers all condition questions, the flow automatically shows this step, then a **Proceed to Payment** button with cost **$49.99**. Symptom-based flow is unchanged (no billing step).

---

## Flow Change (Condition-Based Only)

**Current Condition flow:**

`Entry → Condition list → Condition detail → Questions → Summary`

**New Condition flow:**

`Entry → Condition list → Condition detail → Questions → **Billing & Payment** → Summary`

- After the last question (when user clicks **Next** and there are no more questions), **do not** go straight to Summary.
- Instead, show the **Billing & Payment** screen.
- User fills billing/payment details (see below), then clicks **Proceed to Payment ($49.99)**.
- Then go to **Summary** (and optionally include a note that payment will be processed / consultation fee applied).

**Symptom-based:** Unchanged. Still: `Entry → Symptom list → AI Chat → Summary` (no billing step).

---

## Billing & Payment Screen (New Section)

### Placement

- New section/screen in `index.html`, e.g. `id="condition-billing"`, shown only when `mode === 'condition'`.

### Content

1. **Heading**  
   e.g. “Billing & payment” or “Consultation fee”

2. **Consultation cost**  
   - Display: **$49.99** (fixed consultation fee for condition-based).

3. **Billing and payment details (form)**  
   - Fields to collect (prototype/demo level; no real payment processing):
     - **Billing name** (text)
     - **Email** (email)
     - **Billing address** (e.g. Address line 1, City, State, ZIP — or a single address field for demo)
     - **Payment** (demo only): e.g. “Card ending in ****” placeholder or “Payment method (demo)” — no real card fields unless you plan to integrate a gateway later.

   Exact field set can be simplified for the prototype (e.g. name, email, one address line).

4. **Actions**
   - **Proceed to Payment** button:
     - Label: e.g. **“Proceed to Payment — $49.99”** so the cost is clear.
     - On click: validate minimal fields if desired (e.g. name, email), then navigate to **Summary**.
   - **Back** button: return to the last question (or to condition detail — decide in implementation).

### Data

- Store billing/payment values in a small object (e.g. `billingDetails`) when user proceeds.
- Optionally show a short “Payment summary” line on the final Summary screen for condition-based (e.g. “Consultation fee: $49.99” and “Billing: [email]”) without exposing full card details.

---

## Logic Summary

| Path        | After questions / chat        | New step? | Then        |
|------------|-------------------------------|-----------|-------------|
| Condition  | Last question answered        | Yes       | Show Billing → Proceed to Payment ($49.99) → Summary |
| Symptoms   | User clicks “Finish and see summary” | No    | Summary     |

- In code: when in **condition** flow and `questionIndex >= currentQuestions.length`, call a function that shows **condition-billing** instead of `showSummary()`.
- When user clicks **Proceed to Payment** (condition only), optionally save `billingDetails`, then call `showSummary()`.
- Summary can branch: if `mode === 'condition'` and `billingDetails` exist, show a short “Consultation fee: $49.99” and any billing info you want to display.

---

## Implementation Checklist (for when you implement)

- [ ] Add HTML section `#condition-billing` with heading, $49.99 display, form fields (billing name, email, address, demo payment note), “Proceed to Payment — $49.99” button, Back button.
- [ ] Add CSS for the billing form (layout, labels, inputs, button).
- [ ] In condition flow: when moving past the last question, show `condition-billing` instead of calling `showSummary()`.
- [ ] On “Proceed to Payment”: (optional) validate required fields, set `billingDetails`, then call `showSummary()`.
- [ ] On “Back” from billing: go back to questions (last question) or condition detail.
- [ ] Optional: on Summary for condition, add a line like “Consultation fee: $49.99” and/or billing email.
- [ ] Ensure symptom flow never shows billing and never sets consultation fee in the UI.

---

## Scope Notes

- **Prototype:** No real payment processing (no gateway, no PCI). Form is for layout and flow only; “Proceed to Payment” only advances to Summary.
- **Condition-only:** Billing step and $49.99 are only applicable for Condition-based consultations as requested.
