# temporary-branch

Italian TV channel lists for the [Zappr](https://zappr.stream/) web IPTV player.

## Files

| File | Format | Notes |
|---|---|---|
| `italy_zappr.json` | **Zappr-compatible** JSON (388 channels) | Import-ready |
| `italy_channels.json` | Legacy custom JSON | **Not** compatible with Zappr |

## Why the legacy file is not compatible

Zappr validates imported lists against an official JSON Schema
(`https://channels.zappr.stream/schema.json`, draft-07). The channel object uses
`"additionalProperties": false`, so any unexpected key fails the import.

The legacy `italy_channels.json` failed on every field:

| Legacy field | Problem | Zappr field |
|---|---|---|
| top-level | missing required `name` / `publisher` | — |
| `number` | invalid key | `lcn` |
| `stream` | invalid key | `url` |
| `section` | invalid key (rejected) | *(no per-channel group)* |
| `epg: "Rai1.it"` | wrong type: schema expects an object | `epg: {source, id}` |
| `logo: ".../x.png"` | PNG/WEBP must have **no** file extension | `logo: ".../x"` |

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
