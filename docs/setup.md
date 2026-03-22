# セットアップガイド

## 全体の流れ

1. 手動でソフトをインストール（Docker Desktop）
2. 手動でモデルをダウンロード
3. Claude CodeなどのCLIツールに `CLAUDE.md` を読ませて残りを自動セットアップ

---

## Step 1：手動インストール（GUI必要）

### Docker Desktop
https://www.docker.com/products/docker-desktop/

M4 Macの場合は「Mac with Apple Silicon」を選択してダウンロード・インストール。

---

## Step 2：モデルのダウンロード（手動）

ダウンロード後、ComfyUI のモデルフォルダに配置する。
フォルダのパスは `ComfyUI/models/` 以下。

### FLUX.2 Klein 9B（画像生成モデル）

| ファイル名 | 配置先 | ダウンロード元 |
|---|---|---|
| `flux-2-klein-9b.safetensors` | `diffusion_models/` | [HuggingFace - black-forest-labs/FLUX.2-klein](https://huggingface.co/black-forest-labs/FLUX.2-klein) |
| `qwen_3_8b.safetensors` | `text_encoders/` | [HuggingFace - Comfy-Org/z_image_turbo](https://huggingface.co/Comfy-Org/z_image_turbo/tree/main/split_files/text_encoders) |
| `flux2-vae.safetensors` | `vae/` | [HuggingFace - Comfy-Org/flux2-dev](https://huggingface.co/Comfy-Org/flux2-dev/tree/main/split_files/vae) |

### LTX-2.3（動画生成モデル）

| ファイル名 | 配置先 | ダウンロード元 |
|---|---|---|
| `ltx-2.3-22b-distilled-Q5_K_M.gguf` | `unet/` | [HuggingFace - Lightricks/LTX-Video](https://huggingface.co/Lightricks/LTX-Video) |
| `gemma-3-12b-it-qat-UD-Q4_K_XL.gguf` | `text_encoders/` | [HuggingFace - unsloth/gemma-3-12b-it-qat-GGUF](https://huggingface.co/unsloth/gemma-3-12b-it-qat-GGUF) |
| `ltx-2.3_text_projection_bf16.safetensors` | `text_encoders/` | [HuggingFace - Lightricks/LTX-Video](https://huggingface.co/Lightricks/LTX-Video) |
| `LTX23_video_vae_bf16.safetensors` | `vae/` | [HuggingFace - Lightricks/LTX-Video](https://huggingface.co/Lightricks/LTX-Video) |
| `LTX23_audio_vae_bf16.safetensors` | `vae/` と `checkpoints/` の両方 | [HuggingFace - Lightricks/LTX-Video](https://huggingface.co/Lightricks/LTX-Video) |
| `ltx-2.3-spatial-upscaler-x2-1.0.safetensors` | `latent_upscale_models/` | [HuggingFace - Lightricks/LTX-Video](https://huggingface.co/Lightricks/LTX-Video) |

> **注意**：ダウンロードには合計50GB以上の空き容量と時間が必要。

---

## Step 3：Claude Codeに残りをやってもらう

このリポジトリをクローンして、Claude Codeに以下を伝える：

```
このリポジトリのCLAUDE.mdを読んで、環境をセットアップしてください
```

Claude Codeが以下を自動でやってくれる：
- Homebrewのインストール確認
- ComfyUIのクローンと環境構築
- 必要なカスタムノードのインストール
- n8nのDockerセットアップ
- n8nワークフローのインポート手順案内

---

## Step 4：動作確認

- ComfyUI：`http://localhost:8188`
- n8n：`http://localhost:5678`（Chromeで開く）
