# <a href="https://thepi.pe/"><img src="https://rpnutzemutbrumczwvue.supabase.co/storage/v1/object/public/assets/pipeline_small%20(1).png" alt="Pipeline Illustration" style="width:96px; height:72px; vertical-align:middle;"> The Pipe</a>
[![codecov](https://codecov.io/gh/emcf/thepipe/graph/badge.svg?token=OE7CUEFUL9)](https://codecov.io/gh/emcf/thepipe) ![python-gh-action](https://github.com/emcf/thepipe/actions/workflows/python-ci.yml/badge.svg)

The pipe is a multimodal-first tool for feeding real-world information into large language models. It is built on top of dozens of carefully-crafted heuristics to create sensible text and image prompts from files, directories, web pages, papers, github repos, etc. 

![Demo](https://ngrdaaykhfrmtpodlakn.supabase.co/storage/v1/object/public/assets/demo.gif?t=2024-03-24T19%3A13%3A46.695Z)

## Features 🌟

- Prepare prompts from dozens of complex file types 📄 
- Visual document extraction for complex PDFs, markdown, etc 🧠
- Outputs optimized for multimodal LLMs 🖼️ + 💬
- Multi-threaded ⚡️
- Works with missing file extensions, in-memory data streams 💾
- Works with directories, URL, git repos, and more 🌐

To use the pipe with Python, simply append the output to the start of your prompt:

```python
import openai
import thepipe
openai_client = openai.OpenAI()
response = openai_client.chat.completions.create(
    model="gpt-4-vision-preview",
    messages = thepipe.make_prompt_from_source("https://github.com/emcf/thepipe"),
)
```

##  How it works 🛠️

The pipe is accessible from the command line or from [Python](https://www.python.org/downloads/). The input source is either a file path, a URL, or a directory (or zip file) path. The pipe will extract information from the source and process it for downstream use with [language models](https://en.wikipedia.org/wiki/Large_language_model), [vision transformers](https://en.wikipedia.org/wiki/Vision_transformer), or [vision-language models](https://arxiv.org/abs/2304.00685). The output from the pipe is a sensible text-based (or multimodal) representation of the extracted information, carefully crafted to fit within context windows for any models from [gemma-7b](https://huggingface.co/google/gemma-7b) to [GPT-4](https://openai.com/gpt-4). It uses a variety of heuristics for optimal performance with vision-language models, including AI filetype detection with [filetype detection](https://opensource.googleblog.com/2024/02/magika-ai-powered-fast-and-efficient-file-type-identification.html), AI [PDF extraction](https://mathpix.com), efficient [token compression](https://arxiv.org/abs/2403.12968), automatic [image encoding](https://en.wikipedia.org/wiki/Base64), [reranking](https://arxiv.org/abs/2310.06839) for [lost-in-the-middle](https://arxiv.org/abs/2307.03172) effects, and more, all pre-built to work out-of-the-box.

## Getting Started 🚀

To use The Pipe, clone the repository and install the requirements:
```bash
git clone https://github.com/emcf/thepipe
pip install -r requirements.txt
npm install
npx playwright install --with-deps
```

Linux users can install ctags with
```bash
sudo apt-get install -y universal-ctags
```

Windows users must ensure [ctags.exe](https://github.com/universal-ctags/) is in their PATH environment variable.


To use The Pipe from the command line, simply run

```bash
python thepipe.py path/to/directory
```

This command will process all supported files within the specified directory, compressing any information over the token limit if necessary, and outputting the resulting prompt and images to a folder.

Arguments are:
- The input source (required): can be a file path, a URL, or a directory path.
- `--match` (optional): Regex pattern to match files in the directory.
- `--ignore` (optional): Regex pattern to ignore files in the directory.
- `--limit` (optional): The token limit for the output prompt, defaults to 100K. Prompts exceeding the limit will be compressed.
- `--mathpix` (optional): Extract images, tables, and math from PDFs using [Mathpix](https://docs.mathpix.com/#process-a-pdf).
- `--text_only` (optional): Do not extract images from documents or websites. Additionally, image files will be represented with OCR instead of as images.

You can use the pipe's output with other LLM providers via [LiteLLM](https://github.com/BerriAI/litellm).

## Supported File Types 📚

| Source Type                           | Input types        | Token Compression 🗜️ | Image Extraction 👁️ | Notes 📌                                                  |
|---------------------------------------|------------------------------------------|-------------------|------------------|---------------------------------------------------------|
| Directory                             | Any `/path/to/directory`                 | ✔️               | ✔️               | Extracts from all files in directory, supports match and ignore patterns |
| Code                                  | `.py`, `.tsx`, `.js`, `.html`, `.css`, `.cpp`, etc | ✔️ (varies)   | ❌               | Combines all code files. `.c`, `.cpp`, `.py` are compressible with ctags, others are not |
| Plaintext                             | `.txt`, `.md`, `.rtf`, etc               | ✔️               | ❌               | Regular text files                                                      |
| PDF                                   | `.pdf`                                  | ✔️               | ✔️    | Extracts text and optionally images; can use Mathpix for enhanced extraction |
| Image                                 | `.jpg`, `.jpeg`, `.png`, `.gif`, `.bmp`, `.tiff`, `.webp`, `.svg` | ❌                | ✔️              | Extracts images and can convert to text using OCR                        |
| Data Table                           | `.csv`, `.xls`, `.xlsx`, `supabase`             | ✔️                | ❌               | Extracts data from spreadsheets or SQL tables; converts to text representation. For very large datasets, will only extract column names and types         |
| Jupyter Notebook                      | `.ipynb`                                | ❌               | ❌               | Extracts content from Jupyter notebooks                                  |
| Microsoft Word Document               | `.docx`                                 | ✔️               | ✔️               | Extracts text from Word documents                                        |
| Microsoft PowerPoint Presentation     | `.pptx`                                 | ✔️               | ✔️               | Extracts text from PowerPoint presentations                              |
| Website                               | URLs (http, https, www, ftp)             | ✔️                | ✔️    | Extracts content from web pages; text-only extraction available          |
| GitHub Repository                     | GitHub repo URLs                         | ✔️               | ✔️                | Extracts from GitHub repositories; supports branch specification         |
| ZIP File                              | `.zip`                                  | ✔️               | ✔️                | Extracts contents of ZIP files; supports nested directory extraction     |