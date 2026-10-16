# Seedance 1.0 Pro API (`seedance-1.0-pro`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0184 per second** on the APIMart route (billed by resolution tier) — un seul endpoint compatible OpenAI sur `https://api.apimart.ai/v1`, sans abonnement mensuel. *(relevé le 2026-10-12)*

**[Obtenir une clé API](https://go.apimart.ai/k-50394e)** · **[Tarifs en direct](https://go.apimart.ai/k-5fb42b)** · **[Page du modèle](https://go.apimart.ai/k-3f6893)** · [⚡ Démarrage en 60 secondes](#quickstart)

**Pourquoi les équipes passent par APIMart pour Seedance 1.0 Pro (`seedance-1.0-pro`)**

- **Une clé, tout le catalogue.** La même base URL `https://api.apimart.ai/v1` et le même en-tête `Authorization` donnent accès à Seedance 1.0 Pro (`seedance-1.0-pro`) et à plus de 300 autres modèles d'image, de vidéo et de langage — changez le champ `model`, pas votre client.
- **$1 minimum, paiement à l'usage.** Ni abonnement ni forfait prépayé à dimensionner à l'avance : rechargez à partir de $1 et consommez-le en appels.
- **Le montant revient dans la réponse.** Chaque tâche terminée indique le montant facturé (`cost` / `credits_cost`) ; une tâche marquée `failed` est remboursée.
- **Asynchrone par conception.** Soumettez, récupérez le `task_id`, interrogez `GET /v1/tasks/{id}` — le traitement par lots et les retries relèvent d'une file d'attente ordinaire, pas d'une intégration sur mesure.

<!-- /conv-kit:v1 -->



Seedance 1.0 Pro is ByteDance's fast AI video model for high-quality video generation and rapid previews.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `seedance-1.0-pro` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-3f6893) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| per generated second | — | see the live pricing page |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-5fb42b) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

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

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-3f6893) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"seedance-1.0-pro","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "seedance-1.0-pro", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
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

**Quel est l'id du modèle Seedance 1.0 Pro ?**

`seedance-1.0-pro`. Voir le tableau des routes ci-dessus.

**Comment la facturation fonctionne-t-elle ?**

**from $0.0184 per second** on the APIMart route (billed by resolution tier). La tâche terminée indique le montant exact dans son champ `cost` ; une tâche échouée est remboursée.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-50394e) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [seedance-1-0-pro-prompts-fr](https://github.com/q4bmf/seedance-1-0-pro-prompts-fr)
- **Same model, other languages** — 10 more language builds: English `seedance-1-0-pro-api` · 中文 [seedance-1-0-pro-api-cn](https://github.com/rojas52cg58/seedance-1-0-pro-api-cn) · Deutsch [seedance-1-0-pro-api-de](https://github.com/rm7p0q7p2/seedance-1-0-pro-api-de) · Español [seedance-1-0-pro-api-es](https://github.com/gx-j9/seedance-1-0-pro-api-es) · Relay/gateway [seedance-1-0-pro-api-gateway](https://github.com/pdphag/seedance-1-0-pro-api-gateway) · Bahasa [seedance-1-0-pro-api-id](https://github.com/on3sbwi-p/seedance-1-0-pro-api-id) · 日本語 [seedance-1-0-pro-api-ja](https://github.com/v1atcoe-e3/seedance-1-0-pro-api-ja) · 한국어 [seedance-1-0-pro-api-ko](https://github.com/mev2gjgjc1/seedance-1-0-pro-api-ko) · Português [seedance-1-0-pro-api-pt](https://github.com/zcolce4/seedance-1-0-pro-api-pt) · Русский [seedance-1-0-pro-api-ru](https://github.com/jd6ymi6bby8gzp/seedance-1-0-pro-api-ru)
- **More models in this language** — [midjourney-api-fr](https://github.com/apimartt1q/midjourney-api-fr) · [gemini-2-5-flash-image-preview-api-fr](https://github.com/haftslut7ha/gemini-2-5-flash-image-preview-api-fr) · [nano-banana-api-fr](https://github.com/f84q06w110/nano-banana-api-fr) · [gemini-3-1-flash-image-preview-api-fr](https://github.com/ok71ckyc/gemini-3-1-flash-image-preview-api-fr) · [nano-banana-2-api-fr](https://github.com/ms0p12t/nano-banana-2-api-fr) · [gemini-3-pro-image-preview-api-fr](https://github.com/w-44vba/gemini-3-pro-image-preview-api-fr)
- **Topic hubs** — every build of this model, all languages: [seedance-1-0-pro](https://github.com/topics/seedance-1-0-pro) · [seedance-1-0-pro-api](https://github.com/topics/seedance-1-0-pro-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-3f6893> | `apimart.ai/fr/model/doubao-seedance-1-0-pro` |
| Live pricing | <https://go.apimart.ai/k-5fb42b> | `apimart.ai/fr/pricing` |
| Get an API key | <https://go.apimart.ai/k-50394e> | `apimart.ai/fr/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Seedance 1.0 Pro is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
