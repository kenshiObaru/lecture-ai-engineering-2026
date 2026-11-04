# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
- `--trace` で見えたツール呼び出し（ツール名と引数）：
- 1つの質問での LLM 呼び出し回数（`requests`）：
- 追加したツールと、docstring に書いた説明：

## 宿題

- 取り組んだレベル：基礎
- やったこと：Instructionの各行を削除してみた。
- 結果：
  INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。
- 回の内容を聞かれたら get_lecture_summary を使う。
- 用語集に見つからないことは、推測で断定せず「用語集にはない」と伝える。
- 回答の最後に、関係する回（第N回）を示す。
- 宿題の答えそのものは書かず、考え方のヒントで導く。
"""

(aie2026-exercises) @kenshiObaru ➜ /workspaces/lecture-ai-engineering-2026 (lec03) $ uv run python -m app.agent --trace "LoRA とQLoRAの違いは？ "
[model] gemini-3.5-flash-lite
LoRAとQLoRAの主な違いは、ベースモデルの重みを量子化するかどうか、そしてそれに伴う必要なメモリ量にあります。

* **LoRA（Low-Rank Adaptation）**
  事前学習済みの重みは固定したまま、重みの更新分を小さな低ランク行列で表し、その部分だけを学習するPEFT（Parameter-Efficient Fine-Tuning）手法です。学習するパラメータ量を大幅に削減できます。
* **QLoRA**
  LoRAの仕組みをベースにしつつ、ベースモデルの重みをさらに4ビットなどに量子化（Quantization）してメモリを徹底的に節約したうえで学習を行う手法です。これにより、より大きなモデルを小さなGPU環境でも扱えるようになります。

関係する回：第2回

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0008744'), details={'text_prompt_tokens': 1198}, input_text_tokens=1198, input_tokens=1198, output_tokens=206, requests=2, tool_calls=2)


----------------------------------------------------
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。
- 回の内容を聞かれたら get_lecture_summary を使う。
- 回答の最後に、関係する回（第N回）を示す。
- 宿題の答えそのものは書かず、考え方のヒントで導く。
"""
-------------------------------------------------------
**LoRA**と**QLoRA**の主な違いは、ベースモデルの重みをそのまま（通常は16ビットなど）保持するか、**量子化（Quantization）して軽量化するか**という点にあります。

* **LoRA（Low-Rank Adaptation）**
  事前学習済みのベースモデルの重みを固定し、追加の小さな低ランク行列（アダプタ）だけを学習することで、効率的に微調整を行う手法です。学習するパラメータ数を大幅に減らすことができます。

* **QLoRA（Quantized LoRA）**
  LoRAの仕組みをベースにしつつ、ベースモデルの重みを**4ビットなど非常に低いビット数に量子化**してメモリ消費をさらに劇的に削減する手法です。これにより、限られたGPUメモリ環境でも、より大きなモデルを効率よく学習できるようになります。

第2回</code>

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0009019'), details={'text_prompt_tokens': 1148}, input_text_tokens=1148, input_tokens=1148, output_tokens=223, requests=2, tool_calls=2)
---------------------------------------------------------

INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。
- 回の内容を聞かれたら get_lecture_summary を使う。
- 宿題の答えそのものは書かず、考え方のヒントで導く。
"""
------------------------------------------

それぞれの特徴をまとめると以下のようになります。

* **LoRA**
  * 事前学習済みの重みは固定したまま、重みの更新分（低ランク行列）だけを学習してパラメータ数やメモリを節約する手法です。
  * 重み自体は通常（16ビットや32ビットなど）の精度で読み込んで学習します。

* **QLoRA**
  * LoRAの仕組みをベースにしつつ、**ベースモデルの重みを4ビットなどに量子化（Quantized）**してメモリ消費をさらに大幅に削った手法です。
  * これにより、より大きなモデルを小さなGPUメモリでも効率よくファインチューニングできるようになります。

講座（第2回）の演習では、モデルサイズに応じてLoRAやQLoRAを使い分けるポイントなどを扱います。

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0009892'), details={'text_prompt_tokens': 1114}, input_text_tokens=1114, input_tokens=1114, output_tokens=262, requests=2, tool_calls=2)

--------------------------------------------
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。
- 回の内容を聞かれたら get_lecture_summary を使う。
"""
----------------------------------------------
(aie2026-exercises) @kenshiObaru ➜ /workspaces/lecture-ai-engineering-2026 (lec03) $ uv run python -m app.agent --trace "LoRA とQLoRAの違いは？ "
[model] gemini-3.5-flash-lite
講座の用語集によると、**LoRA**と**QLoRA**の違いは以下の通りです。

* **LoRA**
  * 事前学習済みの重みは固定したまま、重みの更新分を2つの小さな低ランク行列の積で表し、その部分だけを学習するPEFT（Parameter-Efficient Fine-Tuning）手法です。学習パラメータや保存ファイルを少なく抑えられます。
* **QLoRA**
  * ベースモデルの重みを4ビットなどに**量子化**してメモリを大幅に節約したうえで、LoRAによる学習を行う手法です。これにより、より大きなモデルを小さなGPUでも効率よく扱えるようになります。

なお、本講座の第2回では主にLoRAを扱い、発展として4ビット読み込み時のメモリや出力の違いについても比較します。

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0008597'), details={'text_prompt_tokens': 1074}, input_text_tokens=1074, input_tokens=1074, output_tokens=215, requests=2, tool_calls=2)
----------------------------------------------
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。
"""
------------------------------------------------
------------------------------------------------
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 回の内容を聞かれたら get_lecture_summary を使う。
"""
------------------------------------------------
[model] gemini-3.5-flash-lite
**LoRA**と**QLoRA**は、どちらも大規模言語モデル（LLM）の効率的なファインチューニング（PEFT手法）を行うための技術ですが、**ベースモデルの精度（ビット数）とメモリ使用量**に大きな違いがあります。

それぞれの特徴と違いは以下の通りです。

---

### 1. LoRA（Low-Rank Adaptation）
* **定義**: 事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習する手法です。
* **特徴**:
  * 学習するパラメータが大幅に削減され、学習結果（アダプタ）も小さいファイルで保存できます。
  * 基本的に、ベースモデルは通常の精度（FP16やBF16など）のまま読み込んで学習を行います。

### 2. QLoRA（Quantized LoRA）
* **定義**: ベースモデルの重みを **4ビットなど極限まで量子化（圧縮）** してメモリを節約した状態で固定し、その上で LoRA の学習を行う手法です。
* **特徴**:
  * ベースモデルのメモリ消費を劇的に削減できるため、**「手元にある小さなGPU（VRAMが少ない環境）」でも、より大きなモデルの学習が可能**になります。
  * 量子化による精度の劣化を抑える工夫（NF4など）が組み込まれており、モデルの性能を維持しつつ低コストで学習できます。

---

### まとめ（比較）

| 項目 | LoRA | QLoRA |
| :--- | :--- | :--- |
| **ベースモデルの精度** | 通常（FP16 / BF16 など） | 量子化（4ビットなど） |
| **必要なGPUメモリ** | 多め（モデルサイズによる） | **非常に少ない** |
| **狙い** | パラメータ数と学習コストの削減 | さらに大規模なモデルを小規模GPUで動かすこと |

本講座（AIE2026）の第2回では、これらを具体的に扱い、モデルを4ビットで読み込んだときのメモリや出力の違いについても触れていきます。

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0016028'), details={'text_prompt_tokens': 1026}, input_text_tokens=1026, input_tokens=1026, output_tokens=518, requests=2, tool_calls=2)
-------------------------------------------------
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。
"""
-------------------------------------------------
[model] gemini-3.5-flash-lite
講座の用語集によると、**LoRA** と **QLoRA** の違いは以下の通りです。

* **LoRA（Low-Rank Adaptation）**
  * 事前学習済みの重みは固定したまま、重みの更新分を2つの小さな低ランク行列の積で表し、その小さな行列（パラメータ）だけを学習するPEFT手法です。学習するパラメータが少なく、学習結果（アダプタ）も小さいファイルで保存できます。
* **QLoRA（Quantized LoRA）**
  * ベースモデルの重みを4ビットなどに**量子化**してメモリを大幅に節約したうえで、LoRAによる学習を行う手法です。これにより、より大きなモデルをより小さなGPUメモリで扱えるようになります。

まとめると、**「ベースモデルをそのまま（高精度な形式で）動かしながらLoRAで学習するのが LoRA、ベースモデルを4ビットなどに量子化してメモリを節約した状態でLoRAを適用するのが QLoRA」** といえます。

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0009607'), details={'text_prompt_tokens': 1044}, input_text_tokens=1044, input_tokens=1044, output_tokens=259, requests=2, tool_calls=2)
--------------------------------------------------
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
"""
--------------------------------------------------
### まとめ（比較表）

| 項目 | LoRA | QLoRA |
| :--- | :--- | :--- |
| **ベースモデルの重み** | 通常の精度（16ビットなど）で保持 | 4ビットなどに**量子化**して保持 |
| **必要なGPUメモリ（VRAM）** | QLoRAよりは多い | **大幅に少ない**（大きなモデルを扱いやすい） |
| **学習対象** | 低ランク行列（アダプタ） | 低ランク行列（アダプタ） |
| **モデルの大きさ vs GPU** | モデルサイズに応じたVRAMが必要 | 限られたVRAMでも巨大モデルの学習が可能 |

本講座（AIE2026）の第2回では、これらの仕組みを詳しく扱い、演習を通じて実際の動作や違いを体験します。

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0017238'), details={'text_prompt_tokens': 996}, input_text_tokens=996, input_tokens=996, output_tokens=570, requests=2, tool_calls=2)
----------------------------------------------------
----------------------------------------------------

### 2. QLoRA
* **仕組み**: LoRAの仕組みをベースにしつつ、**ベースモデルの重みを4ビットなどに量子化（圧縮）**してメモリ消費をさらに抑えた状態で学習する手法です。
* **特徴**: 4ビット量子化によりVRAM（GPUメモリ）の大幅な節約ができるため、**これまでGPUに乗せられなかったような巨大なモデルでも、比較的小さなGPUで効率よくファインチューニングできる**のが最大のメリットです。

---

### まとめ
* **LoRA**: ベースモデルを通常の精度（16ビット等）のまま、追加する低ランク行列だけを効率よく学習する。
* **QLoRA**: LoRAの効率さに加え、ベースモデルの重みを4ビット等に量子化して、さらに必要なメモリを大幅に削減して学習する。

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0012450'), details={'text_prompt_tokens': 950}, input_text_tokens=950, input_tokens=950, output_tokens=384, requests=2, tool_calls=2)

- 気づき・疑問：
INSTRUCTIONS（システムプロンプト）に記述する制約は、「どのツールをいつ使うか」「どの情報に基づいて回答するか」「どのように振る舞うか（教育的な配慮）」を制御するための極めて重要なスイッチとなっています。

ツール利用の強制（ルール1, 2）が抜けると、エージェントがツールを使わなくなります。

出力フォーマットや教育的方針の指定（ルール3, 4, 5）が抜けると、回答のトーンやフォーマットが揺らいだり、宿題の答えを直接教えてしまうといった不都合が生じます。
