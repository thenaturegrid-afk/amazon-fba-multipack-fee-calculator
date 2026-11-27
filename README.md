markdown
# Amazon US FBA Multipack & Wholesale Profit Calculator

Open-source deterministic math reference for calculating **true Amazon US FBA net profit, ROI, break-even wholesale cost, and 1-pack vs. multipack landed costs**.

> **Live Tool & Bulk CSV Analyzer:** Built to power **[SheetToASIN](https://sheettoasin.com)** — check 3 UPCs/ASINs free per day with no login, detect multipack listing mismatches automatically, and audit wholesale supplier spreadsheets.

---

## Why Multipack Math Traps Wholesale Buyers
A common mistake when scanning wholesale supplier price sheets is comparing a **1-unit supplier wholesale cost** directly against an **Amazon Buy Box price for a 2-pack, 4-pack, or 6-pack** listing.

### Core Formulas
1. **Effective Landed Unit Cost (Pack-Adjusted):**
   $$\text{Effective Cost} = (\text{Supplier Unit Cost} \times \text{Amazon Pack Count}) + (\text{Inbound Prep/Shipping} \times \text{Amazon Pack Count})$$
2. **Amazon Referral Fee (Whole-Cent Precision):**
   $$\text{Referral Fee} = \max(\text{Minimum Fee}, \text{Buy Box Price} \times \text{Category Rate})$$
3. **Net FBA Profit & ROI:**
   $$\text{Net Profit} = \text{Buy Box Price} - \text{Effective Cost} - \text{Referral Fee} - \text{FBA Fulfillment Fee}$$
   $$\text{ROI (\%)} = \left(\frac{\text{Net Profit}}{\text{Effective Cost}}\right) \times 100$$
4. **Break-Even Wholesale Unit Cost ($0 Net Profit):**
   $$\text{Max Supplier Unit Cost} = \frac{\text{Buy Box Price} - \text{Referral Fee} - \text{FBA Fulfillment Fee}}{\text{Amazon Pack Count}} - \text{Inbound Per Unit}$$

---

## Reference Implementation (JavaScript / TypeScript)

```javascript
/**
 * Calculates pack-adjusted Amazon US FBA profitability in whole cents.
 */
export function calculateFbaWholesaleMargin({
  buyBoxPrice,
  supplierUnitCost,
  amazonPackCount = 1,
  supplierPackCount = 1,
  referralRate = 0.15,
  minReferralFee = 0.30,
  fbaFulfillmentFee = 0,
  inboundPrepPerUnit = 0,
}) {
  const packMultiplier = amazonPackCount / supplierPackCount;
  const effectiveLandedCost = (supplierUnitCost + inboundPrepPerUnit) * packMultiplier;
  const referralFee = Math.max(minReferralFee, Number((buyBoxPrice * referralRate).toFixed(2)));
  const netProfit = Number((buyBoxPrice - effectiveLandedCost - referralFee - fbaFulfillmentFee).toFixed(2));
  const roiPercent = effectiveLandedCost > 0 ? Number(((netProfit / effectiveLandedCost) * 100).toFixed(1)) : 0;
  const breakEvenSupplierCost = Number(
    (((buyBoxPrice - referralFee - fbaFulfillmentFee) / packMultiplier) - inboundPrepPerUnit).toFixed(2)
  );

  return {
    packMismatchWarning: amazonPackCount !== supplierPackCount,
    packMultiplier,
    effectiveLandedCost: Number(effectiveLandedCost.toFixed(2)),
    referralFee,
    fbaFulfillmentFee,
    netProfit,
    roiPercent,
    breakEvenSupplierCost,
  };
}
```

---

## Live Wholesale Data & Bulk CSV Scanning
To run this math automatically against **live Amazon US Buy Box prices, FBA fee tiers, and sales rank data** (with automatic UPC leading-zero preservation):
- **Free No-Login UPC & Multipack Checker:** [https://sheettoasin.com](https://sheettoasin.com)
- **Category FBA Fee Calculators:** [http
