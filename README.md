**Issue**: Clipping occurs when solar generation exceeds hybrid inverter AC capacity and there is no or limited battery capacity to make use of inverter DC capacity.
**Workaround**: Use Home Assistant automations to adjust predbat behaviour:
- `pv_clipping_in_next_24_hours.yaml`
- `pv_clipping_make_room_in_battery_at_sunrise.yaml`
- `pv_goes_above_inverter_limit.yaml`

The logic is as follows:
1. We want to make room in the battery if PV clipping is forecast in the next 24 hours AND prioritise export over battery charging. So, at sunrise: 
	- Calculate: forecast clipped PV for day = sum of 5 minute `predbat.pv_energy` forecasts > inverter limit
	- Set `select.predbat_manual_export` for x half-hour slots to make room in battery for forecast clipped PV
2. If `sensor.givtcp_{geserial}_pv_power` becomes >= inverter limit
	- Set `select.predbat_manual_export` for this half-hour
- Note: you must have Solcast set up so your AC capacity is equal to your DC capacity (both equal to your array peak kW). Otherwise, Solcast will provide clipped forecast data.

To use these automations, you will need to adjust the constants in the yaml such as GE inverter serial number, inverter capacity and battery charge rate.
You will also need to create a Home Assistant helper input number `input_number.pv_clipped_in_next_24_hours`. 

You can change `tuning_factor` in `pv_clipping_in_next_24_hours.yaml` to adjust the impact of the automations. The tuning factor is designed to account for clouds and how aggressive you want to be with making room in the battery - the lower the value, the more room it will make. By accounting for clouds, I mean your solar prediction can equal your inverter limit but if that solar turns out to be spikey due to intermittent clouds you can still get clipping.

Example results from 9.5 kWh battery, 5 kW hybrid inverter system:
![2025-04-05 Clipping graph](https://github.com/user-attachments/assets/04e597ad-abb7-403d-9294-fdce1a8561ae)
