# temporary-branch

Italian TV channel list for the [Zappr](https://zappr.stream/) web IPTV player.

## File

`italy_zappr.json` — Zappr-compatible list of 388 Italian channels, validated
against the official schema (`https://channels.zappr.stream/schema.json`).

## Zappr list format

```json
{
  "name": "Italy Free-TV",
  "publisher": "helium00",
  "channels": [
    { "lcn": 1, "name": "Rai 1", "logo": "https://.../CAx7yRm",
      "type": "hls", "url": "https://...", "geoblock": true }
  ]
}
```

- Required (list): `name`, `publisher`, `channels`
- Required (channel): `lcn`, `name`, `logo`
- `type`: `hls` | `dash` | `twitch` | `youtube` | `iframe` | `audio` | `direct` | `popup`
- Logo: strip the extension for PNG/WEBP; keep `.svg`; JPG/GIF keep their extension.
- The channel object uses `"additionalProperties": false` — any unexpected key
  fails the import.

## Source & generation

Channel data is derived from the
[Free-TV/IPTV Italy playlist](https://github.com/Free-TV/IPTV/blob/master/lists/italy.md)
(`playlists/playlist_italy.m3u8`) and converted to the Zappr schema:

- `tvg-chno` → `lcn` (unique numbers assigned where missing)
- `tvg-logo` → `logo` (PNG/WEBP extension stripped)
- stream URL → `url` (+ `type` detected from the URL)
- `Ⓖ` marker → `geoblock: true`; non-HTTPS streams → `http: true`

Per-channel EPG is omitted: Zappr uses a proprietary EPG source
(`source|id` fetched from `datahub.enhanced.tools`) that does not map from
standard XMLTV `tvg-id`s.

## Importing into Zappr

Load the raw file URL in Zappr, or upload it directly:

```
https://raw.githubusercontent.com/helium00/temporary-branch/main/italy_zappr.json
```

## Known limitations

Zappr plays streams **directly in the browser** — it has no server-side proxy
(the only service worker is a PWA offline fallback). Playback therefore depends
on each stream server, not on this file:

- **CORS**: a channel only plays if its server sends
  `Access-Control-Allow-Origin`. Local broadcasters (`streamlock.net`),
  CloudFront-based channels (La7, Nove, K2, Frisbee…) and most Mediaset
  channels work; **RAI** (relinker returns `403` to browsers) and geoblocked
  foreign CDNs do **not**.
- **Geoblocking**: several streams are restricted to Italy.
- **Dead links**: some entries in the upstream source are stale (`404`).

**Logos** must keep their file extension (`.png`, `.jpg`, …). Zappr uses full
`http(s)` logo URLs verbatim, so a URL without extension returns HTML instead of
an image. (The published schema's "no extension" rule only applies to logos
hosted on Zappr's own logo CDN.)

> This is a temporary repository.
