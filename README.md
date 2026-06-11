# data_extraction_from_PDF
This project demostrates how to extract data from a pdf file. The script is an automation of data extraction from a folder then producing an excel file as an output from all the pdfs.

# PDF Data Extraction Project

## Overview

This Python script automates the extraction of structured data from TMV (Thermostatic Mixing Valve) inspection report PDFs. It processes multiple PDF files in a folder, extracts metadata, summary tables, and executive summaries, then combines them into a consolidated Excel workbook with three sheets.

## Features

- **Batch Processing**: Processes all PDF files in a specified folder
- **Multi-section Extraction**: Extracts three distinct sections from each PDF:
  - Metadata (site information, references, visit dates)
  - Summary Tables (detailed asset inspection data)
  - Executive Summary (issues and severity ratings)
- **Data Cleaning**: Automatically cleans and standardizes extracted data
- **Consolidated Output**: Combines data from all PDFs into a single Excel file with three sheets

## Requirements

### Python Version
- Python 3.7+

### Dependencies

```bash
pip install pandas PyPDF2 pdfplumber openpyxl
```

**Package Descriptions:**
- `pandas`: Data manipulation and analysis
- `PyPDF2`: PDF text extraction
- `pdfplumber`: Advanced PDF table extraction
- `openpyxl`: Excel file writing (required by pandas)

## Installation

1. Install required packages:
```bash
pip install -r requirements.txt
```

3. Create a `requirements.txt` file with:
```
pandas>=1.3.0
PyPDF2>=3.0.0
pdfplumber>=0.9.0
openpyxl>=3.0.0
```

## Usage

### Basic Usage

1. **Set up your folder path**: Update the `folder_path` variable in the script to point to your PDF directory:

```python
folder_path = "C:\\Users\\Administrator\\Desktop\\analysis\\Test folder 2"
```

2. **Run the script**:
```bash
python pdf_extraction.py
```

3. **Check output**: The script generates `Final_output_ALL_PDFS.xlsx` in the same directory

### Output Structure

The output Excel file contains three sheets:

#### 1. Metadata Sheet
Contains basic information extracted from the first page:
- Site_Name
- Report_Ref
- Client_Ref
- Visit_Date
- Scope_of_Work
- Notes
- pdf_source

#### 2. Summary_data Sheet
Contains detailed asset inspection records:
- Site and visit information
- Asset identification (Asset_Name, Floor, Ward/Department, Room_ID, Room_Name, Fixture)
- Supply specifications (HWS_Supply_Size, CWS_Supply_Size)
- Cleaning records (Strainers_Cleaned, Before_Clean, After_Clean)
- Temperature readings (Return_Temp, Cold_Temp_to_TMV, Hot_Temp_to_TMV)
- Blended temperatures for different fixture types
- Isolation valve status
- Test results and photos

#### 3. Executive_data Sheet
Contains issue tracking and severity ratings:
- Asset_Name
- Visit_Dates
- Report and client references
- Floor, Ward/Department, Room details
- Severity (High/Medium/Low)
- Issue_Summary
- pdf_source

## How It Works

### 1. Metadata Extraction
Uses **regex patterns** to extract structured information from the first page:
- Searches for specific keywords (SITE, OUR REFERENCE, YOUR REFERENCE, etc.)
- Cleans and formats the extracted text
- Handles multi-line entries

### 2. Summary Table Extraction
Uses **pdfplumber** to extract multi-page tables:
- Locates "SUMMARY TABLE" section
- Extracts tables across multiple pages
- Stops when "LOGBOOK" keyword is found
- Cleans column names and data
- Splits location strings into hierarchical fields (Floor/Ward/Room/Fixture)
- Removes units (°C, mm) from numeric fields

### 3. Executive Summary Extraction
Extracts two types of tables:
- **3-column tables**: Location, Issue Summary, Severity
- **2-column tables** (Works not completable): Location, Issue Summary
- Combines both types and merges with summary data using Asset_Name

### 4. Data Merging
- Merges executive summary with detailed summary data
- Preserves all records (left join)
- Adds source tracking (pdf_source column)
- Combines data from all PDFs into final output

## Data Cleaning Applied

- **Location parsing**: Splits hierarchical location strings (Floor/Ward/Room/Fixture)
- **Unit removal**: Removes °C and mm from temperature and size fields
- **Text normalization**: Removes newlines, tabs, and extra whitespace
- **Room identification**: Separates Room_ID from Room_Name
- **Fixture extraction**: Extracts fixture type from location string
- **Forward filling**: Propagates location values for grouped issues

## Error Handling

The script includes robust error handling:
- Try-catch blocks for each PDF processing
- Continues processing remaining files if one fails
- Prints detailed error messages with traceback
- Creates empty dataframes if sections are not found

## Customization

### Modifying Regex Patterns

To extract different fields from the first page, update the `regex_patterns` dictionary:

```python
regex_patterns = {
    'New Field': r"PATTERN\s*(.*?)\s*NEXT_PATTERN",
}
```

### Changing Column Mappings

Update the `rename` dictionaries to match your PDF structure:

```python
df.rename(columns={
    "Original Name": "New_Name",
}, inplace=True)
```

### Adjusting Keywords

Modify the keyword searches if your PDFs use different section headers:

```python
if "YOUR_KEYWORD" in text:
    # extraction logic
```

## Troubleshooting

### Common Issues

**Issue**: No tables extracted
- **Solution**: Check if PDF contains actual tables (not images of tables)
- Verify section keywords match your PDFs exactly

**Issue**: Columns mismatch
- **Solution**: PDFs may have varying table structures
- Update column handling logic in the script

**Issue**: Regex not matching
- **Solution**: Print `first_page_text` to see actual text structure
- Adjust regex patterns accordingly

**Issue**: Memory errors with large PDFs
- **Solution**: Process PDFs in smaller batches
- Clear dataframes after each batch

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request with clear descriptions

## License

This project is licensed under the MIT License - see LICENSE file for details.

## Acknowledgments

- Built with Python's powerful PDF and data manipulation libraries
- Designed for healthcare facility inspection report processing

## Contact

For questions or support, please open an issue on GitHub or contact [clifotieno97@gmail.com]

---

**Note**: This script is designed for TMV inspection reports with specific formatting. Adaptation may be needed for different PDF structures.
