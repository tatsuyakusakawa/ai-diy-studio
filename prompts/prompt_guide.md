# Flux & LTX-2 プロンプトガイド
# LLM（Gemini / Ollama）に読み込ませて使用するガイドです

---

## FLUX.2 Klein（画像生成）

### モデル仕様
- パラメータ数：9B（`flux-2-klein-9b.safetensors`）
- テキストエンコーダ：Qwen 3 8B（`qwen_3_8b.safetensors`）
- ネガティブプロンプト：**公式非対応**（CFG=1のため効果なし、空白でOK）
- 推奨ステップ数：14
- I2Iは`Flux2_Klein_I2I 9B.json`を使用（KSampler + denoise方式）

### 基本構造
```
[被写体と状況], [感情・表情の物理的描写], [光と影], [テクスチャ指定], [スタイル], [技術品質]
```
※Qwen3ベースのため、自然言語的な記述が有効。カメラ/レンズ情報は補助的に使う。

### 必須キーワード（AI臭さ消去・高画質化）
```
RAW photo
shot on [カメラ機種] with [レンズ mm f/値]
natural film grain
photorealistic / hyperrealistic
authentic depth of field
photojournalistic / wildlife photography
```

### カメラ・レンズの組み合わせ例
| 用途 | カメラ | レンズ |
|---|---|---|
| ポートレート | Canon EOS R5 / Nikon Z9 | 85mm f/1.4 または 135mm f/2.0 |
| 動物・スポーツ | Sony A1 | 400mm f/2.8 または 200mm f/2.0 |
| 風景 | Sony A7R V | 24mm f/8 または 16mm f/11 |
| ストリート | Leica M11 | 35mm f/2.0 または 50mm f/1.4 |

### テクスチャ・リアリティ強化
```
individual fur/hair strands visible
visible skin pores
subsurface scattering
natural skin imperfections
water droplets catching light
rough surface texture
```

### 光の指定
```
golden hour sunlight         # 夕方の温かい光
harsh natural shadows        # 自然な強い影
dappled forest light         # 木漏れ日
overcast diffused light      # 曇り・柔らかい光
dramatic side lighting       # サイドからのドラマチックな光
```

### スタイル指定
```
photojournalistic            # 報道写真風（AI臭さ消去）
documentary photography      # ドキュメンタリー風
editorial photography        # 雑誌風
street photography           # ストリート写真風
wildlife photography         # 野生動物写真風
```

### 感情表現のルール（感情名を書かない・物理的な状態で描写する）
「terrified kitten」より「ears flattened, eyes wide with dilated pupils」が効く。

| 感情 | 物理的描写 |
|---|---|
| 恐怖・危機 | ears flattened against skull, eyes wide with dilated pupils, body pressed low, fur slightly raised along spine, trembling |
| 叫び・助けを求める | mouth open in a silent cry, whiskers pulled back, paws braced against surface, eyes glistening |
| 集中・決意（犬） | ears pricked forward, eyes locked forward with intense focus, body low and coiled, tail level not wagging |
| 希望・探索 | head raised, nose lifted, ears slightly forward, eyes scanning distance, expectant posture |
| 安堵・救出後 | soft half-closed eyes, relaxed jaw, tongue slightly out, body settled low, tail in gentle slow wag |
| 温かみ・幸福 | curled together, eyes soft and half-lidded, relaxed breathing visible, gentle contact between animals |

### 四肢破綻防止（ポジティブプロンプトで対応、ネガティブは効かない）
```
anatomically correct [animal], four legs clearly visible,
well-formed paws with distinct toes, natural [dog/cat] proportions,
correct quadruped anatomy, realistic fur texture
```
- クローズアップ・バストショットにすると四肢が映らず破綻しにくい
- 全身を映す場合は上記の記述を必ず追加する
- 足先のアップは`five toes on front feet, natural pad texture, fur between toes`を追加

### T2I サンプルプロンプト（動物用）
```
RAW photo, shot on Sony A1 with 400mm f/2.8 lens,
golden retriever standing alert in a snowy pine forest,
individual wet fur strands visible on chest and paws,
warm amber eyes focused intently forward,
breath visible as a faint mist in cold air,
overcast diffused winter light, soft shadows on snow,
natural film grain, wildlife photography, authentic depth of field,
hyperrealistic fur texture detail, 8K
```

### レスキューストーリー シーン別プロンプト例

**フック：子猫の危機**
```
RAW photo, shot on Canon EOS R5 with 135mm f/2.0,
a small orange tabby kitten with green eyes and white paws,
crouched low in deep snow, ears flattened against skull,
eyes wide with dilated pupils, body trembling,
fur dusted with snowflakes, desperate expression,
dramatic cold blue-grey light, harsh shadows,
anatomically correct cat, four legs clearly visible,
natural film grain, wildlife photography, 8K
```

**ヒーロー登場：レトリバーが気づく**
```
RAW photo, shot on Canon EOS R5 with 85mm f/1.4,
a golden retriever with honey-amber fur, dark brown eyes, white chest patch,
ears pricked sharply forward, eyes locked with intense focused expression,
nose raised slightly, body coiled and alert, breath visible as white mist,
urgent posture, dramatic side lighting from cold winter sun,
anatomically correct dog, four legs clearly visible,
natural film grain, wildlife photography, 8K
```

**救出後：一緒に温まる**
```
RAW photo, shot on Canon EOS R5 with 85mm f/1.4,
a golden retriever with honey-amber fur and dark brown eyes,
lying on its side in warm indoor light,
a small orange tabby kitten with green eyes and white paws
curled against the dog's chest, both animals with soft half-closed eyes,
relaxed body posture, gentle contact between them,
warm amber light from a nearby window,
anatomically correct animals, natural proportions,
natural film grain, wildlife photography, shallow depth of field, 8K
```

### キャラクター一貫性のルール（ストーリー動画用）

#### I2I（画像→画像）ワークフロー
カット2以降は前カットの画像を参照してKSamplerでI2Iを行う（`Flux2_Klein_I2I 9B.json`）。

**denoiseガイド：**
| Denoise | 効果 | 使う場面 |
|---|---|---|
| 0.15〜0.25 | ほぼ同一・微調整のみ | 欠点修正・照明変更 |
| 0.30〜0.45 | 表情・ポーズ変更、キャラ維持 | シーン変化 **← 推奨** |
| 0.50〜0.65 | 大きな場面変化、キャラはギリギリ維持 | 背景を大幅に変える |
| 0.70以上 | キャラがドリフトする | 非推奨 |

デフォルト値：0.55（n8nワークフローの初期値）

**3〜4シーンごとに最初の参照画像に「リセット」する**（累積ドリフト防止）

#### キャラ説明ブロック（全シーンにコピペ）
```
# ゴールデンレトリバー（変えない）
A golden retriever with honey-amber fur, dark brown eyes,
floppy ears, white chest patch, medium build

# 子猫（変えない）
A small orange tabby kitten with green eyes, white paws,
pink nose, striped tail, approximately 8 weeks old
```

#### 同フレームに2キャラを入れる場合
- フレームを引き（ワイドショット）にして体のサイズ差を活かす
- それぞれを別アングルから撮ったカットで代替する方が安全

### I2I（画像→画像）の注意点
- denoiseは変化量で決める（上のキャラ一貫性ガイド参照）
- stepsはT2Iと同じ10〜12で良い
- ネガティブプロンプトはCFG=1のため効果なし（空白でOK）

---

### 空間描写のルール（複数キャラ・位置関係）

#### 基本原則
空間的な位置関係はモデルが最も苦手とする領域。**曖昧な表現は必ず失敗する。**
「near the net」ではなく「trapped INSIDE the net」のように大文字・前置詞で明示する。

#### 位置の書き方リファレンス
```
# 画面内の位置
in the foreground          # 手前
in the background          # 奥
in the center of frame     # 画面中央
in the left/right side     # 左右
in the upper/lower frame   # 上下
```

```
# 内外・接触関係
INSIDE the net / trapped within   # ネットの中（大文字推奨）
OUTSIDE the net / just beyond     # ネットの外
pressed against the net           # ネットに接触
standing 1 meter away from        # 距離を明示
separated by the net              # ネットで隔てられた
```

```
# 複数キャラの空間関係
[キャラA] in the foreground, [キャラB] in the background
[キャラA] on the LEFT side, [キャラB] on the RIGHT side
[キャラA] INSIDE [物体], [キャラB] OUTSIDE [物体], facing each other
```

#### 複数キャラを同一フレームに入れる場合のプロンプト構造
```
[シーン全体の状況説明].
[キャラA + 位置 + 状態].
[キャラB + 位置 + 状態].
[2キャラの関係性・視線・距離感].
[光・背景].
[カメラ・スタイル].
```

#### 実例（親カモ + 小鴨）
```
RAW photo, shot on Canon EOS R5 with 35mm f/2.0,
wide establishing shot of a Nordic winter harbor, heavy snow falling.
A parent mallard duck with dark green head and orange beak
is trapped INSIDE a fishing net in the BACKGROUND CENTER,
struggling weakly, unable to move.
A tiny bright yellow mallard duckling stands OUTSIDE the net
in the FOREGROUND LEFT, 30cm away from the net,
beak wide open in a silent cry, body trembling, eyes glistening,
looking desperately toward the trapped parent.
The net clearly separates the two birds.
Overcast cold light, gritty harbor atmosphere,
natural film grain, wildlife photography, 8K.
```

#### I2I Kontextスタイルの空間変化記述
前シーン画像を参照しながら新シーンを作る場合：
```
# ❌ 悪い例（全シーンを新規記述）
"A tiny duckling stands outside the net..."

# ✅ 良い例（前シーンからの変化を記述）
"The same Nordic harbor scene with the fishing net.
The parent mallard remains trapped INSIDE the net in the background.
NOW, a tiny bright yellow duckling has appeared OUTSIDE the net
in the foreground, crying desperately..."
```

#### 失敗パターンと対策
| 失敗 | 原因 | 対策 |
|---|---|---|
| 小鴨がネットの中に入る | 「outside」が弱い | 「OUTSIDE」大文字 + 距離を数値で指定 |
| 両キャラが同じ位置に重なる | 位置指定なし | foreground/background + left/right を明示 |
| 背景・設定が変わる | I2Iでシーン全体を書き換えている | 「The same scene」で始める |
| キャラが消える | プロンプトに記述がない | 画面に残したいものはすべてプロンプトに書く |

### 解像度
- YouTube Shorts（縦）：720×1280（32の倍数）
- 横動画：1280×720

---

## LTX-VIDEO-2.3（動画生成）

### 最重要原則
1. **音を映像描写の中に統合する**（末尾にまとめない）
2. **セリフ・鳴き声を必ず1箇所は入れる**（音声生成の最強ヒント）
3. **環境音を動詞で列挙する**（howls, hisses, creaks, clinks等）
4. **プロンプトは参照画像と一致させる**（矛盾するとプロンプトが勝ち、画像が無視される）
5. **ネガティブプロンプトに無音を明示する**

### 基本構造
```
[Shot type + 被写体の視覚描写],
[光と背景の描写],
[カメラの動き指定],
[環境音を動詞で列挙 — 具体的に3つ以上],
[キャラクターの動作 + 鳴き声/セリフ],
[ムード・雰囲気].
```

### ❌ やってはいけない書き方
```
# 悪い例1：音を最後にまとめる
"A kitten sits in snow. Wind and snow sounds fill the air."

# 悪い例2：音の描写が抽象的
"Ambient outdoor sounds."

# 悪い例3：プロンプトと参照画像が矛盾
（猫の画像を渡しながら「A woman walks in a park」と書く → 女性動画になる）

# 悪い例4：カメラ指定なし（ズームになりやすい）
"A dog stands in a field."
```

### ✅ 正しい書き方
```
# 良い例：ウェイトレス（音声生成成功の実例）
A close-up shot of a young waitress in a retro 1950s diner,
her warm brown eyes meeting the camera with a gentle smile.
Soft, warm light from overhead fixtures illuminates her features.
The camera begins slightly to her side, then slowly pushes in toward her face.
The ambient sounds of clinking dishes, distant conversations,
and the gentle hum of a jukebox fill the air.
She tilts her head slightly and says in a friendly, warm voice:
"Here is an even cleaner simple workflow."
The mood is inviting, timeless, and full of classic American diner charm.
```

```
# 良い例：吹雪の子猫（環境音）
A close-up shot of three tiny kittens huddled tightly together
on snow-covered ground, surrounded by snow-laden pine trees.
Their fur is dusted with snowflakes, small bodies pressed together for warmth.
The camera remains static.
A fierce blizzard rages — the howling wind roars through the trees,
loose snow hisses and swirls across the ground,
pine branches creak and groan under the weight of ice.
The gusts rise and fall in waves, filling the air with a deep cold wail.
One kitten trembles slightly and lets out a faint desperate mew against the storm.
The mood is raw, cold, and quietly heartbreaking.
```

### カメラの動き指定
```
Static camera.                   # ズーム防止（基本はこれ）
Slow push in toward face.        # ゆっくり寄る（感情強調）
Slow pan left/right.             # 横移動
Handheld camera, slight shake.   # 手持ち感・緊迫感
```

### 音の書き方リファレンス
| 音の種類 | 書き方の例 |
|---|---|
| 吹雪・嵐 | howling wind roars, snow hisses and swirls, branches creak and groan |
| 足音 | sharp rhythmic crunch of paws on snow |
| 息づかい | rapid panting audible, each breath a visible white puff |
| 動物の鳴き声 | lets out a faint desperate mew, a soft plaintive whimper |
| 環境音（屋内） | clinking dishes, distant conversations, hum of a jukebox |
| セリフ | says in a warm voice: "..." |
| 自然音 | river rushing over stones, leaves rustling in wind |
| 緊迫音 | urgent barking echoes, claws scraping on ice |

### ネガティブプロンプト（必ず含める）
```
silent or muted audio, no sound, muted animals, distorted voice,
background noise drowning out subject, off-sync audio, robotic voice,
blurry, camera shake, overexposed, low contrast, AI artifacts
```

---

## 動物レスキューストーリー フォーマット

### ストーリー構成（15カット・約40秒）
```
1. フック（2カット）
   → 子猫/子狐が危機に陥っている → 視聴者を3秒で引き込む
   → 音：苦しそうな鳴き声 + 危険な環境音

2. ヒーロー登場（2カット）
   → レトリバー等が異変に気づく → 表情・耳・目の反応
   → 音：ハッとした息づかい、雪を踏む音

3. 奮闘（5カット）
   → 向かう / 障害を超える / 近づく / 手を伸ばす / 届くか？
   → カット毎に別アングル・別場面で緊張感を維持
   → 音：疾走音、荒い息、雪崩音等シーンに合わせる

4. 救出（3カット）
   → 触れる / 引き上げる / 抱える
   → 音：安堵の声、柔らかい鳴き声

5. ハッピーエンド（3カット）
   → 一緒に温まる / 毛づくろい / 寄り添い眠る
   → 音：穏やかな環境音、小さな鳴き声、静寂
```

### シーン別プロンプト生成ルール
- **フック**: 音を最初に感じさせる。視覚情報より音で感情を先行させる
- **奮闘シーン**: 激しい動きより「集中した表情」のクローズアップが得意
- **同フレームに2キャラ**: なるべく避ける。「ヒーローが近づく」→「被救助動物のアップ」の交互カットで代替
- **ハッピーエンド**: 寄り添う静止に近い構図 + 温かい音 → LTX-2が最も得意なシーン

---

## n8nワークフローへの渡し方

### ワークフローは2つに分かれている

画像生成（FLUX.2）と動画生成（LTX-2.3）はn8nで別々のワークフローとして実行する。

---

### ① 画像生成ワークフロー（`n8n_flux2_images_only.json`）

`Init State`ノードの`cuts`に貼り付ける。

```js
const cuts = [
  // シーン1（最初のカット or シーン切り替えは newScene: true でT2I生成）
  {
    prompt: 'RAW photo, ...',   // FLUX.2 T2Iプロンプト
    newScene: true              // ← T2I指定（省略するとI2I）
  },
  // シーン1の続き（I2I：前カットから変化を記述）
  {
    prompt: 'The same scene. Now ...'
  },
  // シーン2（新しい場所・キャラ → newScene: true でリセット）
  {
    prompt: 'RAW photo, ...',
    newScene: true
  },
  // ...
];
```

**ルール**
- **Cut 1は自動的にT2I**（`newScene`フラグ不要）
- **`newScene: true`** = 新しいシーン開始。T2Iで生成し、以降はこのカットを参照してI2I
- カット数は何カットでも自由

実行後、各カット画像はComfyUIのinputフォルダに `cut_01.png`, `cut_02.png`... として自動保存される。

---

### ② 動画生成ワークフロー（`n8n_ltx23_i2v.json`）

**手順**：
1. FLUX.2ワークフローを実行し全カットの画像を生成
2. ComfyUIのoutputフォルダで画像を確認（`cut_01.png`〜の順で並んでいる）
3. 承認した画像をカット順にGoogle AI Studioへアップロード
4. Google AI Studioがmotion promptを生成 → コードをn8n `Init State`ノードへ貼り付ける
5. n8n動画ワークフローを実行

`Init State`ノードの`cuts`フォーマット：

```js
const cuts = [
  { motion: '...' },  // LTX-2.3 モーションプロンプト（cut_01.pngに対応）
  { motion: '...' },  // cut_02.pngに対応
  // ...
];
```

**ルール**
- **`imageFile`は不要** — インデックスから自動的に `cut_01.png`, `cut_02.png`... を参照する
- **`motion`** = LTX-2.3用（動きの説明。カメラ指定・音・動詞で記述）
- カット数はFLUX.2で生成したカット数と一致させること

---

### Google AI StudioでのInit State生成方法

1. **画像**：承認したカット画像をカット順（cut1, cut2...）にアップロード
2. **指示**：以下のテンプレートを使う

```
添付した画像をカット順（アップロード順）に読み込んでください。
各画像の動きをLTX-2.3向けのモーションプロンプトとして英語で生成し、
以下のJSON形式で出力してください。

出力形式：
const cuts = [
  { motion: '...' },
  { motion: '...' },
  ...
];

モーションプロンプトのルール：（prompt_guide.mdのLTX-VIDEO-2.3セクションを参照）
```

---

## LLMへの依頼方法
テンプレートは別ファイル `llm_templates.md` を参照してください。
毎回シーン内容に合わせて編集してから渡します。

---

## 設定値リファレンス（M4 Mac 48GB 確認済み）

### LTX-2.3ワークフロー設定
```
モデル：ltx-2.3-22b-distilled-Q5_K_M.gguf
テキストエンコーダ：gemma-3-12b-it-qat-UD-Q4_K_XL.gguf + ltx-2.3_text_projection_bf16.safetensors（DualCLIPLoaderGGUF）
映像VAE：LTX23_video_vae_bf16.safetensors（VAELoader）
音声VAE：LTX23_audio_vae_bf16.safetensors（LTXVAudioVAELoader → checkpointsフォルダに置く）
LTXVImgToVideoConditionOnly Stage1：strength=0.6, bypass=False
LTXVImgToVideoConditionOnly Stage2：strength=1.0, bypass=False
Stage2 ManualSigmas：0.909375, 0.421875, 0.0
VAEDecodeTiled：tile_size=512, overlap=64, temporal_size=512, temporal_overlap=64
frames：97（約3.9秒）
```

### 解像度（Shorts縦動画）
```
ResizeImageMaskNode：704×1280（9:16、32の倍数）
ImageScaleBy：0.5（変えない）
→ latent: 352×640 → upsampler 2x → 704×1280
```

### 有効なframe数（8n+1）
```
57, 65, 73, 81, 89, 97, 105, 113, 121
```
