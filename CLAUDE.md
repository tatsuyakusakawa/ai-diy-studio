# AI DIY Studio セットアップ手順（Claude Code用）

このファイルはClaude Codeなどに読ませてセットアップを自動化するための指示書です。
**モデルのダウンロードとDocker Desktopのインストールは先に手動で済ませてください。**
手順は `docs/setup.md` を参照。

---

## 作業ディレクトリ

`~/Developer/AI_Studio/` 以下に作業する。なければ作成する。

---

## 1. Homebrewの確認・インストール

```bash
which brew || /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

---

## 2. ComfyUIのセットアップ

```bash
cd ~/Developer/AI_Studio
git clone https://github.com/comfyanonymous/ComfyUI.git
cd ComfyUI
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

## 3. 必要なカスタムノードのインストール

```bash
cd ~/Developer/AI_Studio/ComfyUI/custom_nodes

# ComfyUI-GGUF（GGUFモデル対応）
git clone https://github.com/city96/ComfyUI-GGUF.git
cd ComfyUI-GGUF && pip install -r requirements.txt && cd ..

# ComfyUI Video Helper Suite（動画出力）
git clone https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite.git
cd ComfyUI-VideoHelperSuite && pip install -r requirements.txt && cd ..

# ComfyUI-KJNodes
git clone https://github.com/kijai/ComfyUI-KJNodes.git
cd ComfyUI-KJNodes && pip install -r requirements.txt && cd ..

# ComfyUI-LTXVideo
git clone https://github.com/Lightricks/ComfyUI-LTXVideo.git
cd ComfyUI-LTXVideo && pip install -r requirements.txt && cd ..
```

---

## 4. モデルフォルダの確認

以下のフォルダが存在することを確認し、なければ作成する：

```
ComfyUI/models/
├── diffusion_models/   ← flux-2-klein-9b.safetensors
├── text_encoders/      ← qwen_3_8b.safetensors, gemma GGUF, ltx text projection
├── vae/                ← flux2-vae.safetensors, LTX23 VAEs
├── checkpoints/        ← LTX23_audio_vae_bf16.safetensors（vaeと同じファイルをコピー）
├── unet/               ← ltx-2.3-22b-distilled-Q5_K_M.gguf
└── latent_upscale_models/ ← ltx-2.3-spatial-upscaler-x2-1.0.safetensors
```

ダウンロード済みのモデルが正しい場所にあるか確認してユーザーに報告する。

---

## 5. n8nのDockerセットアップ

```bash
docker run -d \
  --name n8n_manager \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  --add-host=host.docker.internal:host-gateway \
  n8nio/n8n:latest
```

起動確認：`http://localhost:5678`（Chromeで開く）

---

## 6. n8nワークフローのインポート

n8nのGUI操作が必要なためユーザーに案内する：

1. `http://localhost:5678` をChromeで開く
2. 左メニュー「Workflows」→「Import from File」
3. `workflows/n8n_flux2_images_only.json` をインポート
4. 同様に `workflows/n8n_ltx23_i2v.json` もインポート

---

## 7. 動作確認

ComfyUIを起動して確認：

```bash
cd ~/Developer/AI_Studio/ComfyUI
source venv/bin/activate
python main.py --listen
```

`http://localhost:8188` にアクセスできれば完了。

---

## 注意事項

- n8nからComfyUIへの接続は `host.docker.internal:8188`（`localhost`ではない）
- ComfyUIは起動したままn8nを実行する
- モデルが正しい場所にないとComfyUIがエラーを出す
