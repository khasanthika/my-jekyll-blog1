---
layout: post
title: "Convert PDF to LaTeX Using Python: A Complete Guide"
date: 2025-02-24
categories: [Python, PDF, LaTeX, Flask]
---

In this post, I describe how I built a Python/Flask application that lets you upload a PDF file and automatically converts its text into a LaTeX document.

### How It Works

The Flask app uses the [PyPDF2](https://pypi.org/project/PyPDF2/) library to extract text from the uploaded PDF. This text is then embedded in a simple LaTeX template. You can see the complete code below.

### The Python Code

#### `app.py`
```
from flask import Flask, request, render_template
import PyPDF2

app = Flask(__name__)

def pdf_to_latex(pdf_file):
    reader = PyPDF2.PdfReader(pdf_file)
    extracted_text = ""
    for page in reader.pages:
        text = page.extract_text()
        if text:
            extracted_text += text + "\n\n"
    
    latex_template = r"""\documentclass{article}
\begin{document}
%s
\end{document}""" % extracted_text
    
    return latex_template

@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        if 'pdf_file' not in request.files:
            return "No file provided", 400
        file = request.files['pdf_file']
        if file.filename == '':
           return "No file selected", 400

        latex_content = pdf_to_latex(file)
        return render_template('result.html', latex_content=latex_content)
    
    return render_template('upload.html')
    if __name__ == '__main__':
    app.run(debug=True)   
```

### Convert PDF to LaTeX

Check out my Flask app that converts PDFs to LaTeX: [PDF to LaTeX App](https://pdf-to-latex-app.onrender.com/)

## **Adding Support for Tables, Images, and Mathematical Symbols**  

Converting a PDF into LaTeX is an essential task for researchers, students, and professionals who need to edit, format, or republish documents. While a basic PDF-to-LaTeX converter extracts plain text, real-world documents often contain complex elements like **mathematical symbols, tables, graphs, and images**. To address these challenges, I have enhanced the original Flask-based converter to handle **structured content** more effectively.

### **Why This Improved App?**
The first version of the application successfully extracted plain text from a PDF and formatted it in a LaTeX document. However, many academic and scientific papers contain:
- **Mathematical Formulas**: Standard text extraction often loses mathematical symbols, making documents incomplete.
- **Tables**: Many documents include tables with structured data, which should be converted into proper LaTeX `tabular` format.
- **Images & Graphs**: Figures, charts, and scanned equations need to be preserved to maintain document integrity.

This new version of the **PDF to LaTeX Converter** is designed to **preserve document structure** by:
1. **Extracting and formatting mathematical symbols** correctly.
2. **Converting tables into LaTeX `tabular` format** for structured presentation.
3. **Detecting and extracting images** to include them in the LaTeX output.

### **How It Works**
- The app switched from using PyPDF2 (which extracts plain text) to using [pdfplumber](https://github.com/jsvine/pdfplumber), which can extract text, tables, and even images. Then I added helper functions that try to format tables and images into LaTeX. Mathematical symbols and formulas that are text-based will be preserved (if the PDF was generated digitally rather than scanned). For scanned images of formulas, you’d need OCR specialized for math (for example, via an external API), which is beyond this basic example.

### **Benefits of This Approach**
✅ **More Accurate Document Conversion**: Retains formulas, tables, and images rather than just extracting raw text.  
✅ **Scientific & Academic Use**: Ideal for research papers, technical documents, and lecture notes.  
✅ **Easy LaTeX Integration**: Users get a fully formatted LaTeX document without manually reconstructing elements.  

This enhancement ensures that converted documents remain **as close as possible to the original format**, making them easy to edit and repurpose in LaTeX.

Check out  version 2 of my Flask app that converts PDFs to LaTeX: [PDF to LaTeX App V2](https://pdkhasanthika.pythonanywhere.com/)

---

### Final Notes

- **Limitations:**  
  This approach will work reasonably well for digitally generated PDFs where text (including math symbols) and structured tables are preserved. However, for scanned documents or very complex layouts, further processing (like OCR or manual adjustments) might be necessary.
  
- **Customization:**  
  Depending on your needs, you might consider further customizing the LaTeX output (adding more packages, formatting options, etc.) to better match the source PDF’s style.



