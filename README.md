# lowlevelboard-arduino-utils

## Add the Neocortex board pin mapping to Arduino IDE
The Neocortex board has broken out some pins that are not available on
an Arduino Mega and therefore are not mapped by the standard board definitions
provided along with the Arduino IDE.

Add the Neocortex pin definitions by following the steps below:

1. `File` :arrow_right: `Preferences`
2. In `Additional Boards Manager URLS` append
`https://raw.githubusercontent.com/CELLINKAB/lowlevelboard-arduino-utils/master/package_cellink_index.json`
by adding a `,` before if more entries are already present.
3. `Tools` :arrow_right: `Board` :arrow_right: `Boards Manager`
4. In the search bar, look for `Neocortex` and install the latest version
5. Select the `Neocortex` board by finding it in `Tools` :arrow_right: `Board`
