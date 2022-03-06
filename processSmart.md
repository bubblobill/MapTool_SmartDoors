Smart Door Processing Chain

Smart door processing commences at **doSmart(tokenIds)**
- Close the Smart door control overlay on all clients (only one person at a time can operate a door to avoid bad things happening).
- Use passed arguments or collate arguments to pass for selected doors using **doors.isSmart(tokenIds)**.
- **doors.isSmart(tokenIds)** returns either 0 (i.e. false) or a JSON object containing door token IDs as keys with individual door settings as JSON objects for values.
- If the value returned was a JSON object, launch the control overlay **doors.popup(vArgs)**.

```mermaid
graph LR;
    checkDoors(checkDoors)-->|token ids|doSmart(doSmart);
    doSmart(doSmart)-->|token ids|isSmart;
    isSmart-->|args object|doSmart(doSmart);
    doSmart(doSmart)-->|args object|popup(popup);
```

- **doors.popup(vArgs)**
  - Stop if more or less than one door selected.
  - Stop if editor is visible.
  - Get icons.
  - Send args for cleaning.
  - Get pinned condition.
  - Initialise variables with defaults.
  - Overwrite variables with door settings.
  - Use context to set icon visibility.
  - If options reduced to one;
    - set *skipPopup* flag,
    - add arguments for processing,
    - call **popupProcessor**.
  - else create control overlay.
- **popupProcessor**
  - switch on *action* value. Either set properties or set new value for action and update values in args for next step.
    - "pin"       (set pinned property true)
    - "pinned"    (set pinned property false)
    - "switch"    (toggle open)
    - "close"  
    - "open"      (open all the way)
    - "open1"     (open a little bit)
    - "open2"     (open more than a little bit)
    - "lock"      (set locked condition true)
    - "peek"      (toggle peek condition)
    - "secret"    (move to hidden layer)
    - "notsecret" (move to token layer)
    - "settings"  (open settings dialogue)
    - "unlock"    (set locked condition false)
    - "visible"   (set visible to players true)
    - "hidden"    (set visible to players false)
  - call **doors.interrupt(vArgs)**
    - **doors.interrupt(vArgs)** is a placeholder to allow users to insert their own calls and code. For instance, you could make a skill check to pick the lock, set off a trap, or any other funkyness.
  - pass args to **doors.action(vArgs)**
- **doors.action(vArgs)**
  - Where there are linked doors - for certain actions - prompt if action should be applied to linked doors
  - Loop through token ids, switch on *action* value.
    - "add"       : doors.addSmart(tokId)
    - "remove"    : doors.removeDoor(tokIds)
    - "link"      : doors.setLinks(tokIds)
    - "unlink"    : doors.unLink(tokIds)
    - "lock"      : doors.setLock(linkedTok, 1)
    - "lockToggle": doors.setLock(linkedTok)
    - "unlock"    : doors.setLock(linkedTok, 0)
    - "open"      : set openToAmount, playSound and a bunch of other stuff, doors.doOpen(linkedTok, json.get(linkedToks, linkedTok))
    - "peek"      : doors.doPeek(tokId, vSettings)
    - "peekOn"    : doors.setPeek(linkedTok, 1)
    - "peekOff"   : doors.setPeek(linkedTok, 0)
    - "lift"      : doors.setSubtype(linkedTok, "lift")
    - "slide"     : doors.setSubtype(linkedTok, "slide")
    - "swingCW"   : doors.setSubtype(linkedTok, "swingCW")
    - "swingCCW"  : doors.setSubtype(linkedTok, "swingCCW")
  - For "add" and "remove" set settings property with cleaned version.
  - Play sounds.
  - Expose FoW.
  - Deselect tokens
  - Refresh the GM panel "Door Controls"


```mermaid
graph LR;
   popup(Popup)-->|form values|popupProcessor(Popup Processor);
popupProcessor(Popup Processor)-->|action arguments|interrupt(Interrupt);
interrupt(Interrupt)-->|action arguments|popupProcessor(Popup Processor);
popupProcessor(Popup Processor)--->|action arguments|action0(Action);
action0(Action)--->act{Act};
action0(Action)-->finish[Stuff];
act{Act}-->action0(Action);
act{Act}-->|action arguments|add;
act{Act}-->|action arguments|remove;
act{Act}-->|action arguments|link;
act{Act}-->|action arguments|unlink;
act{Act}-->|action arguments|lock;
act{Act}-->|action arguments|lockToggle;
act{Act}-->|action arguments|unlock;
act{Act}-->|action arguments|open;
act{Act}-->|action arguments|peek;
act{Act}-->|action arguments|peekOn;
act{Act}-->|action arguments|peekOff;
act{Act}-->|action arguments|lift;
act{Act}-->|action arguments|slide;
act{Act}-->|action arguments|swingCW;
act{Act}-->|action arguments|swingCCW;
add        -->act{Act};
remove     -->act{Act};
link       -->act{Act};
unlink     -->act{Act};
lock       -->act{Act};
lockToggle -->act{Act};
unlock     -->act{Act};
open       -->act{Act};
peek       -->act{Act};
peekOn     -->act{Act};
peekOff    -->act{Act};
lift       -->act{Act};
slide      -->act{Act};
swingCW    -->act{Act};
swingCCW   -->act{Act};
add-->|action arguments|doors.addSmart;
remove-->|action arguments|doors.removeDoor;
link-->|action arguments|doors.setLinks;
unlink-->|action arguments|doors.unLink;
lock-->|action arguments|doors.setLock;
lockToggle-->|action arguments|doors.setLock;
unlock-->|action arguments|doors.setLock;
open-->|action arguments|doors.doOpen;
peek-->|action arguments|doors.doPeek;
peekOn-->|action arguments|doors.setPeek;
peekOff-->|action arguments|doors.setPeek;
lift-->|action arguments|doors.setSubtype;
slide-->|action arguments|doors.setSubtype;
swingCW-->|action arguments|doors.setSubtype;
swingCCW-->|action arguments|doors.setSubtype;

```
