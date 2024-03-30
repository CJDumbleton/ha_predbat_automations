# Home assistant automations to use with predbat

## Automations to adjust best_soc_max to minimise PV clipping by hybrid inverter in predbat
The logic is as follows:
- At 00:05 (After my Solcast update automation runs at midnight. We want to stop the battery charging fully overnight if clipping is forecast in the next 24 hours.)
  - Calculate: forecast excess PV for day = sum of 10 minute predbat.pv_energy forecasts > inverter limit
  - Set best_soc_max = battery capacity - forecast excess PV for day
- Every 30 minutes (at 1 minute past and 31 minutes past) throughout the day 
  - If solar forecast for next 30 minute period > inverter limit:
    - increase best_soc_max by (solar forecast for next 30 minute period - inverter limit)
    - force discharge for coming 30 minute period (so when a cloud comes over and PV drops below inverter limit, the battery won't charge from solar)
- At sunset, reset best_soc_max to 0 (auto)
- Note: you must have Solcast set up so your AC capacity is equal to your DC capacity (both equal to your array peak kW). Otherwise, Solcast will provide clipped forecast data.
