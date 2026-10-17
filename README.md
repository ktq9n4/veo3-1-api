# Veo 3.1 API (`veo3.1-fast-ext`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.064 per second** on the APIMart route — one OpenAI-compatible endpoint at `https://api.apimart.ai/v1`, no monthly plan required. *(observed 2026-10-12)*

**[Get an API key](https://go.apimart.ai/k-0988cf)** · **[Live pricing](https://go.apimart.ai/k-6fd3d8)** · **[Model page](https://go.apimart.ai/k-290c53)** · [⚡ 60-second quickstart](#quickstart)

**Why teams call Veo 3.1 (`veo3.1-fast-ext`) through APIMart**

- **One key, entire catalog.** The same `https://api.apimart.ai/v1` base URL and `Authorization` header reach Veo 3.1 (`veo3.1-fast-ext`) and 300+ other image, video and language models — switch the `model` field, not your client.
- **$1 minimum, pay as you go.** No subscription and no prepaid plan to size up front: top up from $1 and spend it on calls. There is no free quota to burn through first, so the price in this table is the price you pay.
- **The charge comes back in the response.** Every call reports the amount billed (`cost` / `credits_cost`), so a spend number is read per call instead of guessed at month end.
- **Async by design.** Submit, take the `task_id`, poll `GET /v1/tasks/{id}` — batching and retries are ordinary queue work, not a bespoke integration.

<!-- /conv-kit:v1 -->



Veo 3.1 API on APIMart provides access to both veo3.1-fast and veo3.1-quality models, the newest generation of the Google Veo 3 API. Veo 3.1 is Google DeepMind's upgrade to Veo 3, offering enhanced audiovisual quality, native synchronized audio, and advanced cinematic controls for professional video creation.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `veo3.1-fast-ext` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-290c53) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.175 | 0.14 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-6fd3d8) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-290c53) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"veo3.1-fast-ext","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "veo3.1-fast-ext", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**What is the Veo 3.1 model id?**

`veo3.1-fast-ext`. See the route table above.

**How is it billed?**

**from $0.064 per second** on the APIMart route. The completed task reports the exact amount in its `cost` field; a failed task is refunded.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-0988cf) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [veo3-1-prompts](https://github.com/ktq9n4/veo3-1-prompts)
- **Same model, other languages** — 10 more language builds: 中文 [veo3-1-api-cn](https://github.com/wfbjnnovvnj/veo3-1-api-cn) · Deutsch [veo3-1-api-de](https://github.com/mx02yhsjr3t3/veo3-1-api-de) · Español [veo3-1-api-es](https://github.com/bi4gp921v9d/veo3-1-api-es) · Français [veo3-1-api-fr](https://github.com/kd4mi7/veo3-1-api-fr) · Relay/gateway `veo3-1-api-gateway` · Bahasa `veo3-1-api-id` · 日本語 [veo3-1-api-ja](https://github.com/gcjb8wo6icy8/veo3-1-api-ja) · 한국어 `veo3-1-api-ko` · Português [veo3-1-api-pt](https://github.com/ea4fa87m8/veo3-1-api-pt) · Русский [veo3-1-api-ru](https://github.com/m67k3hcecku9ef/veo3-1-api-ru)
- **More models in this language** — [grok-imagine-1-5-api](https://github.com/igu3s5e5x90wx/grok-imagine-1-5-api) · [grok-image-api](https://github.com/hpcsd0apt/grok-image-api) · [grok-imagine-image-quality-api](https://github.com/xt5chw51l/grok-imagine-image-quality-api) · [imagen-4-api](https://github.com/lrk0lbrti/imagen-4-api) · [qwen-image-2-0-pro-api](https://github.com/usen4/qwen-image-2-0-pro-api) · [seedream-4-0-api](https://github.com/qela34r8sb7/seedream-4-0-api)
- **Topic hubs** — every build of this model, all languages: [veo3-1](https://github.com/topics/veo3-1) · [veo3-1-api](https://github.com/topics/veo3-1-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-290c53> | `apimart.ai/model/veo-3-1` |
| Live pricing | <https://go.apimart.ai/k-6fd3d8> | `apimart.ai/pricing` |
| Get an API key | <https://go.apimart.ai/k-0988cf> | `apimart.ai/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Veo 3.1 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
