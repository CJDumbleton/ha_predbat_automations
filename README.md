Issue: Clipping occurs when solar generation exceeds hybrid inverter AC capacity and there is no or limited battery capacity to make use of inverter DC capacity.
Workaround: Use Home Assistant automations to adjust predbat behaviour:
`pv_clipping_in_next_24_hours.yaml`
`pv_clipping_make_room_in_battery_at_sunrise.yaml`
`pv_goes_above_inverter_limit.yaml`

The logic is as follows:
1. We want to make room in the battery if PV clipping is forecast in the next 24 hours AND prioritise export over battery charging. So, at sunrise: 
	- Calculate: forecast clipped PV for day = sum of 10 minute `predbat.pv_energy` forecasts > inverter limit
	- Set `select.predbat_manual_export` for the next x half-hour slots to make room in battery for forecast clipped PV
2. If `sensor.givtcp_{geserial}_pv_power` becomes >= inverter limit
	- Set `select.predbat_manual_export` for this half-hour
- Note: you must have Solcast set up so your AC capacity is equal to your DC capacity (both equal to your array peak kW). Otherwise, Solcast will provide clipped forecast data.

To use these automations, you will need to adjust the constants in the yaml such as GE inverter  serial number, inverter capacity and battery charge rate.
You will also need to create a Home Assistant helper input number `input_number.pv_clipped_in_next_24_hours`.
