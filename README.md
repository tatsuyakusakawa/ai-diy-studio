# AI DIY Studio

ローカル環境でAI画像・動画生成パイプラインを自作するプロジェクト。

ComfyUI + FLUX.2 Klein 9B + n8n を組み合わせて、夜セットして寝ている間に15カット自動生成する仕組みを作りました。

## 動作環境

- M4 Mac（メモリ48GB）
- ComfyUI
- n8n（Docker）
- FLUX.2 Klein 9B（`flux-2-klein-9b.safetensors`）

## できること・できないこと

**できる**
- 食材・料理の静止画
- 風景・城下町・建物
- 動物のアップ
- 雰囲気のあるイメージカット
- 夜セットして朝に15カット完成している自動化

**まだ難しい**
- 複数人物が登場するストーリー（四肢破綻が起こる）
- 全身ショットが必要なシーン
- 服装・顔を複数カットで固定する表現

## ファイル構成

```
workflows/
├── n8n_flux2_images_only.json   # FLUX.2 画像生成ワークフロー（n8n）
└── n8n_ltx23_i2v.json          # LTX-2.3 動画生成ワークフロー（n8n）

prompts/
└── prompt_guide.md              # FLUX.2 / LTX-2.3 プロンプトガイド

docs/
└── setup.md                    # 環境構築手順（作成中）
```

## 使い方

1. `workflows/` のJSONをn8nにインポート
2. `Init State` ノードにプロンプトを記入
3. 実行してComfyUIが動いているのを確認したら寝る

詳細は [Note記事]() を参照してください。
