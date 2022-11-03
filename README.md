# Pre-Deal Check of FX Derivatives

For the bank’s daily trading, traders need fast and efficient ways to approximate their proposed deals. Add-on factors play a key component in the decision making process or approval of transactions. This document goes through how add-on factor should be implemented for intraday pre-deal check of FX Forward.
 
For most OTC derivative products, including FX Forward, max(MtM,0) + add-on exposure approach is applied for intraday replacement risk calculation. Once the add-on factor profile has been obtained for a product, the system proceeds to calculate the exposure profile. The add-on exposure plus the deal’s MtM (only if it is positive, otherwise it is assumed as 0) is considered as exposure.

Add-on factor tables (profile basis) are uploaded to the production system to monitor the replacement risk. The system could easily pick up the tables for intraday pre-deal checking. A complete term profile of add-on factors for FX Forward trades, including buy/sell domestic currency and sell/buy foreign currency, with their gross exposure and collateralized exposure are stored in production system to trigger the intraday pre-deal risk calculation. 

If a tenor’s add-on factor profile is missing in the table, it would be approximated by interpolation. The pre-deal checking procedure would firstly calculate the stand-alone exposure based on add-on factor profile (gross & collateralized). Then, the exposure is added to the counterparty’s profile. After this quick and simple aggregation, the peak of the post-deal exposure is compared with the counterparty’s credit limit. A warning would be triggered if the limit is breached.

Foreign Exchange Forward Contract is an instrument that allows the buyer to lock in a foreign exchange rate for a specified date in the future.

For instance, the 2-year forward rate for USD/CAD is 0.951067573351087 and 0.942640335579959 for 3-year. In order to get a forward rate for a deal that matures in 2.5Y (the system doesn’t provide 2.5Y forward rate), we can use linear interpolation method describe in Appendix to derive the appropriate forward FX rate.

In terms of adding an FX Forward’s exposure profile (add-on factor based) to an existing counterparty’s portfolio, if the counterparty doesn’t have CSA approved, its post-deal exposure is simply equal to the summation of its pre-deal exposure and the stand-alone gross exposure; on the other hand

After the system calculates the individual FX derivatives' exposure based on add-on factor, it will add the exposure profile on top of the pre-deal counterparty-level exposure to get post-deal counterparty-level exposure. However, the time buckets from Pre-Deal counterparty-level exposure might be defined differently from the time buckets of individual FX derivatives' deal’s exposure profile. See https://finpricing.com/lib/EqBarrier.html
 
The idea is very simple. First of all, combine both profiles’ time buckets. Then, pick the exposure from both exposure profiles at each time bucket and sum them up. If a time bucket is missing in either profile, it would pick up the exposure at the closest previous time bucket.

A counterparty’s exposure limit could be time-dependent and set up in other currencies rather than USD. Also, a counterparty’s exposure profile is time-dependent. In this way, the exposure calculated at each time bucket should be compared with the limit set up at the corresponding time interval. If the exposure is higher than the limit, there should be a limit breach warning triggered. Also, the limit should be compared with the exposure calculated at the related time bucket. If the limit is lower than the exposure, the system should trigger a warning.
 
Meanwhile, since the exposure is calculated in USD, the counterparty’s limit defined in other currencies has to be converted into USD value based on the current fx spot rate.
