# NER

Internal tool for processing PDFs and extracting named entities using SpaCy.

## Core Components

### Data Models
The system uses two main data classes (from `data_models.py`):

1. **ExtractedEntity**
   - Stores individual entities found in text
   - Fields:
     - `text`: The actual entity text
     - `label`: Entity type/label
     - `start_char`: Starting position
     - `end_char`: Ending position

2. **ProcessedDocument** 
   - Stores complete document processing results
   - Fields:
     - `filename`: PDF name
     - `timestamp`: Processing time
     - `raw_text`: Original PDF text
     - `preprocessed_text`: Cleaned text
     - `entities`: List of ExtractedEntity
     - `metadata`: Additional info
     - `error`: Any processing errors

### PDF Processor
Main processing class (from `pdf_processor.py`) handles:

1. **PDF Text Extraction**
   ```python:src/pdf_processor.py
   startLine: 140
   endLine: 171
   ```
   - Uses PyMuPDF to extract text
   - Handles multi-page documents
   - Basic text preprocessing
   - Extracts metadata (account numbers, names)

2. **Entity Processing**
   ```python:src/pdf_processor.py
   startLine: 70
   endLine: 85
   ```
   - Uses SpaCy for NER
   - Creates structured entity objects
   - Maintains original text positions

## Setup

1. Create and activate virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
```

2. Install dependencies:
```text:requirements.txt
PyMuPDF>=1.23.7    # PDF processing
spacy>=3.7.2       # NER processing
python-dateutil>=2.8.2
pathlib>=1.0.1
typing>=3.7.4.3
pandas>=2.1.0      # Optional: Excel export
openpyxl>=3.1.2    # Optional: Excel export
```

3. Install requirements:
```bash
pip install -r requirements.txt
```

4. Download SpaCy model:
```bash
python -m spacy download en_core_web_sm
```

## Usage Examples

### Basic Processing
```python
from src.pdf_processor import PDFProcessor

# Initialize with model
processor = PDFProcessor(model_path="output_ner_model")

# Process single PDF
result = processor.process_single_pdf("input/document.pdf")

# Get entities
for entity in result.entities:
    print(f"Found {entity.label}: {entity.text}")
```

### Batch Processing
```python
# Process folder of PDFs
results = processor.process_multiple_pdfs("input/pdfs/")

# Export results
processor.export_results("output/results.json")

# Check processing summary
summary = processor.get_summary()
print(f"Processed {summary['total_documents']} documents")
```

## Key Features Explained

### Error Handling
- Graceful handling of PDF reading errors
- Document-level error tracking
- Processing continues even if single document fails

### Export Options
```python:src/pdf_processor.py
startLine: 87
endLine: 94
```
- JSON export with full details
- Excel export support
- Maintains all metadata

### Metadata Extraction
- Configurable metadata extraction
- Default fields:
  - Account numbers
  - Person names
  - File paths
  - Timestamps

## Project Structure
```
src/
├── data_models.py    - Entity and document structures
├── pdf_processor.py  - Core processing logic
└── utils.py         - Helper functions (WIP)

input/
└── pdfs/            - Place PDFs here

output/
└── results/         - Processing results go here

models/
└── output_ner_model/ - SpaCy model location
```

## Common Issues & Solutions

1. **PDF Reading Errors**
   - Check PDF is not password protected
   - Verify PDF is not corrupted
   - Ensure PyMuPDF can access file

2. **Model Loading Issues**
   - Verify model path is correct
   - Check SpaCy model is downloaded
   - Ensure enough memory for model

3. **Processing Errors**
   - Check input text encoding
   - Verify PDF text extraction
   - Monitor memory usage for large files
