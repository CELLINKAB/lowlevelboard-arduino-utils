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

## Updating board packages

Creating a new board package version involves updating the `./neocortex` directory as desired, creating a `.zip` archive from it with the correct version name, updating the `package_cellink_index.json` file, and pushing these changes to the desired branch of the Git repository.

### Modifying the board package

The [3rd party Hardware Specification document](https://github.com/arduino/Arduino/wiki/Arduino-IDE-1.5-3rd-party-Hardware-specification) governs the syntax for board and platform definitions, as well as describing how the package should be deployed.

You can add different build targets as top level "boards", or alternatively as configurations of a single board. You can define such configuration options as "menu items" on top of the `boards.txt` file. These menu items will appear in the Arduino IDE "Tools" menu, under the board selection. These configurations can be used to override the board defaults. 

:point_up: Update the version number in `platform.txt`.

### Packaging and Deployment

1. Create a `.zip` archive from the `./neocortex` folder and name the archive `neocortex-X.X.X.zip`
2. The `package_xxx_index.json` document is used by the Arduino IDE to install the board package, and needs to be updated. Update the `"version"` and the `"archiveFileName"`. The specification for the package index is described in this [document](https://github.com/arduino/Arduino/wiki/Arduino-IDE-1.6.x-package_index.json-format-specification).

If, for instance, you would add a new board to `boards.txt` called Neovortex, this would also be referenced in the package index document under the `"boards"` key:

```json
    "boards": [
        {"name": "Neocortex"},
        {"name": "Neovortex"}
    ],
```

3. The archive's size (in bytes) and SHA-256 hash needs to be updated in the `package_cellink_index.json`. In order to get the hash, run:

    ```bash
    sha256sum ./neocortex-X.X.X.zip
    ```

#### Testing

1. Commit everything (do not delete the previous `.zip` archive) to a testing branch. You will need to specify the name of the testing branch in the `package_cellink_index.json` document. This is a circular reference that needs to be edited before merging into the master branch!
2. Follow the instructions to add the board to the IDE, but make sure you specify the test branch in the URL.
3. Start the Arduino IDE, remove the package and search for it again. You might need to clean up the cached download files in the arduino IDE directory. When you search for "Neocortex" in the board manager, the IDE will download the package index file. If the package parsing of this fails, you will get an error in the IDE console. Do not count on Arduino to clean up the files, remove them manually.

#### Deployment

1. After testing, edit the URL in the package index document and commit again.


#### Build pipeline

1. In the yocto recipe, this package is pulled via it's commit hash. This commit ID needs to be updated in the Yocto recipe in the file "neocortex-arduino-native_0.0.1.bb"

2. Lastly, in the Jenkins configuration panel scroll down to the "Execute shell" script, and add the new recipe to the list of recipe configurations that need to be generated for "local.conf". For example:

```bash
if [ -n "${BIOX_TORSK_FIRMWARE_BRANCH}" ]; then
	echo "BIOX_INTEGRATION_BRANCH_pn-rombov-firmware = \"${BIOX_TORSK_FIRMWARE_BRANCH}\"" >> conf/local.conf
	echo "BIOX_INTEGRATION_BRANCH_pn-rombov2-firmware = \"${BIOX_TORSK_FIRMWARE_BRANCH}\"" >> conf/local.conf
    echo "BIOX_INTEGRATION_BRANCH_pn-rombov3-firmware = \"${BIOX_TORSK_FIRMWARE_BRANCH}\"" >> conf/local.conf
fi
```