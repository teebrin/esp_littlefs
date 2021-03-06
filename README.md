LittleFS for ESP-IDF.

# What is LittleFS?

[LittleFS](https://github.com/ARMmbed/littlefs) is a small fail-safe filesystem 
for microcontrollers. We ported LittleFS to esp-idf (specifically, the ESP32) 
because SPIFFS was too slow, and FAT was too fragile.

# How to Use

In your project, add this as a submodule to your `components/` directory.

```
git submodule add https://github.com/joltwallet/esp_littlefs.git
```

The library can be configured via `make menuconfig` under `Component config->LittleFS`.

# Documentation

See the official ESP-IDF SPIFFS documentation, basically all the functionality is the 
same; just replace `spiffs` with `littlefs` in all function calls.

Also see the comments in `include/esp_littlefs.h`

# Performance

Here are some naive benchmarks to give a vague indicator on performance.

Formatting a ~512KB partition:

```
FAT:         963,766 us
SPIFFS:   10,824,054 us
LittleFS:  2,067,845 us
```

Writing 5 88KB files:

```
FAT:       14,711,396 us
SPIFFS:   154,238,375 us
LittleFS:  10,344,878 us
```

In the above test, SPIFFS drastically slows down as the filesystem fills up. Below
is the specific breakdown of file write times for SPIFFS. Not sure what happens 
on the last file write.


```
SPIFFS:

88000 bytes written in 6945139 us
88000 bytes written in 6945135 us
88000 bytes written in 11090827 us
88000 bytes written in 27832679 us
22784 bytes written in 101424595 us
```

Deleting 5 88KB files:

```
FAT:      1,216,137 us
SPIFFS:   3,712,190 us
LittleFS:    32,827 us
```


# Limitations

Currently only an absolute maximum of *20* files can be opened at once. This can be
increased by increasing the `esp_littlefs_t.fd_used` bitmask to a larger datatype, 
and changing `ABSOLUTE_MAX_NUM_FILES`. 

# Tips, Tricks, and Gotchas

* LittleFS operates on blocks, and blocks have a size of 4096 bytes on the ESP32.

* A freshly formatted LittleFS will have 2 blocks in use, making it seem like 8KB are in use.

# Running Unit Tests

To flash the unit-tester app and the unit-tests, run


```
make tests
```

# Acknowledgement

This code base was heavily modeled after the SPIFFS esp-idf component.
