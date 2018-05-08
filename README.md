# U8g2_Arduino: Arduino Monochrome Graphics Library
[![Build Status](https://travis-ci.org/Adam5Wu/U8g2_Arduino.svg?branch=adam5wu/master)](https://travis-ci.org/Adam5Wu/U8g2_Arduino)
[![GitHub issues](https://img.shields.io/github/issues/Adam5Wu/U8g2_Arduino.svg)](https://github.com/Adam5Wu/U8g2_Arduino/issues)
[![GitHub forks](https://img.shields.io/github/forks/Adam5Wu/U8g2_Arduino.svg)](https://github.com/Adam5Wu/U8g2_Arduino/network)
[![License](https://img.shields.io/github/license/Adam5Wu/U8g2_Arduino.svg)](./LICENSE)

Modified with one feature:
* Allows storing font glyph data in files, in addition to embed within sketch
	* Benefits:
		1. Reduced sketch binary size, especially when handling heavy font sets (e.g. GB2312)
		2. Change fonts at run-time becomes possible

Caveat:
* Current implementation require in-memory glyph data cache
	* There are two caching modes:
		1. [Default] Dynamic memory allocation, will consume memory up to the largest single glyph in the font
			* Requires `malloc()` and `free()` support
			* Requires additional call to `u8g2_CleanupBuffer()` to release buffer
				* C++ `U8G2` objects will automatically invoke in destructor
		2. Static memory allocation, will consume fixed amount of memory
			* Does not require memory management or cleanup
			* If not enough to contain a glyph data from font file, glyph will NOT be printed
				* Limitation only applies to file based font
* Current implementation incurs observable slow down when using font file compared with embedded font
	* Using Shennong example's per frame glyph drawing time as benchmark
		- Embedded font: 21ms per frame
		- Font file (no cache): 188ms per frame (7.95x slow down)
		- Font file (+scan cache): 156ms per frame (6.43x slow down)
			- Consumes extra 256 bytes memory for faster glyph scan operation
		- Font file (+glyph index cache): 138ms per frame (5.57x slow down)
			- Consumes extra 400 bytes memory for faster index lookup
		- [Default] Font file (+scan cache +glyph index cache): 114ms per frame (4.43x slow down)
			- Consumes extra 656 bytes memory for faster glyph lookup

* [Upstream Project](https://github.com/olikraus/U8g2_Arduino)
* Potentially interesting:
	- [ESP8266 Arduino Core fork](https://github.com/Adam5Wu/Arduino-esp8266)
  - [ZWUtils-Arduino](https://github.com/Adam5Wu/ZWUtils-Arduino)
  - [ESPVFATFS](https://github.com/Adam5Wu/ESPVFATFS)
