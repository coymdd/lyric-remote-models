---
license: apache-2.0
tags:
- translation
- index
---

# Index-Translate-9B

<p><a style="display:inline" href="https://index-translate.bilibili.com/">🌐 Online demo</a> · <a style="display:inline" href="https://github.com/bilibili/Index-Translate"><img src="https://raw.githubusercontent.com/bilibili/Index-Translate/main/docs/assets/github.svg" width="16" height="16" alt="" style="display:inline;vertical-align:middle;margin:0"> GitHub</a> · <a style="display:inline" href="https://arxiv.org/abs/2609.40181">📚 Technical report</a> · <a style="display:inline" href="https://huggingface.co/collections/IndexTeam/index-translate">🤗 Hugging Face collection</a> · <a style="display:inline" href="https://www.modelscope.cn/collections/IndexTeam/Index-Translate"><img src="https://raw.githubusercontent.com/bilibili/Index-Translate/main/docs/assets/modelscope.svg" width="16" height="16" alt="" style="display:inline;vertical-align:middle;margin:0"> ModelScope collection</a></p>

**Index-Translate-9B** is the 9B-parameter multilingual text model of the **Index-Translate** family, built on Qwen3.5. It supports text translation across **150 languages** and follows translation instructions involving terminology, formatting, style, structure, context, and output length.

## Capabilities

- **Multimodal image translation (多模态图片翻译):** natively supports end-to-end image-to-text translation across 150 languages without external OCR tools, powered by companion visual encoders (`mmproj`).
- **General multilingual translation:** translate sentences, articles, subtitles, and other text across the supported language inventory.
- **Instruction following & constrained translation (硬约束 / 软约束):**
  - **Hard constraints (硬约束):** strict glossary enforcement (术语强制对齐), structured data preservation (JSON, CSV, markdown formatting), code blocks, and variable placeholders (e.g. `{variable}`, `[123456]`).
  - **Soft constraints (软约束):** stylistic tone adaptation (formal, casual, social media / meme style), domain & context disambiguation (e.g. distinguishing industrial vs. botanical senses of terms).
- **Social and cultural translation:** interpret community aliases, playful spelling, memes, and nonliteral expressions with attention to their intended meaning.
- Scores **0.8789 FLORES COMET-22**, **75.35 WMT26 Judge**, and **0.8209 instTrans IFscore** on the report's main evaluation.
- Achieves the highest low-resource instTrans IFscore (**0.7725**) and lowest off-target rate (**3.47%**) among the report's comparison systems; low-resource quality is **0.5222**.
- Leads the report's compared models on **MuST-Cinema COMET-22 (0.8979)** and scores **0.7387 MEME**.

Language coverage is documented in the technical report's language inventory. The speech and long-document models have their own packaged interfaces and language coverage.

## Training

The technical report describes a shared multilingual mid-training recipe, followed by task-specific post-training:

1. **Multilingual mid-training:** general text replay, monolingual text, ordinary parallel translations, and pivot-organized multilingual groups. The constant stage uses general / parallel / monolingual data at **1:1:1**; decay uses general / core-pivot / full-pivot data at **1:4:2**. The shared recipe totals **167.77B tokens**.
2. **Specialist SFT and RL:** general translation, instruction following, and meme translation specialists receive task-specific supervision. General translation RL combines XCOMET-XXL with language-validity and adequacy judgments; instruction RL uses Rubric-as-Reward with hard checks and graded constraints. RIVAL provides adaptive judge supervision.
3. **Expert integration and targeted distillation:** parameter interpolation combines complementary specialists, then multi-teacher on-policy distillation (**MOPD**) addresses task types that remain weak after merging.

The evaluated 2B and 9B models combine the general, instruction, and meme specialists with weights **0.8 / 0.1 / 0.1**, then receive targeted MOPD. Reported training-stage ablations use separate configurations; they are not the final model scores below.

## Evaluation

Results below reproduce the latest technical report. Comparisons apply to the report's evaluation data and settings, and metric scales differ. Higher is better except for off-target rates. These are individual benchmark scores, separate from the demo's normalized radar categories.

The tables compare this checkpoint with the other Index-Translate sizes, **Hy-MT2-7B**, **TranslateGemma-12B**, and the **Qwen3.5-9B** base at a similar scale, and larger translation models and API baselines.

### Main translation benchmarks

WMT26 Judge uses a **0–100** scale; the other columns use **0–1**. instTrans quality and IFscore measure translation quality and instruction adherence separately. IFMTBench reports reference-based XCOMET-XXL and instruction adherence. Vertical is the equal-weight mean of the five domain scores below. North-Small-Translate is the **218B-A25B** model; TranslateGemma is **translategemma-12b-it**.

| Model | FLORES COMET-22 | WMT24++ COMET-22 | WMT26 Judge | instTrans Quality | instTrans IFscore | IFMTBench XCOMET-XXL | IFMTBench IFscore | Vertical mean | MEME |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Index-Translate-35B-A3B (preview) | 0.8794 | 0.8586 | 76.76 | 0.6901 | 0.8336 | 0.7926 | 0.8991 | 0.8438 | 0.7405 |
| **Index-Translate-9B** | 0.8789 | 0.8601 | 75.35 | 0.6771 | 0.8209 | 0.7957 | 0.8760 | 0.8451 | 0.7387 |
| Index-Translate-2B | 0.8655 | 0.8489 | 60.26 | 0.5391 | 0.7569 | 0.7712 | 0.7584 | 0.8377 | 0.6443 |
| Hy-MT2-1.8B | 0.8522 | 0.8401 | 49.35 | 0.3181 | 0.4932 | 0.7493 | 0.7161 | 0.8314 | 0.3643 |
| Hy-MT2-7B | 0.8747 | 0.8593 | 60.51 | 0.5143 | 0.6079 | 0.8049 | 0.8741 | 0.8335 | 0.5139 |
| Hy-MT2-30B-A3B | 0.8787 | 0.8624 | 66.81 | 0.5725 | 0.6415 | 0.8177 | 0.9029 | 0.8459 | 0.5812 |
| TranslateGemma-12B | 0.8732 | 0.8524 | 71.19 | 0.4515 | 0.3068 | 0.8023 | 0.2892 | 0.8347 | 0.4281 |
| North-Small-Translate (218B-A25B) | 0.8784 | 0.8578 | 68.37 | 0.5697 | 0.5294 | 0.7657 | 0.8635 | 0.8357 | 0.6836 |
| Qwen3.5-2B (base) | 0.6983 | 0.6933 | 32.11 | 0.0999 | 0.2431 | 0.6197 | 0.3836 | 0.7557 | 0.2062 |
| Qwen3.5-9B (base) | 0.8316 | 0.8073 | 60.31 | 0.2467 | 0.0609 | 0.7341 | 0.5980 | 0.8199 | 0.5728 |
| Qwen3.5-35B-A3B | 0.8570 | 0.8290 | 71.33 | 0.3690 | 0.5204 | 0.7589 | 0.7822 | 0.8267 | 0.6447 |
| DeepSeek-V4.1-Flash | 0.8762 | 0.8510 | 83.55 | 0.6068 | 0.6374 | 0.7817 | 0.9090 | 0.8432 | 0.7424 |
| GPT-5.6-Sol | 0.8650 | 0.8469 | 89.10 | 0.6902 | 0.7624 | 0.7946 | 0.9367 | 0.8311 | 0.7194 |
| Gemini 3.5 Flash Lite | 0.8750 | 0.8497 | 79.52 | 0.6068 | 0.6374 | 0.7764 | 0.8854 | 0.8131 | 0.7034 |

### Low-resource translation and instruction following

**FLORES_minor_pair** contains **104,000 inputs** across **1,040 directions among 62 languages**. **instTrans_minor** contains **2,793 instruction-translation tasks** from Chinese or English into low-resource languages. Off-target is the percentage of outputs in a language other than the requested target; lower is better. The other scores use a 0–1 scale.

| Model | FLORES_minor_pair COMET-22 ↑ | FLORES_minor_pair XCOMET-XXL ↑ | FLORES_minor_pair off-target ↓ | instTrans_minor Quality ↑ | instTrans_minor IFscore ↑ | instTrans_minor off-target ↓ |
|---|---:|---:|---:|---:|---:|---:|
| Index-Translate-35B-A3B (preview) | 0.8168 | 0.7164 | 2.4% | 0.5151 | 0.7715 | 4.05% |
| **Index-Translate-9B** | 0.7992 | 0.6805 | 4.0% | 0.5222 | 0.7725 | 3.47% |
| Index-Translate-2B | 0.7377 | 0.4817 | 4.2% | 0.3050 | 0.6586 | 3.97% |
| Hy-MT2-1.8B | 0.3163 | 0.2014 | 54.2% | 0.0412 | 0.1313 | 65.27% |
| Hy-MT2-7B | 0.4626 | 0.3334 | 35.7% | 0.1121 | 0.2405 | 45.40% |
| Hy-MT2-30B-A3B | 0.6746 | 0.5359 | 14.5% | 0.2246 | 0.4449 | 15.47% |
| TranslateGemma-12B | 0.8021 | 0.6217 | 1.7% | 0.2412 | 0.2682 | 5.37% |
| North-Small-Translate (218B-A25B) | 0.6855 | 0.4919 | 13.2% | 0.2436 | 0.2902 | 26.89% |
| Qwen3.5-2B (base) | 0.3716 | 0.1852 | 39.1% | 0.0069 | 0.0549 | 73.54% |
| Qwen3.5-9B (base) | 0.6774 | 0.4502 | 11.2% | 0.1318 | 0.3532 | 18.51% |
| Qwen3.5-35B-A3B | 0.7854 | 0.6257 | 3.5% | 0.2874 | 0.4458 | 11.60% |
| DeepSeek-V4.1-Flash | 0.8333 | 0.7297 | 1.3% | 0.4793 | 0.5854 | 5.73% |
| GPT-5.6-Sol | 0.7669 | 0.6918 | 10.4% | 0.5757 | 0.6866 | 7.73% |
| Gemini 3.5 Flash Lite | 0.8122 | 0.6995 | 3.3% | 0.3927 | 0.5584 | 12.18% |

### Domain translation

COMET-22 is used for OPUS-Books, WMT Biomedical, MTNT, and MuST-Cinema; GuoFeng-Webnovel uses document-COMET. Higher is better. The complete comparison is in the report.

| Model | OPUS-Books | Biomedical | MTNT | MuST-Cinema | GuoFeng |
|---|---:|---:|---:|---:|---:|
| Index-Translate-35B-A3B (preview) | 0.8013 | 0.8681 | 0.8244 | 0.8962 | 0.8291 |
| **Index-Translate-9B** | 0.8010 | 0.8674 | 0.8246 | 0.8979 | 0.8345 |
| Index-Translate-2B | 0.7845 | 0.8635 | 0.8132 | 0.8930 | 0.8345 |

### General capabilities

These answer-accuracy results (%) use matched evaluation subsets. Translation training can change general capabilities; these scores describe the translation checkpoints rather than the original Qwen3.5 models.

| Model | C-Eval | GPQA-Diamond | INCLUDE | MMMLU |
|---|---:|---:|---:|---:|
| Index-Translate-35B-A3B (preview) | 77.6 | 49.1 | 68.8 | 71.9 |
| **Index-Translate-9B** | 69.6 | 36.3 | 63.1 | 66.6 |
| Index-Translate-2B | 42.6 | 27.4 | 39.1 | 42.7 |

### Evaluation protocol

- The main FLORES evaluation uses **126,000 inputs**, 420 directions among 21 languages; WMT24++ uses 6,000 inputs from English into 20 languages.
- instTrans has **3,000 Chinese-to-20-language tasks** across ten constraint types. GPT-5.6-Sol judges quality and instruction adherence separately. MEME contains **3,638 Chinese-to-English cases**, covering 703 terms and 857 disambiguated senses.
- Text translation evaluation uses **greedy decoding and a 16,384-token context window**, except WMT26, which uses zero-shot Chinese instructions, a **3,584-token context**, and a **1,536-token output limit**. WMT26 reference-free GPT-5.6-Sol scores are averaged over 20 target languages.
- IFMTBench starts with 7,344 examples. Preprocessing removes 280 single-constraint examples with reference or language-label issues, leaving **7,064 inputs**; **7,063 receive valid judgments**. See the [preprocessing note](https://github.com/bilibili/Index-Translate/blob/main/docs/evaluation.md#ifmtbench-preprocessing).

The **1,024-token output budget in the inference client below is a usage default**, not the output budget used to produce every reported evaluation result. Detailed protocols and valid-judgment counts are in the [technical report](https://arxiv.org/abs/2609.40181). The report describes instTrans and MEME; benchmark release plans are tracked in the [GitHub TODO](https://github.com/bilibili/Index-Translate#todo).

## Inference

### Default settings

The following settings describe the released [`translate.py`](https://github.com/bilibili/Index-Translate/blob/main/inference/llm/translate.py) client. Its decoding defaults apply across Index-Translate sizes; the commands below explicitly select this card's model.

| Setting | Client default and override |
|---|---|
| Decoding | Greedy, **`temperature=0`**; override with `--temperature` |
| Output budget | `max_tokens=1024`; override with `--max-tokens` |
| Thinking | Disabled through `chat_template_kwargs={"enable_thinking": False}`; no thinking toggle in this CLI |
| Top-p / top-k / min-p | Not explicitly set by this client; inference-server defaults apply |
| Presence / frequency / repetition penalties | Not explicitly set by this client; inference-server defaults apply |
| Random seed | Not explicitly set by this client; inference-server behavior applies |
| Stop strings / stop-token IDs / ignore-EOS | Not explicitly set by this client; inference-server and model defaults apply |
| Model ID | `IndexTeam/Index-Translate-9B`; `INDEX_MODEL` changes the default, and `--model` overrides it |
| Source language | `auto`; override with `--source` / `-s`; auto omits the source-language name from the prompt |
| Target language | `en`; override with `--target` / `-t` |
| Hard constraints | `--hard` / `-H` (repeatable, e.g. format preservation, code/tag protection) |
| Soft constraints | `--soft` / `-S` (repeatable, e.g. style/tone adaptation, domain sense disambiguation) |
| Glossary | Terminology mappings with `--glossary` / `-g` (`src:tgt` or JSON path); formatted into canonical hard term constraints |
| Genre / Domain | Source text genre/domain with `--genre` / `-d` (default: `文本`, e.g. `专栏文章` / `学术论文` / `小说` / `结构化数据`) |
| Instruction | General instruction with `--instruction` / `-i` (formatted into instTrans constraints) |
| Raw prompt | `--raw-prompt`; directly send input text without template wrapping |
| Endpoint | `http://127.0.0.1:8000/v1`; `OPENAI_BASE_URL` changes the default, and `--base-url` overrides it |
| API key | `EMPTY` for local serving; `OPENAI_API_KEY` changes the default, and `--api-key` overrides it |
| Request format | One user message; no system message is added by the client |
| Response streaming | Not enabled by this client; it prints the complete response |
| Transport timeout and retries | No explicit values set by the script; installed OpenAI / HTTPX client behavior applies |
| Proxy handling | Environment proxies disabled for `127.0.0.1`, `localhost`, and `::1`; remote endpoints use the OpenAI client's usual behavior |
| Input / output | Positional text or stdin; input is stripped and empty input is rejected; output is stripped and any emitted thinking block is removed |

The client maps supported short language codes to Chinese language names in the prompt. Provide the full language name for a code outside its mapping.

For standard unconstrained translation:

```text
请将以下{source-language}文本翻译为{target-language}，直接输出翻译结果，不要进行任何解释。

{source-text}
```

For constrained translation, the model follows the canonical **instTrans** format:

```text
请将以下{source-language}{genre}翻译成{target-language}，并且严格遵循所有约束要求。

【源文】
{source-text}

【约束要求】
1. 【硬性要求】...
2. 【注意】...

只输出译文，不要有任何额外说明。
```

With `--source auto`, `{source-language}` is omitted. Prompt length, source text, and generated output all count toward the served context. Parameters left unspecified above are not assigned new fixed values by this model card.

The shipped configuration sets `max_position_embeddings=262144`. The examples below use **`--max-model-len 32768`**, matching the GitHub 2B/9B Translate serving preset. Input plus output must fit within the served context, and available GPU memory constrains practical lengths. Increase the client output budget for longer translations.

The GitHub inference guide gives an approximate BF16 GPU-memory starting point of **24 GB for 9B**, with additional memory needed for KV cache and longer contexts.

### Serve with vLLM

Use a CUDA environment and a vLLM build with Qwen3.5 support. Start the server in one terminal:

```bash
pip install -U vllm
vllm serve IndexTeam/Index-Translate-9B \
  --served-model-name IndexTeam/Index-Translate-9B \
  --host 127.0.0.1 --port 8000 --max-model-len 32768
```

For a short-text smoke test, `--max-model-len 4096` reduces the requested context. Multi-GPU deployments can add appropriate tensor-parallel arguments. See the [serving guide](https://github.com/bilibili/Index-Translate/blob/main/inference/llm/README.md) for runtime details and other OpenAI-compatible servers.

### OpenAI-compatible Python client

Install the client dependencies in another terminal:

```bash
pip install openai httpx
```

This example uses the training-aligned Chinese translation prompt and disables thinking explicitly:

```python
import httpx
from openai import OpenAI

client = OpenAI(
    base_url="http://127.0.0.1:8000/v1",
    api_key="EMPTY",
    http_client=httpx.Client(trust_env=False),
)

# 1. Standard translation
response = client.chat.completions.create(
    model="IndexTeam/Index-Translate-9B",
    messages=[{
        "role": "user",
        "content": "请将以下文本翻译为英语，直接输出翻译结果，不要进行任何解释。\n\n你好，世界！",
    }],
    temperature=0,
    max_tokens=1024,
    extra_body={"chat_template_kwargs": {"enable_thinking": False}},
)
text = response.choices[0].message.content or ""
if "</think>" in text: text = text.split("</think>", 1)[1]
print(text.strip().removeprefix("<think>").strip())

# 2. instTrans constrained translation (hard glossary + soft style constraints)
prompt_with_constraints = """请将以下专栏文章翻译成英语，并且严格遵循所有约束要求。

【源文】
王平仲采用了更加昂贵的碳纤维材料。碳纤维的好处就是它抗裂缝，而且能显著提升混凝土的刚性。

【约束要求】
1. 【硬性要求】专名/术语对照: 碳纤维→carbon fiber、抗裂缝→crack resistance、刚性→stiffness
2. 【注意】保持专业严谨的技术工程文档风格

只输出译文，不要有任何额外说明。"""

response = client.chat.completions.create(
    model="IndexTeam/Index-Translate-9B",
    messages=[{"role": "user", "content": prompt_with_constraints}],
    temperature=0,
    max_tokens=1024,
    extra_body={"chat_template_kwargs": {"enable_thinking": False}},
)
text = response.choices[0].message.content or ""
if "</think>" in text: text = text.split("</think>", 1)[1]
print(text.strip().removeprefix("<think>").strip())
```

### Multimodal image translation (Vision)

The model natively supports end-to-end multimodal image translation across 150 languages (**zero OCR dependency**), directly projecting image features into latent semantic space:

```python
import base64
from openai import OpenAI

# 1. Read local image and encode to base64
with open("screenshot.png", "rb") as f:
    b64_img = base64.b64encode(f.read()).decode("ascii")

# 2. Call OpenAI-compatible endpoint (public API or local serving)
client = OpenAI(
    base_url="https://index-translate.bilibili.com/v1",
    api_key="none",
    default_headers={"User-Agent": "Index-Translate-Client/1.0"}
)

response = client.chat.completions.create(
    model="IndexTeam/Index-Translate-9B",
    messages=[{
        "role": "user",
        "content": [
            {"type": "text", "text": "请将图片中的英文文本翻译为中文，直接输出翻译结果，不要进行任何解释。"},
            {"type": "image_url", "image_url": {"url": f"data:image/png;base64,{b64_img}"}},
        ]
    }],
    temperature=0.0,
    max_tokens=1024,
    extra_body={"chat_template_kwargs": {"enable_thinking": False}}
)
print(response.choices[0].message.content)
```

You can also use the official zero-dependency script [`inference/llm/call_api.py`](https://github.com/bilibili/Index-Translate/blob/main/inference/llm/call_api.py):
```bash
python inference/llm/call_api.py --image test.png --target zh -m Index-Translate-9B
```

Or test it directly in the interactive [Web Demo](https://index-translate.bilibili.com/?p=/site/translate.html). For quantized GGUF weights and visual projector files (`mmproj`), see [Index-Translate-9B-GGUF](https://huggingface.co/IndexTeam/Index-Translate-9B-GGUF). Full tutorial: [Multimodal Image Translation Guide](https://github.com/bilibili/Index-Translate/blob/main/docs/image_translate.md).

### Repository client

After starting the server, use the public client with this model ID:

```bash
git clone https://github.com/bilibili/Index-Translate.git
cd Index-Translate
pip install -r inference/llm/requirements.txt

# Basic translation
python inference/llm/translate.py \
  "你好，世界。今天天气不错。" --target en \
  --model IndexTeam/Index-Translate-9B

# Hard constraint: Terminology glossary (-g / --glossary)
python inference/llm/translate.py \
  "王平仲采用了更加昂贵的碳纤维材料。碳纤维的好处就是它抗裂缝。" --target en \
  -g "碳纤维:carbon fiber, 抗裂缝:crack resistance" \
  --model IndexTeam/Index-Translate-9B

# Hard constraint: Structured data / JSON format preservation (-H / --hard)
python inference/llm/translate.py \
  '{"user_id": 1024, "message": "您的订单已支付完成。"}' --target en \
  -H "保留源文中的 JSON 格式标记不变" \
  --model IndexTeam/Index-Translate-9B

# Soft constraint: Style (-S / --soft) & Genre (-d / --genre)
python inference/llm/translate.py \
  "今天下午的会议临时取消了，改天我们再碰一下商量。" --target en \
  -d "商务邮件" -S "调整为严谨、正式、礼貌的商务公文风格" \
  --model IndexTeam/Index-Translate-9B
```

Use `--source` to specify the input language and `--target` for the output language. For languages without a mapped code in the client, provide the full language name. The client also accepts `--base-url`, `--api-key`, `--max-tokens`, and `--temperature`.

## Examples and practical limits

The following is a recorded Index-Translate-9B case from the official demo. The task asks for Korean translation while preserving the JSON structure, stars, and Chinese hashtag.

Source:

```json
{"title": "⭐2月13日例行维护公告⭐", "content": "#热血航线大和登场#"}
```

Recorded translation:

```json
{"title": "⭐2월 13일 정기 점검 공지⭐", "content": "#热血航线大和登场#"}
```

See the [captured instruction cases](https://github.com/bilibili/Index-Translate/blob/main/inference/llm/cases/instruction_cases.jsonl) for verified hard/soft constraint examples, [translate cases](https://github.com/bilibili/Index-Translate/blob/main/inference/llm/cases/translate_cases.jsonl), and [prompt examples](https://github.com/bilibili/Index-Translate/blob/main/docs/prompts.md) for more directions and instruction formats.

Scores vary across languages and tasks. Combined constraints and low-resource directions remain harder than core-language translation, and successful individual examples do not guarantee that every instruction will be followed. For explicit target syllable counts, use Index-Homura; for native full-document translation, use Index-NativeLong with its dedicated templates.

## Model family

| Family | Models | Task |
|---|---|---|
| Index-Translate | [2B](https://huggingface.co/IndexTeam/Index-Translate-2B) / [9B](https://huggingface.co/IndexTeam/Index-Translate-9B) / [35B-A3B (preview)](https://huggingface.co/IndexTeam/Index-Translate-35B-A3B-preview) | Text translation and translation instructions |
| Index-Echo S2TT | [2B](https://huggingface.co/IndexTeam/Index-Echo-S2TT-2B) / [9B](https://huggingface.co/IndexTeam/Index-Echo-S2TT-9B) | Speech to translated subtitles |
| Index-Echo S2ST | [2B](https://huggingface.co/IndexTeam/Index-Echo-S2ST-2B) / [9B](https://huggingface.co/IndexTeam/Index-Echo-S2ST-9B) | Speech to speech with source-voice conditioning |
| Index-Homura | [2B](https://huggingface.co/IndexTeam/Index-Homura-2B) / [9B](https://huggingface.co/IndexTeam/Index-Homura-9B) | Translation toward a target syllable count |
| Index-NativeLong | [2B](https://huggingface.co/IndexTeam/Index-Nailong-2B) / [9B](https://huggingface.co/IndexTeam/Index-Nailong-9B) | Full-document translation |

**Naming:** Index-NativeLong retains the published model IDs `IndexTeam/Index-Nailong-2B` and `IndexTeam/Index-Nailong-9B`. The [family repository](https://github.com/bilibili/Index-Translate) includes prompt guides, browser-extension code, and a video-dubbing pipeline. This model's [ModelScope page](https://www.modelscope.cn/models/IndexTeam/Index-Translate-9B) mirrors the model card.

## Citation

```bibtex
@techreport{indextranslate2026,
  author={Tianjiao Li and Mengran Yu and Chenyu Shi and Lusheng Zhang and
          Qisi Chen and Yanshan Zhou and Ji Qi and Jingying Liu and
          Yuang Feng and Ziang Cui and Tianxing Yan},
  title={Index-Translate: A Multilingual Translation Model Family --- Text, Speech, Controlled Dubbing, and Long-Document Translation},
  institution={Index LLM Team},
  year={2026},
  month={September},
  url={https://arxiv.org/abs/2609.40181}
}
```

## License

[Apache-2.0](https://github.com/bilibili/Index-Translate/blob/main/LICENSE). Questions and feedback are welcome through [GitHub Issues](https://github.com/bilibili/Index-Translate/issues).
