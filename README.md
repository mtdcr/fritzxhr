# FritzXHR: An interface to FRITZ!Box web settings
For some settings of a FRITZ!Box there's no documented API. They can usually
only get modified by the use of a web browser.

This small library aims to simplify write access to those settings.

For now, it only implements a pretty raw interface to `data.lua` through the `set_data`
method, but it's easy to extend if other API endpoints are required.

## Dependencies
* Python 3.6+
* [defusedxml](https://pypi.org/project/defusedxml/)
* [requests](https://pypi.org/project/requests/)

## How to use
Here's an example to configure a mobile phone number to be called when somebody
rings your doorbell:

```python
#!/usr/bin/env python3

from fritzxhr import FritzXHR

mobile_phone = "017012345678"
outgoing_number = 555666

data = {
    "Name0": "Doorbell",
    "Num_Org0": "11",
    "Signal0" = "outNum",
    "Num_Rep0" = mobile_phone,
    "ringtone0": "3",
    "name": "Door phone",
    "out_num": outgoing_number,
    "opener": "",
    "pre_video": "http://",
    "video": "",
    "ip_idx": "0",
    "back_to_page": "/fon_devices/fondevices_list.lua",
    "popup_url": "",
    "apply": "",
    "oldpage": "/fon_devices/edit_doorline_num.lua",
}

fb = FritzXHR(
    host="https://fritz.box",
    username="fritzbox-admin",
    password="fritzbox-password",
    verify="/etc/ssl/certs/custom_ca.pem",
)

if fb.login():
    if fb.has_access('BoxAdmin'):
        fb.set_data(data)
    fb.logout()
```
All parameters to `FritzXHR` are optional. See the source code for default values.

The `data` dict above was captured using a web browser like Chrome or Firefox:
* Browse to the settings page you'd like to modify
* Right-click on that page
* Select "Inspect" or "Inspect Element"
* Select the "Network" tab
* Press the "Apply" button
* Look for `POST` requests to `data.lua`

FritzXHR automatically adds `xhr`, `sid`, `lang` and `no_sidrenew`, so it's safe to leave them out.
