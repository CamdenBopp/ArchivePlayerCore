# ArchivePlayerCore

Plays video containers that AVFoundation won't open, using Apple's own decoders.

AVPlayer handles MP4, M4V and MOV. A lot of what people upload to Internet Archive is MKV, DVD VOB or MPEG transport stream, and AVPlayer refuses those files even when the codecs inside are ones the hardware decodes every day. This package reads those containers in Swift, turns their packets into `CMSampleBuffer`s, and hands them to `AVSampleBufferVideoRenderer` and `AVSampleBufferAudioRenderer`. Apple does the decoding. There is no FFmpeg and no third-party code.

It streams over HTTP Range requests, so a 3 GB original can start playing after about a megabyte.

## What's in it

| Piece | State |
| --- | --- |
| `MatroskaDemuxer` (MKV, WebM container) | Used by [Funston](https://github.com/CamdenBopp/Funston) for MKV originals. |
| `ProgramStreamDemuxer` + DVD IFO metadata (VOB) | Demuxes real DVD rips on macOS. The iOS Simulator rejects MPEG-2 frames, and it hasn't been tried on a phone. |
| `TransportStreamDemuxer` + access-unit adapter (TS) | H.264/AAC renders on macOS and the Simulator. It isn't used by any app yet. |
| `SampleBufferPlaybackEngine` | Renderer, synchronizer, seeking, track switching. |
| `CaptionPipeline`, `CEA608Decoder` | Text subtitle tracks and line-21 captions. |
| `HTTPRangeByteSource` | Bounded, cancellable Range reads. |

`ExtraCodecs/` is a separate package that audits a future LGPL-only FFmpeg build configuration. It contains no FFmpeg and nothing depends on it. See [OptionalCodecs.md](Docs/OptionalCodecs.md).

## Use

```swift
.package(url: "https://github.com/CamdenBopp/ArchivePlayerCore.git", branch: "main")
```

Requires Swift 6, iOS 18 or macOS 15.

## Test

```sh
swift test
```

Tests that hit the network or need a local media corpus are opt-in through environment variables (`IA_NETWORK_TESTS=1`, `IA_TS_RENDER_TESTS=1` and others). Each is documented in the findings file for its container under [Docs](Docs). Those files record what was measured, on which machine, and what hasn't been verified yet. [DemuxMissionStatus.md](Docs/DemuxMissionStatus.md) is the summary.

## License

MIT. See [LICENSE](LICENSE).
