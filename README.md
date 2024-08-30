# buildtogether2

Some code for Hackster Build2gether 2.0 — Inclusive Innovation Challenge

The project is composed of three parts, the first for the management of the Blues Notecard-A, the second for the management of the Seeed Studio XIAO ESP32S3 Sense. the third that configures and manages the HUNIHIKER board.
1. Blues Notecarrier-A configuration.
   you can configure the board using Blues documentation, rich of examples and use cases (https://dev.blues.io/accelerators/). For my project I need the board to read exact position from GPS and extract latitute, longitude and temperature information from the JSON data obtained. We need to use something like this:
   { "req": "hub.set", "product" : "com.your-company:your-product-name", "sn": "a-serial-number", "mode": "periodic", "outbound": 30, "body":{"app":"nf14"} }

   The board is very interesting because, although it is designed to minimize consumption, it can be configured to meet the user's requests. By default, the board sends the acquired data only if there are variations in the data itself, in the case of tracking an object or a person, for example, it will send updated data only if the tracked object moves. However, we can set the board to transmit data periodically, with a frequency decided by the user, thus having higher consumption but better precision in tracking.
   When you configure it, you can find the data on https://notehub.io/project/.../events WEB page.
   
 ![Notehub-third-Events](https://github.com/user-attachments/assets/46870f53-264e-488b-8e68-bc5438500c50)

If you want to extract the information you need from this WEB page, you have to generate the project keys from the WEB page: https://notehub.io/project/.../settings/project , section Programmatic API access. You will obtain 2 keys: Client ID and Client Secret that you have to save in a safe place.
Now you can create a session token to connect to Notehub site being authenticate.
To create the session token you have to send a request to the server using Curl: 
curl -X POST -L "https://notehub.io/oauth2/token" -H "content-type: application/x-www-form-urlencoded" -d grant_type=client_credentials -d client_id=... client_secret=...
The server will send a session token:
{"access_token":"8T3bp1eskNb9VDcL87DPakzoVi0XHae0N2dG9NzjCDM.WgWOQ6RJqwW9krhdf6Vrww71wyJUYp5yXh0h37mA8rQ","expires_in":1799,"scope":"","token_type":"bearer"}

Remember that you have to update the token every 1800 seconds.

Now you can ask the server for latitude, longitude and temperature data simply sending three requestes using Curl:

curl -s -X GET -L "https://api.notefile.net/v1/projects/.../devices" -H "Authorization: Bearer 8T3bp1eskNb9VDcL87DPakzoVi0XHae0N2dG9NzjCDM.WgWOQ6RJqwW9krhdf6Vrww71wyJUYp5yXh0h37mA8rQ" | jq ".devices[0].gps_location.latitude"

curl -s -X GET -L "https://api.notefile.net/v1/projects/.../devices" -H "Authorization: Bearer 8T3bp1eskNb9VDcL87DPakzoVi0XHae0N2dG9NzjCDM.WgWOQ6RJqwW9krhdf6Vrww71wyJUYp5yXh0h37mA8rQ" | jq ".devices[0].gps_location.longitude"

curl -s -X GET -L "https://api.notefile.net/v1/projects/.../devices" -H "Authorization: Bearer 8T3bp1eskNb9VDcL87DPakzoVi0XHae0N2dG9NzjCDM.WgWOQ6RJqwW9krhdf6Vrww71wyJUYp5yXh0h37mA8rQ" | jq ".devices[0].gps_location.temperature"

The HUNIHIKER board will contact the Notehub server to generate session tokens and to receive latitude, longitude and temperature every time it will need such data.



