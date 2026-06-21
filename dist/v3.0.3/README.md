# Payment Initiation

## V3.0.3

This is v3.0.3 NZ Open Banking Payment Initiation API technical specification.

The OpenAPI 3 document is [here](payment-initiation-nz-openapi.yaml).

### Changes

- Relaxed Amount pattern from `^\d{1,13}\.\d{1,5}$` to `^\d{1,13}(\.\d{1,5})?$` so the decimal portion is optional
- Refactored the inline `{Amount, Currency}` object (used in 4 places — `InstructedAmount`, `TotalAmount`, `MaximumAmount`, `Frequency.TotalAmount`) into reusable components:
  - `ActiveCurrencyAndAmount_SimpleType` — the amount string
  - `ActiveOrHistoricCurrency` — the ISO 4217 currency code
  - `ActiveOrHistoricCurrencyAndAmount` — the composed object
