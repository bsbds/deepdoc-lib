# Deepdoc

### Installations

``` bassh
pip install deepdoc-lib
```

### Model Artifact Configuration

DeepDoc resolves runtime artifacts from either a local model directory or ModelScope.

```bash
# provider: auto | local | modelscope
export DEEPDOC_MODEL_PROVIDER=auto

# shared model cache root (default: ~/.cache/deepdoc)
export DEEPDOC_MODEL_HOME=/path/to/deepdoc-models

# optional bundle-specific local directories
export DEEPDOC_VISION_MODEL_DIR=/path/to/vision
export DEEPDOC_XGB_MODEL_DIR=/path/to/xgb

# single combined ModelScope repo (all bundles in one repo)
# (default: Xorbits/deepdoc)
export DEEPDOC_MODELSCOPE_REPO=Xorbits/deepdoc
# optional shared revision (default: master)
export DEEPDOC_MODELSCOPE_REVISION=master

# offline mode for tokenizer NLTK auto-download
export DEEPDOC_OFFLINE=0

# optional NLTK data controls for tokenizer
export DEEPDOC_NLTK_DATA_DIR=/path/to/nltk_data
```

### Parser Usage

```python
from deepdoc import (
    DocxParser,
    ExcelParser,
    HtmlParser,
    PdfModelConfig,
    PdfParser,
    TokenizerConfig,
)

# Build explicit configs (no hidden kwargs/env wiring in parser constructors)
tokenizer_cfg = TokenizerConfig(
    dict_path="/path/to/models/tokenizer/huqie.txt",
    offline=True,
    nltk_data_dir="/path/to/nltk_data",
)
pdf_model_cfg = PdfModelConfig(
    vision_model_dir="/path/to/models/vision",
    xgb_model_dir="/path/to/models/xgb",
    model_provider="local",
)


# Parse PDF
pdf_parser = PdfParser(model_cfg=pdf_model_cfg, tokenizer_cfg=tokenizer_cfg)
result = pdf_parser("document.pdf")

# Parse DOCX / HTML (tokenizer only)
docx_parser = DocxParser(tokenizer_cfg=tokenizer_cfg)
html_parser = HtmlParser(tokenizer_cfg=tokenizer_cfg)

# Parse Excel (no model/tokenizer dependency)
excel_parser = ExcelParser()
with open("data.xlsx", "rb") as f:
    result = excel_parser(f.read())
```

Or use explicit env factories:

```python
tokenizer_cfg = TokenizerConfig.from_env()
pdf_model_cfg = PdfModelConfig.from_env()
pdf_parser = PdfParser(model_cfg=pdf_model_cfg, tokenizer_cfg=tokenizer_cfg)
```


### Vision Model Usage

``` python
from deepdoc import create_vision_model
```

- Use Environment Variable

```bash
# 视觉模型配置
export DEEPDOC_VISION_PROVIDER="qwen"
export DEEPDOC_VISION_API_KEY="your-api-key"
export DEEPDOC_VISION_MODEL="qwen-vl-max"
export DEEPDOC_VISION_LANG="Chinese"
export DEEPDOC_VISION_BASE_URL="http://your_base_url"

# 其他配置
export DEEPDOC_LIGHTEN=0  # 是否使用轻量模式
```

``` python
vision_model = create_vision_model()
```

- Use Default Provider

``` bash
export DEEPDOC_VISION_API_KEY="your-api-key"
```

``` python
vision_model = create_vision_model("qwen")
```

Supported providers: ["openai", "qwen", "zhipu", "ollama", "gemini", "anthropic"]

- Use Configuration File

Create `deepdoc_config.yaml`:

```yaml
vision_model:
  provider: "qwen"
  model_name: "qwen-vl-max"
  api_key: "your-api-key"
  lang: "Chinese"
  base_url : "http://your-base-url"
```

``` python
vision_model = create_vision_model("/path/to/deepdoc_config.yaml")
```

#### Run
``` python
with open("image.jpg", "rb") as f:
    result = vision_model.describe_with_prompt(f.read())
```
