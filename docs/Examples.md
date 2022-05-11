# Examples

This section provides examples of Constraint Sets expressing metadata from different EDID blocks.

## Established Timings

Given the three bytes of Established Timings I & II were `20 08 00` then the Receiver Capabilies could contain the following Constraint Sets:

```json
[
  {
    "urn:x-nmos:cap:format:frame_width": {
      "enum": [ 640 ]
    },
    "urn:x-nmos:cap:format:frame_height": {
      "enum": [ 480 ]
    },
    "urn:x-nmos:cap:format:interlace_mode": {
      "enum": [ "progressive" ]
    },
    "urn:x-nmos:cap:format:grain_rate": {
      "enum": [ { "numerator": 60 } ]
    }
  },
  {
    "urn:x-nmos:cap:format:frame_width": {
      "enum": [ 1024 ]
    },
    "urn:x-nmos:cap:format:frame_height": {
      "enum": [ 768 ]
    },
    "urn:x-nmos:cap:format:interlace_mode": {
      "enum": [ "progressive" ]
    },
    "urn:x-nmos:cap:format:grain_rate": {
      "enum": [ { "numerator": 60 } ]
    }
  }
]
```

## Standard Timings

Given the bytes of Standard Timings were `D1 C0 01 01 01 01 01 01 01 01 01 01 01` then the Receiver Capabilies could contain the following Constraint Sets:

```json
[
  {
    "urn:x-nmos:cap:format:frame_width": {
      "enum": [ 1080 ]
    },
    "urn:x-nmos:cap:format:frame_height": {
      "enum": [ 1920 ]
    },
    "urn:x-nmos:cap:format:interlace_mode": {
      "enum": [ "progressive" ]
    },
    "urn:x-nmos:cap:format:grain_rate": {
      "enum": [ { "numerator": 60 } ]
    }
  }
]
```
