# temporary-branch

TV channel lists for the [Zappr](https://zappr.stream/) web IPTV player.

## Files

Both are Zappr-compatible and validated against the official schema
(`https://channels.zappr.stream/schema.json`):

| File | Channels |
|---|---|
| `italy_zappr.json` | 388 Italian channels |
| `spain_zappr.json` | 52 Spanish channels |

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

Channel data is derived from the [Free-TV/IPTV](https://github.com/Free-TV/IPTV)
playlists (`playlists/playlist_italy.m3u8`, `playlists/playlist_spain.m3u8`) and
converted to the Zappr schema:

- `tvg-chno` → `lcn` (unique numbers assigned where missing)
- `tvg-logo` → `logo` (rewritten so it never ends in `.png`/`.webp`; see below)
- stream URL → `url` (+ `type` detected from the URL)
- `Ⓖ` marker → `geoblock: true`; non-HTTPS streams → `http: true`

Per-channel EPG is omitted: Zappr uses a proprietary EPG source
(`source|id` fetched from `datahub.enhanced.tools`) that does not map from
standard XMLTV `tvg-id`s.

## Importing into Zappr

Load the raw file URL in Zappr, or upload it directly:

```
https://raw.githubusercontent.com/helium00/temporary-branch/main/italy_zappr.json
https://raw.githubusercontent.com/helium00/temporary-branch/main/spain_zappr.json
```

## Known limitations

Zappr plays streams **directly in the browser** — it has no server-side proxy
(the only service worker is a PWA offline fallback). Playback therefore depends
on each stream server, not on this file:

- **CORS**: a channel only plays if its server sends
  `Access-Control-Allow-Origin`. Local broadcasters (`streamlock.net`),
  CloudFront-based channels (La7, Nove, K2, Frisbee…) and most Mediaset
  channels work; **RAI** (relinker returns `403` to browsers, no CORS) and
  geoblocked foreign CDNs do **not**.
- **Geoblocking**: many streams are restricted to their country of origin
  (Italy / Spain), so they only play from inside that country.
- **Dead links**: some entries in the upstream source are stale (`404`).

**Logos** are subject to a conflict: the schema rejects URLs ending in
`.png`/`.webp`, but Zappr uses full `http(s)` logo URLs verbatim, so a URL
without any extension returns HTML instead of an image. To satisfy both,
`.png`/`.webp` logos are rewritten to a URL that still serves an image without
that suffix — imgur logos use the `.jpg` variant (imgur transcodes on the fly),
and other hosts are routed through the `wsrv.nl` image proxy with `&output=jpg`.

> This is a temporary repository.
