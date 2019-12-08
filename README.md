# Device Tree overlays for sunxi devices

### :orange_book: [End user documentation](https://docs.armbian.com/User-Guide_Allwinner_overlays/)

### :speech_balloon: [Forum thread for issues and suggestions](https://forum.armbian.com/index.php?/topic/3787-testers-wanted-sunxi-device-tree-overlays/)

## Version

As of now overlays in this repository should work with the kernel version **5.0.x**.
They may need changes/adjustments to work on older versions and eventually they will be updated to newer kernel versions.

## Technical info

##### Requirements

- mainline u-boot 2017.03 or newer with `CONFIG_OF_LIBFDT_OVERLAY` enabled
- latest version of appropriate boot script
- existing armbianEnv.txt with correct `overlay_prefix` value
- Device Tree compiler with overlays support for compiling the overlays

Notes:

- Older u-boot versions require [this](http://git.denx.de/?p=u-boot.git;a=commitdiff;h=b05bf6c75d03c925737e228472b694cbeaa503c2) patch to fix endiannes of values obtained with `fdt get value` command

##### Implementation details

Boot script reads `/boot/armbianEnv.txt` which may contain following environment variables:

- `overlay_prefix`
- `overlays`
- `user_overlays`
- overlay specific parameters

Overlay files referenced by `overlays` and `user_overlays` variables are loaded and applied using `fdt apply` command. After applying all overlays a SoC specific fixup script is executed to process overlay specific parameters.

##### Limitations

- U-boot `fdt` command does not support "tuples" values which limits implementing things like GPIO SPI chip selects with variable GPIO pins that require this type of values:

```
cs-gpios = <0>, <&pio 0 1 0>, <&pio 7 7 0>; /* Native, PA1, PH7 */
```

- U-boot does not support overlay parameters, so changing values is implemented via executing a "fixup" script after all overlays were applied. This script uses environment variables loaded from `/boot/armbianEnv.txt` to change the live tree using `fdt` command.

- Since SoCs have multiple controllers of the same type (I2C, SPI) that can be exposed in different combinations on different boards, slave devices (I2C, I2S, SPI, ...) are provided not as ready to use overlays but as examples that require minimal editing to match the used configuration.
