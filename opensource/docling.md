# docling

- **저장소:** https://github.com/docling-project/docling
- **공식 문서:** https://docling-project.github.io/docling/
- **PyPI:** https://pypi.org/project/docling/
- **라이선스:** MIT (코드에만 해당, 내부에서 쓰는 AI 모델은 각 모델의 라이선스를 따름)
- **언어:** Python (3.10 이상)
- **한 줄 설명:** PDF·Word·PPT·이미지 등 여러 문서를 AI가 읽기 좋은 마크다운·JSON으로 바꿔 주는 문서 변환기 (IBM Research 시작, LF AI & Data 재단 프로젝트)

[← 목록으로](../README.md)

## 무엇인가

일반 PDF 추출 도구는 글자만 뽑아서 표가 깨지고 코드 블록과 본문이 섞입니다. docling은 페이지 레이아웃을 분석해서 **읽는 순서, 표 구조, 코드, 수식**을 살린 채로 변환합니다.

- **지원 형식:** PDF, DOCX, PPTX, XLSX, HTML, EPUB, 이미지(PNG·JPEG·TIFF), LaTeX, 이메일(EML·MSG), 오디오(WAV·MP3), 동영상 등
- **출력 형식:** Markdown, HTML, JSON(정보 손실 없음), DocTags 등
- **OCR:** 스캔한 PDF와 이미지에서도 글자를 읽음
- **로컬 실행:** 인터넷 없이 내 PC에서 돌아가서 민감한 문서에도 쓸 수 있음
- **연동:** LangChain, LlamaIndex, Crew AI, Haystack, MCP 서버

## 사용법

### 설치

```bash
pip install docling
# pip 명령이 안 되는 Windows PC에서는
python -m pip install docling
```

PyTorch 등 큰 라이브러리를 함께 설치하므로 용량이 수 GB에 이를 수 있습니다. 처음 변환할 때 AI 모델을 내려받느라 시간이 더 걸립니다.

> ⚠️ **Windows 11 스마트 앱 컨트롤:** 이 기능이 켜져 있으면 docling이 쓰는 `pandas`의 DLL이 차단되어 `DLL load failed ... 애플리케이션 제어 정책에서 이 파일을 차단했습니다` 오류가 납니다. Windows 보안 → 앱 및 브라우저 컨트롤 → 스마트 앱 컨트롤에서 끄면 해결됩니다. 단, 한 번 끄면 Windows를 초기화하지 않는 한 다시 켤 수 없습니다.

### 명령줄에서 변환

```bash
# PDF → 현재 폴더에 .md 파일 생성
docling my-book.pdf

# 인터넷 주소도 가능
docling https://arxiv.org/pdf/2206.01062

# 시각 언어 모델(GraniteDocling)로 더 정밀하게
docling --pipeline vlm --vlm-model granite_docling my-book.pdf
```

### 파이썬에서 변환

```python
from docling.document_converter import DocumentConverter

converter = DocumentConverter()
result = converter.convert("my-book.pdf")  # 파일 경로 또는 URL
print(result.document.export_to_markdown())
```

## 활용법

- **book-to-skill과 함께:** [book-to-skill](book-to-skill.md)로 코드·표·수식이 많은 기술서를 변환할 때 docling이 설치되어 있으면 표와 코드 블록이 살아 있는 스킬이 만들어집니다(기술서 모드). 글 위주의 책이라면 docling 없이 기본 도구(`pypdf`, `pdftotext`)가 더 빠릅니다.
- **AI에게 문서 넘기기 전 정리:** PDF를 마크다운으로 바꿔서 Claude 같은 AI에게 주면 표·목차를 더 정확하게 이해합니다.
- **RAG(문서 검색 AI) 만들기:** LangChain·LlamaIndex 연동으로 사내 문서를 검색 가능한 지식 베이스로 만듭니다.
- **스캔 문서 디지털화:** OCR로 스캔한 PDF나 사진 속 글자를 텍스트로 바꿉니다.
- **주의:** 페이지당 약 1.5초가 걸릴 만큼 기본 도구보다 느립니다. 수백 쪽짜리 책은 시간이 꽤 걸립니다.
