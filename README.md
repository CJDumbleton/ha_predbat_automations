The logic is as follows:
1. We want to make room in the battery if PV clipping is forecast in the next 24 hours. So, at sunrise: 
	- If `predbat.rates < 0`, 
		- set `input_number.predbat_best_soc_max = 0` (auto), 
	- else:
		- Calculate: forecast clipped PV for day = sum of 10 minute `predbat.pv_energy` forecasts > inverter limit
		- Set `input_number.predbat_best_soc_max` = battery capacity - forecast clipped PV for day
2. Between sunrise and sunset, at 1 minute past and 31 minutes past the hour:
	- If `input_number.predbat_best_soc_max != 0` and solar forecast for next 30 minute period > inverter limit:
	    - Increment `input_number.predbat_best_soc_max` by (solar forecast for next 30 minute period - inverter limit)
3. If `sensor.givtcp_{geserial}_pv_power` becomes >= inverter limit
	- Set `select.predbat_manual_discharge` for this half-hour (to prioritise export over battery charging
4. If `sensor.givtcp_{geserial}_pv_power` changes from >= inverter limit to < inverter limit and `predbat.rates > 0`:
	- Set `select.predbat_manual_discharge` for this half-hour (to prioritise export over battery charging if the sun goes behind a cloud)
- Note: you must have Solcast set up so your AC capacity is equal to your DC capacity (both equal to your array peak kW). Otherwise, Solcast will provide clipped forecast data.
