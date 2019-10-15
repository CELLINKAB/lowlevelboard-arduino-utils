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

See the 3rd party Hardware specification that governs the board packages to be used to define boards:

<https://github.com/arduino/Arduino/wiki/Arduino-IDE-1.5-3rd-party-Hardware-specification>

1. Update the version number in `platform.txt`.

### Packaging and Deployment

1. Create a `.zip` archive from the `./neocortex` folder and name the archive `neocortex-X.X.X.zip`
2. The `package_xxx_index.json` document is used by the Arduino IDE to install the board package, and needs to be updated. Update the `"version"` and the `"archiveFileName"`. The specification for the package index is described in the following document:

    <https://github.com/arduino/Arduino/wiki/Arduino-IDE-1.6.x-package_index.json-format-specification>

For instance, if you added a new board to `boards.txt`, this needs to be refered to under the `"boards"` key like this:

```json
    "boards": [
        {"name": "Neocortex"},
        {"name": "Neocortex2"}
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
