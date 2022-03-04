# Notes and Things
## Internationalisation
Set the language using the dropdown in the **settings** input macro.
All display text is stored in a JSON object in a library property.
The object structure is *language -> key, value*, e.g.
```{
"en_au", {
  "door.added", "New door created",
  ...
  },
"nl", {
  "door.added", "Nieuwe deur aangemaakt",
  ...
  }
}
```
To alter the messages object, update the JSON setting data in the **messages.json** macro and run it.
Text is fetched using the UDF **doors.getText("key name")**.
Should return text like *Translation text not found* where a key is not present, and *Translation lookup failed for ...* where there is no localised message "translationMissing".
## Icons
Icons used in frames and overlays are SVG images stored in a library property.
To change the store of SVG images, update the contents of **setSVGIcons** and run it.
SVG is stored as a JSON object with a name for the icon and the SVG as the value, e.g.
```
... 'unlock',
'<?xml version="1.0" encoding="UTF-8"?>
<svg version="1.1" viewBox="0 0 48 48" xmlns="http://www.w3.org/2000/svg" id="swingCW">...
</svg>', ...
```
## onTokenMove
For players to be able to click on doors and interact with them they also have the ability to move them with errant mouse actions.<br>
The **onTokenMove** event blocks doors being moved that have been pinned.
## onCampaignLoad
**onCampaignLoad** does the following;
- creates UDFs with a prefix. Utilise UDFs with "doors." prefix, e.g. *doors.getText()*
- starts the door handling overlay, 
- creates image tables from door tokens on the map with some confusing wizardry thanks to Wolph42.
##  checkDoors (where it all starts)
**checkDoors** contains the HTML for the overlay *Door Handler*.
*Door Handler* exists simply to utilise the *onChangeSelection* event to call **checkDoors**.
The **checkDoors** process is;
- Abort if door functionality is turned off
- If there is only one token selected and it is a Simple door, set the *toggleDoor* flag.
- Close the Smart door control overlay.
- If token(s) selected are Smart doors.
  - Close the Smart door control overlay on all clients (only one person at a time can operate a door to avoid bad things happening).
  - Start the Smart door process chain **doors.doSmart(vSmart)**
- If *toggleDoor* flag is set, close or open the door based on it's current state.
