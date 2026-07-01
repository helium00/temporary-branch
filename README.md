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

> This is a temporary repository.
