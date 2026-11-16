# 使用したプロンプト

```
uv run python -m app.agent --trace "LoRA とQLoRAの違いは？"
```

# 変更前

## agent.pyの内容

- プロンプトの内容

```
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。
- 回の内容を聞かれたら get_lecture_summary を使う。
- 用語集に見つからないことは、推測で断定せず「用語集にはない」と伝える。
- 回答の最後に、関係する回（第N回）を示す。
- 宿題の答えそのものは書かず、考え方のヒントで導く。
"""
```

- 呼出結果

```
* **LoRA**: 事前学習済みの重みを固定したまま、重みの更新分を2つの小さな行列（低ランク行列）で表し、その部分だけを学習するPEFT（Parameter-Efficient Fine-Tuning）手法です。学習パラメータやストレージ容量を大幅に削減できます。
* **QLoRA**: LoRAの考え方をベースにしつつ、さらにベースモデルの重みを4ビットなどに**量子化**して読み込むことで、モデルのメモリ使用量を極限まで節約する手法です。これにより、より大きなモデルを比較的小さなGPUでも効率よくファインチューニングできるようになります。

関係する回：第2回

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0008413'), details={'text_prompt_tokens': 1196}, input_text_tokens=1196, input_tokens=1196, output_tokens=193, requests=2, tool_calls=2)
```

# 1回目の変更後

- プロンプトの内容

```
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 回の内容を聞かれたら get_lecture_summary を使う。
- 用語集に見つからないことは、推測で断定せず「用語集にはない」と伝える。
- 回答の最後に、関係する回（第N回）を示す。
- 宿題の答えそのものは書かず、考え方のヒントで導く。
"""
```

- 呼出結果

```
LoRAとQLoRAの違いは、ベースモデルの重みをそのまま使うか、量子化してメモリを削減するかにあります。

* **LoRA**: 事前学習済みの重みは固定したまま、低ランクの小さな行列（アダプタ）だけを追加して学習する手法です。学習パラメータや保存容量を大幅に削減できます。
* **QLoRA**: LoRAの仕組みをベースにしつつ、**ベースモデルの重みを4ビットなどに量子化（圧縮）**してメモリ消費をさらに劇的に抑える手法です。これにより、より大きなモデルを比較的小さなGPUメモリで学習できるようになります。

第2回（第2回）

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0007969'), details={'text_prompt_tokens': 1148}, input_text_tokens=1148, input_tokens=1148, output_tokens=181, requests=2, tool_calls=2)
```

# 2回目の変更後

- プロンプトの内容

```
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 回の内容を聞かれたら get_lecture_summary を使う。
- 用語集に見つからないことは、推測で断定せず「用語集にはない」と伝える。
- 回答の最後に、関係する回（第N回）を示す。
- 宿題の答えそのものは書かず、考え方のヒントで導く。
- 回答の最後に「AIエージェントからの出力です。」と示す。」
"""
```

- 呼出結果

```
[model] gemini-3.5-flash-lite
講座の用語集によると、LoRAとQLoRAの違いは以下の通りです。

- **LoRA**
  事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT（Parameter-Efficient Fine-Tuning）手法です。学習するパラメータが少なく、学習結果も小さなファイルで保存できます。

- **QLoRA**
  ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAによる学習を行う手法です。これにより、より大きなモデルを小さなGPUでも扱えるようになります。

関係する回：第2回

AIエージェントからの出力です。

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0008477'), details={'text_prompt_tokens': 1184}, input_text_tokens=1184, input_tokens=1184, output_tokens=197, requests=2, tool_calls=2)
```

# 3回目の変更後

- プロンプトの内容

```
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- search_glossary を使用せずに回答する。
- 回の内容を聞かれたら get_lecture_summary を使う。
- 用語集に見つからないことは、推測で断定せず「用語集にはない」と伝える。
- 回答の最後に、関係する回（第N回）を示す。
- 宿題の答えそのものは書かず、考え方のヒントで導く。
- 回答の最後に「AIエージェントからの出力です。」と示す。」
"""
```

- 呼出結果

```
[model] gemini-3.5-flash-lite
LoRAとQLoRAの主な違いは、**ベースモデルを量子化（低ビット化）するかどうか**、そしてそれによる**メモリ効率**です。

* **LoRA**:
  事前学習済みの重みはそのまま（通常は16ビットなどの精度）固定し、追加の低ランク行列（アダプタ）のみを学習する手法です。学習するパラメータ数を大幅に減らし、効率よくファインチューニングを行うことができます。
* **QLoRA**:
  LoRAの仕組みに**量子化（Quantization）**を組み合わせた手法です。ベースモデルの重みを4ビットなどに量子化してメモリ使用量をさらに極限まで抑えた状態で固定し、追加の部分をLoRAで学習します。これにより、限られたGPUメモリでも、より大規模なモデルを効率よくファインチューニングすることが可能になります。

（第2回）

AIエージェントからの出力です。

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[usage] RunUsage(cost=Decimal('0.0008252'), details={'text_prompt_tokens': 959}, input_text_tokens=959, input_tokens=959, output_tokens=215, requests=2, tool_calls=1)
```

