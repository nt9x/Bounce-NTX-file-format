# Bounce NTX file format
The texture format used in all 3D Bounce games by Rovio - except Bounce Evolution. 

# Specification
The entire format is little endian, with almost all values being unsigned 2-byte integers.
An NTX image supports 2 color types - ARGB4444, which is only used for GUI elements, and
RGB565, which is only used for model textures. If specified, an RGB565 image may remove all
occurences of the color 0x0000, to simulate transparency. This mechanic is used primarily
in 2.5D models, such as decorations and hypnotized eyes of the mole and duck (Bounce Touch
and Bounce Boing Voyage, level 1).

The format structure is as follows:
```
--HEADER--
-------------------
BYTES   DESCRIPTION
-------------------
2       Must equal 0x104 - identifies a valid NTX file.
2       Texture width
2       Texture height
2       Color type -> 7 = RGB565, 15 or 16 = ARGB4444
2       No. of colors in palette -> P
2       Whether to use color 0x0000 (full black) for transparency. (1 = yes, 0 = no)
2       Unused. Ignore this value.
2P      Palette entries
X       Image data
--------------------

--IMAGE DATA--
If P equals 0, X = 2, and each pixel is a raw color of the type that was specified.
If P is greater than 0, X = 1, and each pixel is a 1-byte entry ID from the palette.
If P is greater than 256, there are too many colors and the texture is invalid.

--EXAMPLE--
Indexed image:
--------+-----------------------+--------+
Offset  |00 01 02 03 04 05 06 07|Text    |
--------+-----------------------+--------+
00000000|04 01 02 00 03 00 10 00|........|(width=2, height=2, type=ARGB4444)
00000008|02 00 00 00 00 00 00 FF|.......ÿ|(color count=2, remove black=false)
00000010|55 FF 00 01 01 00 00 01|Uÿ......|(Some color and image data...)
--------+-----------------------+--------+
The data above produces a simple 2x3 checker-like image
with colors #FF0000 (red) or #FF5555 (lighter red), both
at full opacity.
```
