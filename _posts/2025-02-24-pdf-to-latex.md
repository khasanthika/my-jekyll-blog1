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

# Convert PDF to LaTeX

Check out my Flask app that converts PDFs to LaTeX: [PDF to LaTeX App](https://pdkhasanthika.pythonanywhere.com)


