# Home assistant automations to use with predbat

## Automations to adjust best_soc_max to minimise PV clipping by hybrid inverter in predbat
The logic is as follows:
- We want to stop the battery charging fully overnight if PV clipping is forecast in the next 24 hours. So, at sunset and after any Solcast update between sunset and sunrise, after delay of input_number.predbat_calculate_plan_every + 5 minutes: 
  - Calculate: forecast clipped PV for day = sum of 10 minute predbat.pv_energy forecasts > inverter limit
  - Set best_soc_max = battery capacity - forecast clipped PV for day
- Between sunrise and sunset, at 1 minute past and 31 minutes past the hour:
  - If solar forecast for next 30 minute period > inverter limit:
    - Increment best_soc_max by (solar forecast for next 30 minute period - inverter limit)
    - force discharge for coming 30 minute period (so when a cloud comes over and PV drops below inverter limit, the battery won't charge from solar)
- Note: you must have Solcast set up so your AC capacity is equal to your DC capacity (both equal to your array peak kW). Otherwise, Solcast will provide clipped forecast data.
