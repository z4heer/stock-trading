# Nifty Option Selling Strategies Discussion Summary (Feb 20, 2026)

## 1. मूल प्रश्न: Option Selling में Max Profit
- **Option Seller का Max Profit**: हमेशा upfront premium (OTM expire पर)।
- Buyers direction में भी premium buffer तक profit, उसके बाद loss। [web:1][web:9]

## 2. Put Selling Scenario (Strike 100)
- Premium ₹5 मानें: Spot 75 पर exit → ~₹20 loss।
- Max profit के लिए: 50-80% premium decay पर exit या expiry तक hold (spot ≥ strike)। [web:26][web:27]

## 3. Nifty Bearish Scenario (25450 → 25200 breakdown)
**Bear Call Credit Spread (25500/25600 CE)**:
- Sell 25500 CE (~₹80), Buy 25600 CE (~₹40) → Net Credit ₹40/share (₹2600/lot 65 size)।
- Max Profit: ₹2600 (Nifty ≤25500 expiry)।
- Max Loss: ₹3900 (Nifty >25600)।
- Breakeven: 25540।

### Payoff Table
| Nifty Close | P&L (65 lot) |
|-------------|--------------|
| ≤25500     | +₹2600      |
| 25550      | +₹650       |
| >25600     | -₹3900      |

## 4. Max Loss कैलकुलेशन (25600 पर)
- Short CE: -₹100 intrinsic।
- Long CE: +₹100 intrinsic (offset)।
- Net: -₹100 + ₹40 credit = -₹60/share। [web:66][web:73]

## 5. Profitable Adjustment (Nifty <25500)
- 50% Profit Exit: Net ₹20 debit पर close (Short buy ~₹12-23, Long sell ~₹2-5) → ₹1300 lock।
- Roll up/out या Iron Condor convert। [web:79][code_file:1]

## 6. Expiry Outcomes
- **Nifty <25500**: दोनों OTM → Full ₹2600 profit (auto expire)।
- **Nifty ≥25600**: Auto cash settlement → Max ₹3900 loss। [web:73][web:108]

## 7. Credit vs Debit Spread
| Parameter     | Credit Spread       | Debit Spread        |
|---------------|---------------------|---------------------|
| Cash Flow     | +Credit upfront    | -Debit upfront     |
| Max Profit    | Net Credit (₹40)   | Strike Diff - Debit (₹60) |
| Max Loss      | Strike Diff - Credit (₹60) | Net Debit (₹40) |
| Theta Decay   | Favor में           | Against            |

## 8. Risk Management
- Early exit: 50% profit पर।
- Hedge: OTM Put buy (target 50-100%, SL 20-30%)।
- Tools: Zerodha SPAN, Sensibull, Excel tracker।

**Disclaimer**: यह educational है, actual trade से पहले verify करें। Lot size 65 (2026)। Sources: Zerodha Varsity, Investopedia।
