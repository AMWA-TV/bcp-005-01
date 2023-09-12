# AMWA BCP-005-01: EDID to NMOS Receiver Capabilities Mapping
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

## Introduction

The purpose of AMWA BCP-005-01 is to provide Best Current Practice guidelines for mapping EDID fields to Receiver Capabilities in the case Receiver is associated with an Output connected to a downstream counterpart which provides EDID.

The proposed mapping provides the mechanism for converting EDID information into Receiver Capabilities.
The effective Receiver Capabilities MAY differ from those obtained from the proposed mapping when the Receiver is:

- Capable of altering signal (e.g. it is able to consume 4K and downscale it before passing to the Output which downstream counterpart supports up to HD resolutions)
- Constrained beyond the capabilities of the downstream counterpart of the Output (e.g. it supports resolution up to 1080p but the Output is connected to a 4K monitor)

The term 'Receiver' used in this document is defined in [IS-04][].

The terms 'Parameter Constraint' and 'Constraint Set' used in this document are defined in [BCP-004-01][].

The term 'Output' used in this document is defined in [IS-11][].

BCP-005-01 is intended to be used in conjunction with an [IS-11][] and [BCP-004-01][] deployment; however it has been written in such a way to provide useful functionality even in the absence of such a system.

This document is targeted against [E-EDID][] which consist of EDID 1.4 (and covers EDID 1.3) and is referred to as _Base EDID_ and the [CTA-861][] Extension Block.

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Video Receivers

If an [IS-04][] Video Receiver is associated with an Output which has an EDID, the optional mapping of the EDID supported video formats into Receiver's Capabilities SHALL be performed according to the rules below.

### Video Mode Mappings

Video modes, described in [E-EDID][], provide information about the video frame size and frame rate.
There are multiple blocks which keep information about these modes, each has its own mapping requirements.

Each video mode SHOULD be expressed in the Receiver's Capabilities as a separate Constraint Set or a part of a more common Constraint Set with non-empty

- `urn:x-nmos:cap:format:frame_width`
- `urn:x-nmos:cap:format:frame_height`
- `urn:x-nmos:cap:format:grain_rate`

The video mode descriptors MAY include one or more of the following mappings:

- `urn:x-nmos:cap:format:interlace_mode`
- `urn:x-nmos:cap:meta:preference`

#### Established Timings

Three blocks of Established Timings, described in the form of video mode lists, indicate support of industry de-facto video modes.
Established Timings I and II are defined in [E-EDID][] section 3.8 and Established Timings III in [E-EDID][] section 3.10.3.9.
Both sections give information about frame width, height and rate and interlace mode for each of the listed video modes.

[Example](./Examples.md#established-timings)

#### Standard Timings

Standard Timings describe industry de-facto video modes that are not listed in the Established Timings.
Standard Timings format is defined in [E-EDID][] section 3.9.
The mapping is as follows:

- `urn:x-nmos:cap:format:frame_width` MUST be calculated from the _Horizontal Active Pixel Count_
- `urn:x-nmos:cap:format:frame_height` MUST be calculated using the _Frame Width_ and _Image Aspect Ratio_
- `urn:x-nmos:cap:format:grain_rate` MUST be calculated with _Field Refresh Rate_
- `urn:x-nmos:cap:format:interlace_mode` MUST be set to `progressive`

[Example](./Examples.md#standard-timings)

#### Detailed Timing Descriptors (18 Byte Descriptors)

Defined in [E-EDID][] section 3.10, there are 4 possible descriptors which can be provided.
Each Detailed Timing Descriptor in Base EDID and [CTA-861][] Extension Block (section 7.2.1) MUST follow the mapping.

The mapping is defined in section 3.10.2 and is applied as follows:

- `urn:x-nmos:cap:format:interlace_mode` MUST be set according to _Signal Interface Type_ defined in table 3.22
- `urn:x-nmos:cap:format:frame_width` MUST be _Horizontal Addressable Video in pixels_
- `urn:x-nmos:cap:format:frame_height` MUST be calculated using the _Vertical Addressable Video in lines_ which MUST be multiplied by 2 when _Signal Interface Type_ indicates interlaced
- `urn:x-nmos:cap:format:grain_rate` is represented by
  - `numerator` which MUST be calculated using _Pixel Clock_
  - `denominator` which MUST be calculated with _Horizontal Addressable Video in pixels_, _Horizontal Blanking in pixels_, _Vertical Addressable Video in lines_ and _Vertical Blanking in lines_

#### CVT 3 Byte Codes

CVT 3 Byte Code structure is defined in [E-EDID][] section 3.10.3.8.

- `urn:x-nmos:cap:format:frame_height` MUST be set according to _Addressable Lines per Field_
- `urn:x-nmos:cap:format:frame_width` MUST be calculated with _Addressable Lines per Field_ and _Aspect Ratio_
- `urn:x-nmos:cap:format:grain_rate` MUST be set according to _Supported Vertical Rate and Blanking Style_

The _Preferred Vertical Rate_ SHOULD be indicated by using a higher `urn:x-nmos:cap:meta:preference` value in Constraint Set(s) describing this value vs. other _Supported Vertical Rates_.

#### Short Video Descriptors

Short Video Descriptor (SVD) format is defined in [CTA-861][] section 7.5.1.

It operates with Video Identification Codes (VICs).
Each of them is associated with a union of frame width, height and rate and interlace mode.
This mapping is defined in [CTA-861][] section 4.1.

### Vertical Frequency Discrepancy

[E-EDID][] describes video timings which actual vertical frequency may not be equal to the grain rate of the corresponding video mode.

Established Timings, Standard Timings and CVT 3 Byte Codes operate with video mode descriptions with integer vertical frequencies although these video modes correspond to video timings with fractional vertical frequencies (some of these timings are described in [DMT][]).

Describing such video modes in Receiver Capabilities is implementation specific and depend on what network stream the Receiver can handle.
`urn:x-nmos:cap:format:grain_rate` in corresponding Constraint Set(s) MUST describe vertical frequency from either the video mode or the video timings and MAY describe both.

Some VICs ([CTA-861][] section 4.1) are marked as associated with two flavours of the same mode: with a vertical frequency that is an integer multiple of 6 Hz and a vertical frequency adjusted by a factor of 1000/1001.
`urn:x-nmos:cap:format:grain_rate` in corresponding Constraint Set(s) MUST describe at least one of these vertical frequencies and MAY describe both.

### Video Mode Preference

Constraint Sets for Detailed Timing Descriptors and Short Video Descriptors describing _Native Video Formats_ MUST have higher `urn:x-nmos:cap:meta:preference` values than Constraint Sets for video modes not marked as native.

The Constraint Set for the first Detailed Timing Descriptor in Base EDID, called _Preferred Timing Mode_, or the Constraint Set for the first Short Video Descriptor in the first CTA-861 Extension if it takes precedence ([CTA-861][] section 7.5) MUST have the highest `urn:x-nmos:cap:meta:preference` value among the Constraint Sets.

### Color Subsampling

If an [E-EDID][] doesn't have any [CTA-861][] Extensions, color subsampling formats MUST be taken from Base EDID, otherwise from the Extensions.

#### Base EDID

The origin of supported color subsampling formats in Base EDID is the Feature Support in [E-EDID][] section 3.6.4.

It has one of four possible values:

- RGB 4:4:4
- RGB 4:4:4 & YCbCr 4:4:4
- RGB 4:4:4 & YCbCr 4:2:2
- RGB 4:4:4 & YCbCr 4:4:4 & YCbCr 4:2:2

This value MUST be transformed into `urn:x-nmos:cap:format:color_sampling` with `enum` values according to those permitted by [capabilities Parameter Registry](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#color-sampling) and MUST be added to each Constraint Set.

#### CTA-861 Extension

The supported color subsampling formats in the CTA Extension Header ([CTA-861][] section 7.5) indicate `YCbCr-4:2:2` and `YCbCr-4:4:4` support in addition to `RGB`.

YCbCr 4:2:0 Capability Map Data Block ([CTA-861][] section 7.5.11) shows which video modes support `YCbCr-4:2:0` in addition to subsampling formats listed in the CTA Extension Header.
Constraint Sets associated with these video modes MUST contain `YCbCr-4:2:0` within the possible values for `urn:x-nmos:cap:format:color_sampling`.

YCbCr 4:2:0 Video Data Block ([CTA-861][] section 7.5.10) marks video modes as supporting only `YCbCr-4:2:0`.
Constraint Sets associated with these video modes MUST have `urn:x-nmos:cap:format:color_sampling` limited to `YCbCr-4:2:0`.

### Color Component Depth

_Color Bit Depth_ of _Video Input Definition_ described in [E-EDID][] section 3.6.1 MUST be transformed into `urn:x-nmos:cap:format:component_depth` and MUST be added to each Constraint Set.

Vendor-Specific Data Block ([CTA-861][] section 7.5.4) SHOULD be transformed into `urn:x-nmos:cap:format:component_depth` if contains related information.

### Colorspace

Colorimetry Data Block ([CTA-861][] section 7.5.5) SHOULD be transformed into `urn:x-nmos:cap:format:colorspace` with related information if present.

## Audio Receivers

If the Basic Audio support bit is active in the CTA Extension Header, audio receiver MUST have Receiver Capabilities.

When no descriptors are provided, the capabilities MUST contain:

- `urn:x-nmos:cap:format:media_type` MUST be equal to `audio/L8`
- `urn:x-nmos:cap:format:channel_count` MUST be equal to 2

### Short Audio Descriptors

If there are Short Audio Descriptors (see [CTA-861][] section 7.5.2), then each of them MUST be transformed into a Constraint Set.

- `urn:x-nmos:cap:format:media_type` MUST be determined with _Audio Format Code_
  - Linear PCM bit depth MUST be taken from the third Short Audio Descriptors byte
- `urn:x-nmos:cap:format:channel_count` MUST be determined by _Number of Channels_
- `urn:x-nmos:cap:format:sample_rate` MUST be filled with _Sampling Frequencies_

Each of these Parameter Constraints MUST use `enum` Constraint Keyword.

[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs to Indicate Requirement Levels"
[IS-04]: https://specs.amwa.tv/is-04 "AMWA IS-04 NMOS Discovery and Registration Specification (Stable)"
[IS-11]: https://specs.amwa.tv/is-11 "AMWA IS-11 NMOS Flow Compatibility Management"
[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA NMOS Receiver Capabilities"
[E-EDID]: https://vesa.org/vesa-standards/ "VESA Enhanced Extended Display Identification Data Standard Release A, Revision 2"
[DMT]: https://vesa.org/vesa-standards/ "VESA and Industry Standards and Guidelines for Computer Display Monitor Timing (DMT) Version 1.0, Rev. 12"
[CTA-861]: https://shop.cta.tech/products/a-dtv-profile-for-uncompressed-high-speed-digital-interfaces-cta-861-g "A DTV Profile for Uncompressed High Speed Digital Interfaces (CTA-861-G)"
