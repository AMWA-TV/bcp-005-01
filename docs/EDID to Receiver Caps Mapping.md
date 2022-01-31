# AMWA BCP-005-01: EDID to NMOS Receiver Capabilities Mapping \[Work In Progress\]
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

## Introduction

The purpose of AMWA BCP-005-01 is to provide Best Current Practice guidelines for mapping EDID fields to Receiver Capabilities in the case Receiver is associated with an Output connected to a downstream counterpart which provides EDID.

The proposed mapping provides the mechanism for converting EDID information into Receiver Capabilities. The effective Receiver Capabilities MAY differ from those obtained from the proposed mapping when the Receiver is:

- Capable of altering signal (e.g. it is able to consume 4K and downscale it before passing to the Output which downstream counterpart supports up to HD resolutions)
- Constrained beyond the capabilities of the downstream counterpart of the Output (e.g. it supports resolution up to 1080p but the Output is connected to a 4K monitor)

The term 'Receiver' used in this document is defined in [IS-04][IS-04].

The terms 'Parameter Constraint' and 'Constraint Set' used in this document are defined in [BCP-004-01][BCP-004-01].

The term 'Output' used in this document is defined in [IS-11][IS-11].

BCP-005-01 is intended to be used in conjunction with an [IS-11][IS-11] and [BCP-004-01][BCP-004-01] deployment; however it has been written in such a way to provide useful functionality even in the absence of such a system.

This document is targeted against [E-EDID A2][E-EDID] which consist of EDID 1.4 (and covers EDID 1.3) and is referred to as _Base EDID_ and the [CTA-861-G][CTA-861] Extension Block imposed by HDMI. The information present in the EDID is subject to the requirements of the [Display Monitor Timing (DMT)][DMT] specification Version 1 Rev 12.

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Video Receivers

If an [IS-04][IS-04] Video Receiver is associated with an Output which has an EDID, the optional mapping of the EDID supported video formats into Receiver's Capabilities SHALL be performed according to the rules below.

### Video Timings

Video timings, described in [E-EDID][E-EDID], provide information about the video frame size and rate. There are multiple blocks which keep information about timings each with their own mapping requirements.

Each video timing SHOULD be present in the Receiver's Capabilities as a Constraint Set with a non-empty

- `urn:x-nmos:cap:format:frame_width`
- `urn:x-nmos:cap:format:frame_height`
- `urn:x-nmos:cap:format:grain_rate` which MUST be the exact frame rate of the signal

The timing descriptors MAY include one or more of the following mappings:

- `urn:x-nmos:cap:format:interlace_mode`
- `urn:x-nmos:cap:meta:preference`

Each of these Parameter Constraints MUST use `enum` Constraint Keyword.

Video timings with _Reduced Blanking_ MAY be determined by the exact `urn:x-nmos:cap:format:grain_rate`.

#### Established Timings I, II & III

Each Established Timing is an ID associated with a predefined frame width, height and rate and interlace mode. Mapping the first two types are defined in [E-EDID][E-EDID] section 3.8 and the last in [E-EDID][E-EDID] section 3.10.3.9.

<details><summary>Example: Established Timings I & II</summary>

For example, given the three bytes of Established Timings I & II were `20 08 00` then the receiver capabilies could contain the following in its constraint sets

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

</details>

#### Standard Timings

Standard Timing Definitions (STD) format is defined in [E-EDID][E-EDID] section 3.9. The mapping is as follows:

- `urn:x-nmos:cap:format:frame_width` MUST be calculated from the _Horizontal Active Pixel Count_
- `urn:x-nmos:cap:format:frame_height` MUST be calculated using the _Frame Width_ and _Image Aspect Ratio_
- `urn:x-nmos:cap:format:grain_rate` MUST be calculated with _Field Refresh Rate_

Additionally, defined in [DMT][DMT] section 1 are the interlace mode for (STD) codes

- `urn:x-nmos:cap:format:interlace_mode` MUST be set according Table 1-1 Standards and Guidelines

<details><summary>Example: Standard Timings</summary>

For example, given the bytes of Standard Timings were `D1 C0 01 01 01 01 01 01 01 01 01 01 01` then the receiver capabilies could contain the following in its constraint sets

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

</details>

#### Detailed Timing Descriptors (18 Byte Descriptors)

Defined in [E-EDID][E-EDID] section 3.10, there are 4 possible descriptors which can be provided. The first, _Prefered Timing Mode_ is an obligation and MUST have a `urn:x-nmos:cap:meta:preference` value of `100`. Any Detailed Timing Descriptors in [CTA-861][CTA-861] Extension BLock (section 7.2.1) MUST follow the mapping.

The mapping is defined in section 3.10.2 and is applied as follows:

- `urn:x-nmos:cap:format:interlace_mode` MUST be set according to _Signal Interface Type_ defined in table 3.22
- `urn:x-nmos:cap:format:frame_width` MUST be _Horizontal Addressable Video in pixels_
- `urn:x-nmos:cap:format:frame_height` MUST be calculated using the _Vertical Addressable Video in lines_ which MUST be multiplied by 2 when _Signal Interface Type_ indicates interlaced
- `urn:x-nmos:cap:format:grain_rate` SHALL be accurately represented by
  - `numerator` MUST be calculated using _Pixel Clock_
  - `denominator` MUST be calculated with _Horizontal Addressable Video in pixels_, _Horizontal Blanking in pixels_, _Vertical Addressable Video in lines_ and _Vertical Blanking in lines_

#### 3 Byte CVT Codes

3 Byte CVT Code structure is defined in [E-EDID][E-EDID] section 3.10.3.8.

- `urn:x-nmos:cap:format:frame_height` MUST be calculated using value of the CVT Code
- `urn:x-nmos:cap:format:frame_width` MUST be calculated with Frame Height and _Aspect Ratio_

_Preferred Vertical Rate_ united with the frame width and height makes a Constraint Set and SHOULD have a `urn:x-nmos:cap:meta:preference` value off `50`

Each _Supported Vertical Rate_, except _Preferred Vertical Rate_, MUST be extracted to a new Constraint Set with the same frame width and height.

#### DMT Standard Codes & IDs Summary

DMT Codes are used to augment the Standard Timing Descriptors and CVT Codes for some EDID IDs. Extra calcuations are REQUIRED to obtain the exact frame rate as per [DMT][DMT] section 2 and section 4.

- `urn:x-nmos:cap:format:grain_rate` MUST be calculated and represent the exact frame rate. The rational MUST be reduced to the smallest possible numerator.

<details><summary>Example: DMT Standard Codes</summary>

For example, the EDID with ID `DMT ID: 45h; Std. 2 Byte Code: (D1, 00)h; CVT 3 Byte Code: (57, 28, 28)h` then the receiver capabilies could contain the following in its constraint sets

```jsonc
[
  {
    "urn:x-nmos:cap:format:grain_rate": {
      "enum": [
        { "numerator": 2403125, "denominator": 40338 } // When reduced by GCD of 80
      ]
    }
  }
]
```

</details>

#### Video Data Block

Video Data Block is defined in [CTA-861][CTA-861] section 7.5.1.

It operates with Video Identification Codes (VICs), each of them is associated with a union of frame width, height and rate and interlace mode. This mapping is defined in [CTA-861][CTA-861] section 4.1.

### Color subsampling

If EDID doesn't have the [CTA-861][CTA-861] Extension Block, color subsampling formats MUST be taken from Base EDID, otherwise from the Extenstion Block.

#### Base EDID

The origin of supported color subsampling formats in Base EDID is the Feature Support in [E-EDID A2][E-EDID] section 3.6.4.

It has one of four possible values:

- RGB 4:4:4
- RGB 4:4:4 & YCbCr 4:4:4
- RGB 4:4:4 & YCbCr 4:2:2
- RGB 4:4:4 & YCbCr 4:4:4 & YCbCr 4:2:2

This value MUST be transformed into `urn:x-nmos:cap:format:color_sampling` with `enum` values according to those permitted by the [NMOS Parameter Registers - Capabilities][NMOS Capabilities] section and added to each Constraint Set.

#### CTA-861 Extension Block

The supported color subsampling formats in the CTA Extension Header ([CTA-861][CTA-861] section 7.5) indicate `YCbCr-4:2:2` and `YCbCr-4:4:4` support in addition to `RGB`.

YCbCr 4:2:0 Capability Map Data Block ([CTA-861][CTA-861] section 7.5.11) shows which timings support `YCbCr-4:2:0` in addition to subsampling formats listed in the CTA Extension Header. These timings MUST contain `YCbCr-4:2:0` within the possible values for `urn:x-nmos:cap:format:color_sampling` with associated Constraint Set.

YCbCr 4:2:0 Video Data Block ([CTA-861][CTA-861] section 7.5.10) marks timings as supporting only `YCbCr-4:2:0`. Constraint Set associated with these timings MUST have `urn:x-nmos:cap:format:color_sampling` limited to `YCbCr-4:2:0`.

## Audio Receivers

If Basic Audio support bit is active in the CTA Extension Header, audio receiver MUST have Receiver Capabilities.

When no descriptors are provided, the capabilities MUST contain:

- `urn:x-nmos:cap:format:media_type` MUST be equal to `audio/L8`
- `urn:x-nmos:cap:format:channel_count` MUST be equal to 2

### Short Audio Descriptors

If there are Short Audio Descriptors (see [CTA-861][CTA-861] section 7.5.2), then each of them MUST be transformed into a Constraint Set.

- `urn:x-nmos:cap:format:media_type` MUST be determined with _Audio Format Code_
  - Linear PCM bit depth MUST be taken from the third Short Audio Descriptors byte
- `urn:x-nmos:cap:format:channel_count` MUST be determined by _Number of Channels_
- `urn:x-nmos:cap:format:sample_rate` MUST be filled with _Sampling Frequencies_

Each of these Parameter Constraints MUST use `enum` Constraint Keyword.

[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs to Indicate Requirement Levels"
[IS-04]: https://specs.amwa.tv/is-04 "AMWA IS-04 NMOS Discovery and Registration Specification (Stable)"
[IS-11]: https://specs.amwa.tv/is-11 "AMWA IS-11 NMOS Flow Compatibility Management"
[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA NMOS Receiver Capabilities"
[NMOS Capabilities]: https://github.com/AMWA-TV/nmos-parameter-registers/blob/main/capabilities/README.md "AMWA NMOS Parameter Registers - NMOS Capabilities"
[E-EDID]: https://vesa.org/vesa-standards/ "VESA Enhanced Extended Display Identification Data Standard Release A, Revision 2"
[DMT]: https://vesa.org/vesa-standards/ "VESA and Industry Standards and Guidelines for Computer Display Monitor Timing (DMT) Version 1.0, Rev. 12"
[CTA-861]: https://shop.cta.tech/products/a-dtv-profile-for-uncompressed-high-speed-digital-interfaces-cta-861-g "A DTV Profile for Uncompressed High Speed Digital Interfaces (CTA-861-G)"
