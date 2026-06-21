# Payment Initiation

## V2.3.5

This is v2.3.5 NZ Open Banking Payment Initiation API technical specification.

The Swagger 2.0 document is [here](payment-initiation-nz-swagger.yaml).

### Changes

- Relaxed Amount pattern from `^\d{1,13}\.\d{1,5}$` to `^\d{1,13}(\.\d{1,5})?$` so the decimal portion is optional
- Refactored the inline `{Amount, Currency}` object (used in 4 places — `InstructedAmount`, `TotalAmount`, `MaximumAmount`, `Frequency.TotalAmount`) into reusable definitions:
  - `ActiveCurrencyAndAmount_SimpleType` — the amount string
  - `ActiveOrHistoricCurrency` — the ISO 4217 currency code
  - `ActiveOrHistoricCurrencyAndAmount` — the composed object
