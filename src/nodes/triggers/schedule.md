---
nav:
  title: "Schedule Trigger"
  position: 2
---

# Schedule Trigger

Use the Schedule Trigger node to run a workflow at fixed times instead of in response to an event. It suits periodic syncs and reports, for example pulling ERP stock levels into Shopware every hour, exporting yesterday's orders each morning, or reconciling data on a weekly basis.

Choose a frequency in the **Schedule** picker (every few minutes, hourly, daily, weekly, monthly) or switch to a custom cron expression with five fields: minute, hour, day of month, month, weekday. Set the **Timezone** the schedule should follow; it defaults to UTC. The node has no inputs.

:::info
**Node type:** `trigger.schedule` · **Category:** Trigger
:::

## Node parameters

Configure this node with these parameters.

### Schedule

Cron expression for scheduling (e.g., 0 9 * * 1-5)

- Type: Text
- Required
- Default: `0 9 * * *`
- Validated against pattern `^\s*([0-9*\/\-,a-zA-Z]+)\s+([0-9*\/\-,a-zA-Z]+)\s+([0-9*\/\-,a-zA-Z]+)\s+([0-9*\/\-,a-zA-Z]+)\s+([0-9*\/\-,a-zA-Z]+)\s*$`

### Timezone

Timezone for schedule execution

- Type: Dropdown
- Optional
- Default: `UTC`

Options:

- **(UTC) UTC** `UTC`
- **(UTC -11:00) Pacific/Niue** `Pacific/Niue`
- **(UTC -11:00) Pacific/Midway** `Pacific/Midway`
- **(UTC -11:00) Pacific/Pago Pago** `Pacific/Pago_Pago`
- **(UTC -10:00) Pacific/Rarotonga** `Pacific/Rarotonga`
- **(UTC -10:00) Pacific/Honolulu** `Pacific/Honolulu`
- **(UTC -10:00) Pacific/Tahiti** `Pacific/Tahiti`
- **(UTC -10:30) Pacific/Marquesas** `Pacific/Marquesas`
- **(UTC -09:00) Pacific/Gambier** `Pacific/Gambier`
- **(UTC -09:00) America/Adak** `America/Adak`
- **(UTC -08:00) America/Anchorage** `America/Anchorage`
- **(UTC -08:00) Pacific/Pitcairn** `Pacific/Pitcairn`
- **(UTC -07:00) America/Hermosillo** `America/Hermosillo`
- **(UTC -07:00) America/Phoenix** `America/Phoenix`
- **(UTC -07:00) America/Los Angeles** `America/Los_Angeles`
- **(UTC -07:00) America/Tijuana** `America/Tijuana`
- **(UTC -07:00) America/Vancouver** `America/Vancouver`
- **(UTC -07:00) America/Whitehorse** `America/Whitehorse`
- **(UTC -06:00) America/Belize** `America/Belize`
- **(UTC -06:00) America/Guatemala** `America/Guatemala`
- **(UTC -06:00) America/Managua** `America/Managua`
- **(UTC -06:00) America/Mexico City** `America/Mexico_City`
- **(UTC -06:00) America/Costa Rica** `America/Costa_Rica`
- **(UTC -06:00) America/El Salvador** `America/El_Salvador`
- **(UTC -06:00) America/Regina** `America/Regina`
- **(UTC -06:00) America/Tegucigalpa** `America/Tegucigalpa`
- **(UTC -06:00) Pacific/Galapagos** `Pacific/Galapagos`
- **(UTC -06:00) America/Edmonton** `America/Edmonton`
- **(UTC -06:00) America/Ciudad Juarez** `America/Ciudad_Juarez`
- **(UTC -06:00) America/Denver** `America/Denver`
- **(UTC -05:00) America/Rio Branco** `America/Rio_Branco`
- **(UTC -05:00) America/Chicago** `America/Chicago`
- **(UTC -05:00) America/Matamoros** `America/Matamoros`
- **(UTC -05:00) America/Winnipeg** `America/Winnipeg`
- **(UTC -05:00) America/Bogota** `America/Bogota`
- **(UTC -05:00) Pacific/Easter** `Pacific/Easter`
- **(UTC -05:00) America/Atikokan** `America/Atikokan`
- **(UTC -05:00) America/Cancun** `America/Cancun`
- **(UTC -05:00) America/Cayman** `America/Cayman`
- **(UTC -05:00) America/Jamaica** `America/Jamaica`
- **(UTC -05:00) America/Panama** `America/Panama`
- **(UTC -05:00) America/Guayaquil** `America/Guayaquil`
- **(UTC -05:00) America/Lima** `America/Lima`
- **(UTC -04:00) America/Manaus** `America/Manaus`
- **(UTC -04:00) America/St Kitts** `America/St_Kitts`
- **(UTC -04:00) America/Blanc-Sablon** `America/Blanc-Sablon`
- **(UTC -04:00) America/Montserrat** `America/Montserrat`
- **(UTC -04:00) America/Barbados** `America/Barbados`
- **(UTC -04:00) America/Port of Spain** `America/Port_of_Spain`
- **(UTC -04:00) America/Martinique** `America/Martinique`
- **(UTC -04:00) America/St Lucia** `America/St_Lucia`
- **(UTC -04:00) America/St Barthelemy** `America/St_Barthelemy`
- **(UTC -04:00) America/St Vincent** `America/St_Vincent`
- **(UTC -04:00) America/Kralendijk** `America/Kralendijk`
- **(UTC -04:00) America/Guadeloupe** `America/Guadeloupe`
- **(UTC -04:00) America/Marigot** `America/Marigot`
- **(UTC -04:00) America/Aruba** `America/Aruba`
- **(UTC -04:00) America/Lower Princes** `America/Lower_Princes`
- **(UTC -04:00) America/Tortola** `America/Tortola`
- **(UTC -04:00) America/Dominica** `America/Dominica`
- **(UTC -04:00) America/St Thomas** `America/St_Thomas`
- **(UTC -04:00) America/Grenada** `America/Grenada`
- **(UTC -04:00) America/Antigua** `America/Antigua`
- **(UTC -04:00) America/Puerto Rico** `America/Puerto_Rico`
- **(UTC -04:00) America/Santo Domingo** `America/Santo_Domingo`
- **(UTC -04:00) America/Anguilla** `America/Anguilla`
- **(UTC -04:00) America/Curacao** `America/Curacao`
- **(UTC -04:00) America/La Paz** `America/La_Paz`
- **(UTC -04:00) America/Havana** `America/Havana`
- **(UTC -04:00) America/Grand Turk** `America/Grand_Turk`
- **(UTC -04:00) America/Nassau** `America/Nassau`
- **(UTC -04:00) America/New York** `America/New_York`
- **(UTC -04:00) America/Port-au-Prince** `America/Port-au-Prince`
- **(UTC -04:00) America/Toronto** `America/Toronto`
- **(UTC -04:00) America/Guyana** `America/Guyana`
- **(UTC -04:00) America/Caracas** `America/Caracas`
- **(UTC -03:00) America/Argentina/Buenos Aires** `America/Argentina/Buenos_Aires`
- **(UTC -03:00) America/Halifax** `America/Halifax`
- **(UTC -03:00) Atlantic/Bermuda** `Atlantic/Bermuda`
- **(UTC -03:00) America/Thule** `America/Thule`
- **(UTC -03:00) America/Sao Paulo** `America/Sao_Paulo`
- **(UTC -03:00) Antarctica/Palmer** `Antarctica/Palmer`
- **(UTC -03:00) America/Punta Arenas** `America/Punta_Arenas`
- **(UTC -03:00) America/Santiago** `America/Santiago`
- **(UTC -03:00) Atlantic/Stanley** `Atlantic/Stanley`
- **(UTC -03:00) America/Cayenne** `America/Cayenne`
- **(UTC -03:00) America/Asuncion** `America/Asuncion`
- **(UTC -03:00) America/Paramaribo** `America/Paramaribo`
- **(UTC -03:00) America/Montevideo** `America/Montevideo`
- **(UTC -03:30) America/St Johns** `America/St_Johns`
- **(UTC -02:00) America/Nuuk** `America/Nuuk`
- **(UTC -02:00) America/Noronha** `America/Noronha`
- **(UTC -02:00) Atlantic/South Georgia** `Atlantic/South_Georgia`
- **(UTC -02:00) America/Miquelon** `America/Miquelon`
- **(UTC -01:00) Atlantic/Azores** `Atlantic/Azores`
- **(UTC -01:00) Atlantic/Cape Verde** `Atlantic/Cape_Verde`
- **(UTC) Africa/Abidjan** `Africa/Abidjan`
- **(UTC) Africa/Bamako** `Africa/Bamako`
- **(UTC) Africa/Bissau** `Africa/Bissau`
- **(UTC) Africa/Conakry** `Africa/Conakry`
- **(UTC) Africa/Dakar** `Africa/Dakar`
- **(UTC) America/Danmarkshavn** `America/Danmarkshavn`
- **(UTC) Europe/Isle of Man** `Europe/Isle_of_Man`
- **(UTC) Europe/Dublin** `Europe/Dublin`
- **(UTC) Africa/Freetown** `Africa/Freetown`
- **(UTC) Atlantic/St Helena** `Atlantic/St_Helena`
- **(UTC) Africa/Accra** `Africa/Accra`
- **(UTC) Africa/Lome** `Africa/Lome`
- **(UTC) Europe/London** `Europe/London`
- **(UTC) Africa/Monrovia** `Africa/Monrovia`
- **(UTC) Africa/Nouakchott** `Africa/Nouakchott`
- **(UTC) Africa/Ouagadougou** `Africa/Ouagadougou`
- **(UTC) Atlantic/Reykjavik** `Atlantic/Reykjavik`
- **(UTC) Europe/Jersey** `Europe/Jersey`
- **(UTC) Europe/Guernsey** `Europe/Guernsey`
- **(UTC) Africa/Sao Tome** `Africa/Sao_Tome`
- **(UTC) Africa/Banjul** `Africa/Banjul`
- **(UTC) Antarctica/Troll** `Antarctica/Troll`
- **(UTC) Africa/Casablanca** `Africa/Casablanca`
- **(UTC) Africa/El Aaiun** `Africa/El_Aaiun`
- **(UTC) Atlantic/Canary** `Atlantic/Canary`
- **(UTC) Europe/Lisbon** `Europe/Lisbon`
- **(UTC) Atlantic/Faroe** `Atlantic/Faroe`
- **(UTC +01:00) Africa/Algiers** `Africa/Algiers`
- **(UTC +01:00) Europe/Amsterdam** `Europe/Amsterdam`
- **(UTC +01:00) Europe/Andorra** `Europe/Andorra`
- **(UTC +01:00) Europe/Belgrade** `Europe/Belgrade`
- **(UTC +01:00) Europe/Berlin** `Europe/Berlin`
- **(UTC +01:00) Europe/Bratislava** `Europe/Bratislava`
- **(UTC +01:00) Europe/Brussels** `Europe/Brussels`
- **(UTC +01:00) Europe/Budapest** `Europe/Budapest`
- **(UTC +01:00) Europe/Copenhagen** `Europe/Copenhagen`
- **(UTC +01:00) Europe/Gibraltar** `Europe/Gibraltar`
- **(UTC +01:00) Europe/Ljubljana** `Europe/Ljubljana`
- **(UTC +01:00) Arctic/Longyearbyen** `Arctic/Longyearbyen`
- **(UTC +01:00) Europe/Luxembourg** `Europe/Luxembourg`
- **(UTC +01:00) Europe/Madrid** `Europe/Madrid`
- **(UTC +01:00) Europe/Monaco** `Europe/Monaco`
- **(UTC +01:00) Europe/Oslo** `Europe/Oslo`
- **(UTC +01:00) Europe/Paris** `Europe/Paris`
- **(UTC +01:00) Europe/Podgorica** `Europe/Podgorica`
- **(UTC +01:00) Europe/Prague** `Europe/Prague`
- **(UTC +01:00) Europe/Rome** `Europe/Rome`
- **(UTC +01:00) Europe/San Marino** `Europe/San_Marino`
- **(UTC +01:00) Europe/Malta** `Europe/Malta`
- **(UTC +01:00) Europe/Sarajevo** `Europe/Sarajevo`
- **(UTC +01:00) Europe/Skopje** `Europe/Skopje`
- **(UTC +01:00) Europe/Stockholm** `Europe/Stockholm`
- **(UTC +01:00) Europe/Tirane** `Europe/Tirane`
- **(UTC +01:00) Africa/Tunis** `Africa/Tunis`
- **(UTC +01:00) Europe/Vaduz** `Europe/Vaduz`
- **(UTC +01:00) Europe/Vatican** `Europe/Vatican`
- **(UTC +01:00) Europe/Vienna** `Europe/Vienna`
- **(UTC +01:00) Europe/Warsaw** `Europe/Warsaw`
- **(UTC +01:00) Europe/Zagreb** `Europe/Zagreb`
- **(UTC +01:00) Europe/Zurich** `Europe/Zurich`
- **(UTC +01:00) Africa/Bangui** `Africa/Bangui`
- **(UTC +01:00) Africa/Malabo** `Africa/Malabo`
- **(UTC +01:00) Africa/Brazzaville** `Africa/Brazzaville`
- **(UTC +01:00) Africa/Porto-Novo** `Africa/Porto-Novo`
- **(UTC +01:00) Africa/Douala** `Africa/Douala`
- **(UTC +01:00) Africa/Kinshasa** `Africa/Kinshasa`
- **(UTC +01:00) Africa/Lagos** `Africa/Lagos`
- **(UTC +01:00) Africa/Libreville** `Africa/Libreville`
- **(UTC +01:00) Africa/Luanda** `Africa/Luanda`
- **(UTC +01:00) Africa/Ndjamena** `Africa/Ndjamena`
- **(UTC +01:00) Africa/Niamey** `Africa/Niamey`
- **(UTC +02:00) Africa/Bujumbura** `Africa/Bujumbura`
- **(UTC +02:00) Africa/Gaborone** `Africa/Gaborone`
- **(UTC +02:00) Africa/Harare** `Africa/Harare`
- **(UTC +02:00) Africa/Juba** `Africa/Juba`
- **(UTC +02:00) Africa/Khartoum** `Africa/Khartoum`
- **(UTC +02:00) Africa/Kigali** `Africa/Kigali`
- **(UTC +02:00) Africa/Blantyre** `Africa/Blantyre`
- **(UTC +02:00) Africa/Lubumbashi** `Africa/Lubumbashi`
- **(UTC +02:00) Africa/Lusaka** `Africa/Lusaka`
- **(UTC +02:00) Africa/Maputo** `Africa/Maputo`
- **(UTC +02:00) Africa/Windhoek** `Africa/Windhoek`
- **(UTC +02:00) Europe/Athens** `Europe/Athens`
- **(UTC +02:00) Asia/Beirut** `Asia/Beirut`
- **(UTC +02:00) Europe/Bucharest** `Europe/Bucharest`
- **(UTC +02:00) Africa/Cairo** `Africa/Cairo`
- **(UTC +02:00) Europe/Chisinau** `Europe/Chisinau`
- **(UTC +02:00) Asia/Hebron** `Asia/Hebron`
- **(UTC +02:00) Europe/Helsinki** `Europe/Helsinki`
- **(UTC +02:00) Europe/Kaliningrad** `Europe/Kaliningrad`
- **(UTC +02:00) Europe/Kyiv** `Europe/Kyiv`
- **(UTC +02:00) Europe/Mariehamn** `Europe/Mariehamn`
- **(UTC +02:00) Asia/Nicosia** `Asia/Nicosia`
- **(UTC +02:00) Europe/Riga** `Europe/Riga`
- **(UTC +02:00) Europe/Sofia** `Europe/Sofia`
- **(UTC +02:00) Europe/Tallinn** `Europe/Tallinn`
- **(UTC +02:00) Africa/Tripoli** `Africa/Tripoli`
- **(UTC +02:00) Europe/Vilnius** `Europe/Vilnius`
- **(UTC +02:00) Asia/Jerusalem** `Asia/Jerusalem`
- **(UTC +02:00) Africa/Johannesburg** `Africa/Johannesburg`
- **(UTC +02:00) Africa/Mbabane** `Africa/Mbabane`
- **(UTC +02:00) Africa/Maseru** `Africa/Maseru`
- **(UTC +03:00) Asia/Kuwait** `Asia/Kuwait`
- **(UTC +03:00) Asia/Bahrain** `Asia/Bahrain`
- **(UTC +03:00) Asia/Baghdad** `Asia/Baghdad`
- **(UTC +03:00) Asia/Qatar** `Asia/Qatar`
- **(UTC +03:00) Asia/Riyadh** `Asia/Riyadh`
- **(UTC +03:00) Asia/Aden** `Asia/Aden`
- **(UTC +03:00) Asia/Amman** `Asia/Amman`
- **(UTC +03:00) Asia/Damascus** `Asia/Damascus`
- **(UTC +03:00) Africa/Addis Ababa** `Africa/Addis_Ababa`
- **(UTC +03:00) Indian/Antananarivo** `Indian/Antananarivo`
- **(UTC +03:00) Africa/Asmara** `Africa/Asmara`
- **(UTC +03:00) Africa/Dar es Salaam** `Africa/Dar_es_Salaam`
- **(UTC +03:00) Africa/Djibouti** `Africa/Djibouti`
- **(UTC +03:00) Africa/Kampala** `Africa/Kampala`
- **(UTC +03:00) Indian/Mayotte** `Indian/Mayotte`
- **(UTC +03:00) Africa/Mogadishu** `Africa/Mogadishu`
- **(UTC +03:00) Indian/Comoro** `Indian/Comoro`
- **(UTC +03:00) Africa/Nairobi** `Africa/Nairobi`
- **(UTC +03:00) Europe/Minsk** `Europe/Minsk`
- **(UTC +03:00) Europe/Moscow** `Europe/Moscow`
- **(UTC +03:00) Europe/Simferopol** `Europe/Simferopol`
- **(UTC +03:00) Antarctica/Syowa** `Antarctica/Syowa`
- **(UTC +03:00) Europe/Istanbul** `Europe/Istanbul`
- **(UTC +03:30) Asia/Tehran** `Asia/Tehran`
- **(UTC +04:00) Asia/Yerevan** `Asia/Yerevan`
- **(UTC +04:00) Asia/Baku** `Asia/Baku`
- **(UTC +04:00) Asia/Tbilisi** `Asia/Tbilisi`
- **(UTC +04:00) Asia/Dubai** `Asia/Dubai`
- **(UTC +04:00) Asia/Muscat** `Asia/Muscat`
- **(UTC +04:00) Indian/Mauritius** `Indian/Mauritius`
- **(UTC +04:00) Indian/Reunion** `Indian/Reunion`
- **(UTC +04:00) Europe/Samara** `Europe/Samara`
- **(UTC +04:00) Indian/Mahe** `Indian/Mahe`
- **(UTC +04:30) Asia/Kabul** `Asia/Kabul`
- **(UTC +05:00) Indian/Kerguelen** `Indian/Kerguelen`
- **(UTC +05:00) Indian/Maldives** `Indian/Maldives`
- **(UTC +05:00) Antarctica/Mawson** `Antarctica/Mawson`
- **(UTC +05:00) Asia/Karachi** `Asia/Karachi`
- **(UTC +05:00) Asia/Dushanbe** `Asia/Dushanbe`
- **(UTC +05:00) Asia/Ashgabat** `Asia/Ashgabat`
- **(UTC +05:00) Asia/Tashkent** `Asia/Tashkent`
- **(UTC +05:00) Asia/Almaty** `Asia/Almaty`
- **(UTC +05:00) Asia/Yekaterinburg** `Asia/Yekaterinburg`
- **(UTC +05:30) Asia/Colombo** `Asia/Colombo`
- **(UTC +05:30) Asia/Kolkata** `Asia/Kolkata`
- **(UTC +05:45) Asia/Kathmandu** `Asia/Kathmandu`
- **(UTC +06:00) Asia/Dhaka** `Asia/Dhaka`
- **(UTC +06:00) Asia/Thimphu** `Asia/Thimphu`
- **(UTC +06:00) Asia/Urumqi** `Asia/Urumqi`
- **(UTC +06:00) Indian/Chagos** `Indian/Chagos`
- **(UTC +06:00) Asia/Bishkek** `Asia/Bishkek`
- **(UTC +06:00) Asia/Omsk** `Asia/Omsk`
- **(UTC +06:30) Indian/Cocos** `Indian/Cocos`
- **(UTC +06:30) Asia/Yangon** `Asia/Yangon`
- **(UTC +07:00) Indian/Christmas** `Indian/Christmas`
- **(UTC +07:00) Antarctica/Davis** `Antarctica/Davis`
- **(UTC +07:00) Asia/Hovd** `Asia/Hovd`
- **(UTC +07:00) Asia/Bangkok** `Asia/Bangkok`
- **(UTC +07:00) Asia/Ho Chi Minh** `Asia/Ho_Chi_Minh`
- **(UTC +07:00) Asia/Phnom Penh** `Asia/Phnom_Penh`
- **(UTC +07:00) Asia/Vientiane** `Asia/Vientiane`
- **(UTC +07:00) Asia/Novosibirsk** `Asia/Novosibirsk`
- **(UTC +07:00) Asia/Jakarta** `Asia/Jakarta`
- **(UTC +08:00) Antarctica/Casey** `Antarctica/Casey`
- **(UTC +08:00) Australia/Perth** `Australia/Perth`
- **(UTC +08:00) Asia/Brunei** `Asia/Brunei`
- **(UTC +08:00) Asia/Makassar** `Asia/Makassar`
- **(UTC +08:00) Asia/Macau** `Asia/Macau`
- **(UTC +08:00) Asia/Shanghai** `Asia/Shanghai`
- **(UTC +08:00) Asia/Hong Kong** `Asia/Hong_Kong`
- **(UTC +08:00) Asia/Irkutsk** `Asia/Irkutsk`
- **(UTC +08:00) Asia/Kuala Lumpur** `Asia/Kuala_Lumpur`
- **(UTC +08:00) Asia/Manila** `Asia/Manila`
- **(UTC +08:00) Asia/Singapore** `Asia/Singapore`
- **(UTC +08:00) Asia/Taipei** `Asia/Taipei`
- **(UTC +08:00) Asia/Ulaanbaatar** `Asia/Ulaanbaatar`
- **(UTC +08:45) Australia/Eucla** `Australia/Eucla`
- **(UTC +09:00) Asia/Dili** `Asia/Dili`
- **(UTC +09:00) Asia/Jayapura** `Asia/Jayapura`
- **(UTC +09:00) Asia/Tokyo** `Asia/Tokyo`
- **(UTC +09:00) Asia/Pyongyang** `Asia/Pyongyang`
- **(UTC +09:00) Asia/Seoul** `Asia/Seoul`
- **(UTC +09:00) Pacific/Palau** `Pacific/Palau`
- **(UTC +09:00) Asia/Chita** `Asia/Chita`
- **(UTC +09:30) Australia/Darwin** `Australia/Darwin`
- **(UTC +10:00) Australia/Brisbane** `Australia/Brisbane`
- **(UTC +10:00) Pacific/Guam** `Pacific/Guam`
- **(UTC +10:00) Pacific/Saipan** `Pacific/Saipan`
- **(UTC +10:00) Pacific/Chuuk** `Pacific/Chuuk`
- **(UTC +10:00) Antarctica/DumontDUrville** `Antarctica/DumontDUrville`
- **(UTC +10:00) Pacific/Port Moresby** `Pacific/Port_Moresby`
- **(UTC +10:00) Asia/Vladivostok** `Asia/Vladivostok`
- **(UTC +10:30) Australia/Adelaide** `Australia/Adelaide`
- **(UTC +11:00) Australia/Sydney** `Australia/Sydney`
- **(UTC +11:00) Pacific/Bougainville** `Pacific/Bougainville`
- **(UTC +11:00) Pacific/Kosrae** `Pacific/Kosrae`
- **(UTC +11:00) Australia/Lord Howe** `Australia/Lord_Howe`
- **(UTC +11:00) Pacific/Noumea** `Pacific/Noumea`
- **(UTC +11:00) Asia/Sakhalin** `Asia/Sakhalin`
- **(UTC +11:00) Pacific/Guadalcanal** `Pacific/Guadalcanal`
- **(UTC +11:00) Pacific/Efate** `Pacific/Efate`
- **(UTC +12:00) Pacific/Fiji** `Pacific/Fiji`
- **(UTC +12:00) Pacific/Tarawa** `Pacific/Tarawa`
- **(UTC +12:00) Pacific/Majuro** `Pacific/Majuro`
- **(UTC +12:00) Pacific/Nauru** `Pacific/Nauru`
- **(UTC +12:00) Pacific/Norfolk** `Pacific/Norfolk`
- **(UTC +12:00) Asia/Kamchatka** `Asia/Kamchatka`
- **(UTC +12:00) Pacific/Funafuti** `Pacific/Funafuti`
- **(UTC +12:00) Pacific/Wake** `Pacific/Wake`
- **(UTC +12:00) Pacific/Wallis** `Pacific/Wallis`
- **(UTC +13:00) Pacific/Apia** `Pacific/Apia`
- **(UTC +13:00) Pacific/Auckland** `Pacific/Auckland`
- **(UTC +13:00) Antarctica/McMurdo** `Antarctica/McMurdo`
- **(UTC +13:00) Pacific/Kanton** `Pacific/Kanton`
- **(UTC +13:00) Pacific/Fakaofo** `Pacific/Fakaofo`
- **(UTC +13:00) Pacific/Tongatapu** `Pacific/Tongatapu`
- **(UTC +13:45) Pacific/Chatham** `Pacific/Chatham`
- **(UTC +14:00) Pacific/Kiritimati** `Pacific/Kiritimati`

## Input and output

### Input

None.

### Output

| Port | Type | Description |
| --- | --- | --- |
| Timestamp | `string` | Execution timestamp |

The output contains the timestamp of the run. Workflows started by a schedule carry no event payload, so the next node usually reads data itself, for example a **Shopware API Call** with **Get all** or an ERP read operation.

## Examples

### Sync ERP items into Shopware every morning on weekdays

| Parameter | Value |
| --- | --- |
| Schedule | `0 6 * * 1-5` |
| Timezone | (UTC +01:00) Europe/Berlin |

Connect the trigger to the read operation of your ERP node, then a **Field Mapping** node with the matching preset, then a **Shopware API Call** with **Create or update**.

### Check stock every 15 minutes

| Parameter | Value |
| --- | --- |
| Schedule | `*/15 * * * *` |
| Timezone | (UTC) UTC |

## Related resources

- [Cron expression reference](https://crontab.guru/)

## Common issues

- **Cron expression must use 5 fields**: the expression has too many or too few parts. Use `minute hour day month weekday`; seconds and years are not supported.
- **The workflow runs at the wrong hour**: the schedule follows the selected **Timezone**, which defaults to UTC. Pick your local timezone if you expect local times.
- **The schedule does not fire**: only an active workflow is scheduled. Publish and activate it; a draft or inactive workflow never runs on its own.
- **You need a run now**: use **Run** in the workflow toolbar. It executes the workflow immediately without waiting for the next scheduled time.
- **Each run re-processes everything**: enable **Sync only new and updated data** on nodes that support it (for example Shopware API Call **Get all**) so later runs only fetch changes.
