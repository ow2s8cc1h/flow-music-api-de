# Flow Music API (`flowmusic`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/audio-waveform.svg" width="820" alt="Music generation request path through the APIMart audio endpoint">
</p>

> **from $0.01 per generation** on the APIMart route — ein einziger OpenAI-kompatibler Endpoint unter `https://api.apimart.ai/v1`, kein Monatsabo nötig. *(erfasst am 2026-10-12)*

**[API-Key erhalten](https://go.apimart.ai/k-3d3fa5)** · **[Live-Preise](https://go.apimart.ai/k-267484)** · **[Modellseite](https://go.apimart.ai/k-b0fe67)** · [⚡ Quickstart in 60 Sekunden](#quickstart)

**Warum Teams Flow Music (`flowmusic`) über APIMart nutzen**

- **Ein Key, der gesamte Katalog.** Dieselbe Base-URL `https://api.apimart.ai/v1` und derselbe `Authorization`-Header erreichen Flow Music (`flowmusic`) und über 300 weitere Bild-, Video- und Sprachmodelle — Sie wechseln das `model`-Feld, nicht Ihren Client.
- **$1 Minimum, Pay-as-you-go.** Kein Abo und kein Prepaid-Tarif, den Sie vorab dimensionieren müssen: ab $1 aufladen und in Calls verbrauchen.
- **Die Abrechnung kommt in der Antwort zurück.** Jede abgeschlossene Aufgabe meldet den berechneten Betrag (`cost` / `credits_cost`); eine als `failed` markierte Aufgabe wird erstattet.
- **Asynchron by Design.** Absenden, `task_id` entgegennehmen, `GET /v1/tasks/{id}` pollen — Batching und Retries sind gewöhnliche Queue-Arbeit, keine Sonderintegration.

<!-- /conv-kit:v1 -->



Flow Music API on APIMart provides a production-ready AI music generation workflow for creating full songs from lyrics and style prompts, with vocals or instrumental output and asynchronous task polling.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Music generation (async task) | `flowmusic` | per generated track, by operation | see the [model page](https://go.apimart.ai/k-b0fe67) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/music/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.075 | 0.06 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-267484) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| model | string | — | the music model id |
| title | string | — | track title |
| sound_prompt | string | — | style / instrumentation description (one of sound_prompt or lyrics is required) |
| lyrics | string | — | lyrics text with section tags; leave empty for instrumental |
| bpm | string | number | — | tempo of the generated track |
| length | integer | — | target duration in seconds |
| audio_url | string | — | source audio for extend / cover / stem operations |
| callback_url | string | — | webhook called when the task completes |

The table lists the fields accepted by the APIMart `music/generations` endpoint. Check the [model page](https://go.apimart.ai/k-b0fe67) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/music/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"flowmusic","title":"My Song","sound_prompt":"upbeat pop music with piano","bpm":"120","length":60}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/music/generations", headers=HEADERS, timeout=60, json={
    "model": "flowmusic", "title": "My Song", "sound_prompt": "upbeat pop music with piano", "bpm": "120", "length": 60,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["audio"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Wie lautet die Modell-id von Flow Music?**

`flowmusic`. Siehe Routentabelle oben.

**Wie wird abgerechnet?**

**from $0.01 per generation** on the APIMart route. Die abgeschlossene Aufgabe meldet den genauen Betrag im Feld `cost`; eine fehlgeschlagene Aufgabe wird erstattet.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-3d3fa5) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [flow-music-prompts-de](https://github.com/ow2s8cc1h/flow-music-prompts-de)
- **Same model, other languages** — 10 more language builds: English `flow-music-api` · 中文 `flow-music-api-cn` · Español `flow-music-api-es` · Français `flow-music-api-fr` · Relay/gateway `flow-music-api-gateway` · Bahasa `flow-music-api-id` · 日本語 `flow-music-api-ja` · 한국어 `flow-music-api-ko` · Português `flow-music-api-pt` · Русский `flow-music-api-ru`
- **More models in this language** — [midjourney-api-de](https://github.com/h5fd0cofy/midjourney-api-de) · [gemini-2-5-flash-image-preview-api-de](https://github.com/kn6f69s2b8j9/gemini-2-5-flash-image-preview-api-de) · [nano-banana-api-de](https://github.com/uh6cob8op/nano-banana-api-de) · [gemini-3-1-flash-image-preview-api-de](https://github.com/k8wq8pd46e5ku/gemini-3-1-flash-image-preview-api-de) · [nano-banana-2-api-de](https://github.com/mocpl3/nano-banana-2-api-de) · [gemini-3-pro-image-preview-api-de](https://github.com/cmpxx2w/gemini-3-pro-image-preview-api-de)
- **Topic hubs** — every build of this model, all languages: [flow-music](https://github.com/topics/flow-music) · [flow-music-api](https://github.com/topics/flow-music-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-b0fe67> | `apimart.ai/de/model/flow-music` |
| Live pricing | <https://go.apimart.ai/k-267484> | `apimart.ai/de/pricing` |
| Get an API key | <https://go.apimart.ai/k-3d3fa5> | `apimart.ai/de/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Flow Music is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
