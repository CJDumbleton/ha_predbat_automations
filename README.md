The logic is as follows:
1. We want to make room in the battery if PV clipping is forecast in the next 24 hours AND prioritise export over battery charging. So, at sunrise: 
	- Calculate: forecast clipped PV for day = sum of 10 minute `predbat.pv_energy` forecasts > inverter limit
	- Set `select.predbat_manual_discharge` for the next x half-hour slots to make room in battery for forecast clipped PV
2. If `sensor.givtcp_{geserial}_pv_power` becomes >= inverter limit
	- Set `select.predbat_manual_discharge` for this half-hour
- Note: you must have Solcast set up so your AC capacity is equal to your DC capacity (both equal to your array peak kW). Otherwise, Solcast will provide clipped forecast data.
