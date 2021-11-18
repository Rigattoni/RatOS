# Changelog

## RatOS V1.0

### Updating from V-CoreOS-RC2

Updating to RatOS v1.0 requires you to flash the SD Card in your Raspberry Pi, as well as your control board following the usual steps (has to be done manually via SD card transfer) found in the [Installation Section](docs/installation). That also means that any changes you've made to your Raspberry Pi setup will be lost unless you make a backup first. If you want to be absolutely sure you don't loose something, you can use a new SD card for RatOS v1.0. These are common things you'll probably want to backup:

- printer.cfg (you can download this from Fluidd)
- .moonraker_database/\* (located in /home/pi/.moonraker_database on your Raspberry Pi. Download data.mdb and lock.mdb with SCP/WinSCP then upload them to that same location after installation of RatOS v1.0)
- Any custom setup you've done, that isn't replaced with native RatOS functionality.

If you're reuploading your printer.cfg from V-CoreOS-RC2 you can (optionally) add this to the bottom of printer.cfg (right above the ### USER OVERRIDES section):

```properties
### MACRO CONFIGURATION
[gcode_macro RatOS]
# Use relative extrusion mode
# Set to False to use absolute extrusion mode
variable_relative_extrusion: True
# Wait for extruder to reach 150 so an inductive probe (if present) is at a predictable temp.
# Also allows the bed heat to spread a little, and softens any plastic that might be stuck to the nozzle.
# Set to False to disable
variable_preheat_extruder: True
# Calibrate the bed mesh in the START_PRINT macro.
# Set to false to skip BED_MESH_CALIBRATE, it will still load the BED_MESH
# with the name "ratos", be sure to save your bed_mesh profile with that name.
# or override the _START_PRINT_BED_MESH macro to implement your own mesh handling logic.
variable_calibrate_bed_mesh: True
# Print a prime line at the end of the START_PRINT macro
# set to False to disable nozzle_priming.
variable_nozzle_priming: "primeline"
# Park in the back when waiting for the extruder to heat up
# set to "front" to park in the front, or "center" to park in the center.
variable_start_print_park_in: "back"
# Height to park it when waiting for extruder to heat.
variable_start_print_park_z_height: 50
# Skew profile to load before starting the print
# uncomment this to use your calibrated skew correction profile.
#variable_skew_profile: "my_skew_profile"
# Park in the back after the print has ended or was cancelled.
# set to "front" to park in the front, or "center" to park in the center.
variable_end_print_park_in: "back"
# Park in the back when the print is paused.
# set to "front" to park in the front, or "center" to park in the center.
variable_pause_print_park_in: "back"
# Set the speed for travel moves in RatOS Macros in mm/s.
variable_macro_travel_speed: 150
```

If the section is not present in your printer.cfg the default values will be used. See [Macro Configuration](/docs/configuration/macros) for information on how to use the variables.

### General

- Since the future of Fluidd is uncertain, RatOS has migrated to Mainsail.
- KlipperScreen now comes preinstalled.
- Picking your printer is now done via Macros on first startup.

### Printers

- Add V-Core Pro 1.3 support

### Boards

- Firmware compilation has been automated! Firmware compilation and flashing is now attempted when the klipper repository is updated.
- Fysetc Spider v1.1 support added
- SKR 2 /w STM32F429 support added
- Udev symlinks are now automatically updated when the ratos config is updated. That means new boards are mapped automatically.
- RPi MCU added (automatically updated with klipper)
- make-and-flash-mcu.sh now copies the firmware binary into a firmware_binaries folder browsable in mainsail for manual flashing via SD Card if needed.

### Macros

- gcode_shell_command.py has been added.
- Added `INSTALL_VCORE_THREE_CONFIG`, `INSTALL_VCORE_PRO_CONFIG`, `INSTALL_VMINION_CONFIG` macro's for initial setup.
- Added COMPILE_BINARIES macro to compile binaries for all supported boards in case the user got himself into a bad situation and needs the newest firmware.
- Added GENERATE_SHAPER_GRAPHS which runs resonance test and generates graphs which are placed in the `input_shaper` folder.
- Macro's now have configurable behavior, the following variables are available: `relative_extrusion`, `preheat_extruder`, `calibrate_bed_mesh`, `nozzle_priming`, `start_print_park_in`, `start_print_park_z_height`, `end_print_park_in`, `pause_print_park_in` and `macro_travel_speed`.
- `START_PRINT` now has 4 hooks: `_START_PRINT_AFTER_HEATING_BED`, `_START_PRINT_BED_MESH`, `_START_PRINT_PARK` and `_START_PRINT_AFTER_HEATING_EXTRUDER`. You can override these in your printer.cfg to further customize the way your `START_PRINT` macro behaves.
- `END_PRINT` now has 2 hooks: `_END_PRINT_AFTER_HEATERS_OFF` and `_END_PRINT_PARK`. You can override these in your printer.cfg to further customize the way your END_PRINT macro behaves.
- `END_PRINT` and PAUSE now moves to a safe Z distance instead of always moving the same distance which could lead to errors.
- `M600` is now supported.