# All the HTML to PDF Open-Source Libraries

A single, maintained reference for choosing an **open-source HTML to PDF conversion library**.

Every tool listed here is open source and linked to its repository. Star and activity badges are live (via shields.io), so the numbers you see are current. Editorial notes (CSS support, paged-media support, status) were last reviewed **July 2026**.

> Proprietary tools (Prince, IronPDF, SelectPdf, DocRaptor, PDFShift, api2pdf, …) are deliberately **not** listed. This comparison is open source only.

## Contents

- [TL;DR which one should I use?](#tldr--which-one-should-i-use)
- [How to read this comparison](#how-to-read-this-comparison)
- [The big comparison table](#the-big-comparison-table)
- [By language](#by-language)
  - [JavaScript / TypeScript](#javascript--typescript) · [Python](#python) · [PHP](#php) · [Java / JVM](#java--jvm) · [C# / .NET](#c--net) · [Ruby](#ruby) · [Go](#go) · [Rust](#rust) · [Any language](#any-language-clis--self-hosted-services)
- [Choosing guide](#choosing-guide)
  - [Browser engine vs. own engine](#browser-engine-vs-own-engine--the-core-trade-off)
  - [Paged media: headers, footers, page numbers](#paged-media-headers-footers-page-numbers)
  - [Licensing gotchas](#licensing-gotchas)
  - [Security notes](#security-notes)
  - [Performance & deployment](#performance--deployment)
  - [The wkhtmltopdf problem](#the-wkhtmltopdf-problem)
- [Deprecated, do not use for new projects](#deprecated--do-not-use-for-new-projects)
- [Not quite HTML→PDF: programmatic & template-based alternatives](#not-quite-htmlpdf-programmatic--template-based-alternatives)
- [Contributing](#contributing)

---

## TL;DR which one should I use?

- **You need pixel-perfect rendering of modern HTML/CSS/JS** (charts, web fonts, flexbox/grid, SPA output) → use a **headless-browser tool**: [Puppeteer](https://github.com/puppeteer/puppeteer) or [Playwright](https://github.com/microsoft/playwright) (Node), [Playwright for Python](https://github.com/microsoft/playwright-python), [Browsershot](https://github.com/spatie/browsershot) (PHP), [PuppeteerSharp](https://github.com/hardkoded/puppeteer-sharp) (.NET), [Grover](https://github.com/Studiosity/grover) or [ferrum_pdf](https://github.com/excid3/ferrum_pdf) (Ruby), [chromedp](https://github.com/chromedp/chromedp) or [go-rod](https://github.com/go-rod/rod) (Go), [chromiumoxide](https://github.com/mattsse/chromiumoxide) (Rust).
- **You need print-grade documents?** use a **dedicated paged-media engine**: [WeasyPrint](https://github.com/Kozea/WeasyPrint) (Python, the gold standard), [Paged.js](https://github.com/pagedjs/pagedjs-cli) or [Vivliostyle CLI](https://github.com/vivliostyle/vivliostyle-cli) (Node), [OpenHTMLtoPDF](https://github.com/openhtmltopdf/openhtmltopdf) (Java).
- **You can't (or don't want to) ship a browser binary** → use a **pure-language engine**: [dompdf](https://github.com/dompdf/dompdf) or [mPDF](https://github.com/mpdf/mpdf) (PHP), [WeasyPrint](https://github.com/Kozea/WeasyPrint) or [xhtml2pdf](https://github.com/xhtml2pdf/xhtml2pdf) (Python), [OpenHTMLtoPDF](https://github.com/openhtmltopdf/openhtmltopdf) (Java).
- **You want one PDF service for a polyglot stack / microservices** → [Gotenberg](https://github.com/gotenberg/gotenberg) (Docker, REST API, Chromium + LibreOffice inside).
- **You need to generate the PDF in the user's browser** → [html2pdf.js](https://github.com/eKoopmans/html2pdf.js) (also read the [rasterization caveat](#javascript--typescript) )
- **You're still on [wkhtmltopdf](https://github.com/wkhtmltopdf/wkhtmltopdf) or anything wrapping it** → it was archived in 2023 with unpatched CVEs. See [migration paths](#the-wkhtmltopdf-problem).

---

## How to read this comparison

### The four approaches

Every open-source HTML→PDF tool takes one of four approaches, and most of a tool's strengths and weaknesses follow directly from which one it uses:

| Approach | How it works | Fidelity | Footprint | Paged media |
|---|---|---|---|---|
| 🌐 **Headless browser** | Drives real Chromium/Firefox/WebKit, calls its print-to-PDF | Identical to Chrome | Heavy (~300 MB+, ~0.5–2 s startup) | Basic |
| 🧩 **Own engine** | Implements its own HTML/CSS layout engine that writes PDF directly | Good, but not a browser | Light (pure library) | Usually excellent |
| 📦 **Wrapper** | Shells out to an external binary or another library | Inherits the backend's | Inherits | Inherits |
| 🖼️ **Client-side** | Screenshots the live DOM to canvas, embeds images in a PDF | What the user sees as pixels | Zero server cost | Poor |

### Table columns

- **Approach / Engine:** what actually renders the HTML. The engine determines fidelity: "Chromium" means it looks exactly like Chrome; "own engine" means test your templates.
- **JS:** whether JavaScript in the page executes before conversion. Required for client-rendered charts, SPAs, anything built at runtime. Own-engine tools almost never run JS.
- **CSS:** *Modern* = flexbox, grid, custom properties, web fonts. *CSS 2.1* = tables-and-floats era; modern layouts silently break.
- **Paged:** support for CSS Paged Media: `@page` rules, margin boxes, running headers/footers, `counter(page)`. ✅ full · Partial (e.g. only a header/footer template API) · ❌ none. The same three states apply to the **JS** column, where Partial means an outdated engine (wkhtmltopdf's ~2012 QtWebKit) that fails on modern scripts.
- **License** 
- **Status** ✅ actively maintained · 🟡 low activity / maintenance mode · ⚠️ archived or abandoned

---

## The big comparison table

*Sorted by language, then by our recommendation within each language. Stars and last-commit badges are live.*

| Library | Language | Approach / Engine | JS | CSS | Paged | License | Stars | Last commit | Status |
|---|---|---|---|---|---|---|---|---|---|
| [Puppeteer](https://github.com/puppeteer/puppeteer) | JS/TS | 🌐 Chromium (or Firefox) | ✅ | Modern | Partial | Apache-2.0 | ![Stars](https://img.shields.io/github/stars/puppeteer/puppeteer?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/puppeteer/puppeteer?style=flat-square&label=) | ✅ |
| [Playwright](https://github.com/microsoft/playwright) | JS/TS | 🌐 Chromium / Firefox / WebKit | ✅ | Modern | Partial | Apache-2.0 | ![Stars](https://img.shields.io/github/stars/microsoft/playwright?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/microsoft/playwright?style=flat-square&label=) | ✅ |
| [Paged.js / pagedjs-cli](https://github.com/pagedjs/pagedjs-cli) | JS/TS | 🧩+🌐 Paged-media polyfill on Chromium | ✅ | Modern | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/pagedjs/pagedjs-cli?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/pagedjs/pagedjs-cli?style=flat-square&label=) | ✅ |
| [percollate](https://github.com/danburzo/percollate) | JS/TS | 📦 Puppeteer (article→PDF) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/danburzo/percollate?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/danburzo/percollate?style=flat-square&label=) | ✅ |
| [html2pdf.js](https://github.com/eKoopmans/html2pdf.js) | JS (browser) | 🖼️ html2canvas + jsPDF | ✅* | Rasterized | Partial | MIT | ![Stars](https://img.shields.io/github/stars/eKoopmans/html2pdf.js?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/eKoopmans/html2pdf.js?style=flat-square&label=) | ✅ |
| [jsPDF](https://github.com/parallax/jsPDF) (+ [html2canvas](https://github.com/niklasvh/html2canvas)) | JS (browser) | 🖼️ Canvas raster → PDF | ✅* | Rasterized | ❌ | MIT | ![Stars](https://img.shields.io/github/stars/parallax/jsPDF?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/parallax/jsPDF?style=flat-square&label=) | ✅ |
| [html-pdf-node](https://github.com/mrafiqk/html-pdf-node) | JS/TS | 📦 Puppeteer | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/mrafiqk/html-pdf-node?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/mrafiqk/html-pdf-node?style=flat-square&label=) | 🟡 |
| [puppeteer-html-pdf](https://github.com/ultimateakash/puppeteer-html-pdf) | JS/TS | 📦 Puppeteer | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/ultimateakash/puppeteer-html-pdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/ultimateakash/puppeteer-html-pdf?style=flat-square&label=) | 🟡 |
| [chrome-headless-render-pdf](https://github.com/Szpadel/chrome-headless-render-pdf) | JS/TS | 📦 Chrome CLI | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/Szpadel/chrome-headless-render-pdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/Szpadel/chrome-headless-render-pdf?style=flat-square&label=) | 🟡 |
| [electron-pdf](https://github.com/fraserxu/electron-pdf) | JS/TS | 📦 Electron (Chromium) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/fraserxu/electron-pdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/fraserxu/electron-pdf?style=flat-square&label=) | 🟡 |
| [node-wkhtmltopdf](https://github.com/devongovett/node-wkhtmltopdf) | JS/TS | 📦 wkhtmltopdf (QtWebKit) | Partial | CSS 2.1 | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/devongovett/node-wkhtmltopdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/devongovett/node-wkhtmltopdf?style=flat-square&label=) | ⚠️ upstream archived |
| [node-html-pdf](https://github.com/marcbachmann/node-html-pdf) | JS/TS | 📦 PhantomJS (WebKit) | ✅ | CSS 2.1 | Partial | MIT | ![Stars](https://img.shields.io/github/stars/marcbachmann/node-html-pdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/marcbachmann/node-html-pdf?style=flat-square&label=) | ⚠️ archived |
| [WeasyPrint](https://github.com/Kozea/WeasyPrint) | Python | 🧩 Own CSS engine | ❌ | Modern | ✅ | BSD-3-Clause | ![Stars](https://img.shields.io/github/stars/Kozea/WeasyPrint?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/Kozea/WeasyPrint?style=flat-square&label=) | ✅ |
| [Playwright for Python](https://github.com/microsoft/playwright-python) | Python | 🌐 Chromium / Firefox / WebKit | ✅ | Modern | Partial | Apache-2.0 | ![Stars](https://img.shields.io/github/stars/microsoft/playwright-python?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/microsoft/playwright-python?style=flat-square&label=) | ✅ |
| [xhtml2pdf](https://github.com/xhtml2pdf/xhtml2pdf) | Python | 🧩 Own engine (ReportLab) | ❌ | CSS 2.1 | Partial | Apache-2.0 | ![Stars](https://img.shields.io/github/stars/xhtml2pdf/xhtml2pdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/xhtml2pdf/xhtml2pdf?style=flat-square&label=) | ✅ |
| [django-weasyprint](https://github.com/fdemmer/django-weasyprint) | Python | 📦 WeasyPrint (Django views) | ❌ | Modern | ✅ | Apache-2.0 | ![Stars](https://img.shields.io/github/stars/fdemmer/django-weasyprint?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/fdemmer/django-weasyprint?style=flat-square&label=) | ✅ |
| [python-pdfkit](https://github.com/JazzCore/python-pdfkit) | Python | 📦 wkhtmltopdf (QtWebKit) | Partial | CSS 2.1 | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/JazzCore/python-pdfkit?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/JazzCore/python-pdfkit?style=flat-square&label=) | ⚠️ upstream archived |
| [Pyppeteer](https://github.com/pyppeteer/pyppeteer) | Python | 🌐 Chromium | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/pyppeteer/pyppeteer?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/pyppeteer/pyppeteer?style=flat-square&label=) | ⚠️ unmaintained |
| [dompdf](https://github.com/dompdf/dompdf) | PHP | 🧩 Own CSS engine | ❌ | Partial modern | ✅ | LGPL-2.1 | ![Stars](https://img.shields.io/github/stars/dompdf/dompdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/dompdf/dompdf?style=flat-square&label=) | ✅ |
| [mPDF](https://github.com/mpdf/mpdf) | PHP | 🧩 Own engine (FPDF lineage) | ❌ | CSS 2.1+ | ✅ | GPL-2.0 | ![Stars](https://img.shields.io/github/stars/mpdf/mpdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/mpdf/mpdf?style=flat-square&label=) | ✅ |
| [Browsershot](https://github.com/spatie/browsershot) | PHP | 📦 Puppeteer (Chromium) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/spatie/browsershot?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/spatie/browsershot?style=flat-square&label=) | ✅ |
| [laravel-pdf (Spatie)](https://github.com/spatie/laravel-pdf) | PHP | 📦 Multi-driver (Browsershot, …) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/spatie/laravel-pdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/spatie/laravel-pdf?style=flat-square&label=) | ✅ |
| [chrome-php](https://github.com/chrome-php/chrome) | PHP | 🌐 Chromium (DevTools protocol) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/chrome-php/chrome?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/chrome-php/chrome?style=flat-square&label=) | ✅ |
| [laravel-dompdf](https://github.com/barryvdh/laravel-dompdf) | PHP | 📦 dompdf (Laravel) | ❌ | Partial modern | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/barryvdh/laravel-dompdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/barryvdh/laravel-dompdf?style=flat-square&label=) | ✅ |
| [TCPDF](https://github.com/tecnickcom/TCPDF) | PHP | 🧩 Own writer, limited HTML | ❌ | Limited | Partial | LGPL-3.0 | ![Stars](https://img.shields.io/github/stars/tecnickcom/TCPDF?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/tecnickcom/TCPDF?style=flat-square&label=) | ✅ |
| [Snappy](https://github.com/KnpLabs/snappy) | PHP | 📦 wkhtmltopdf (QtWebKit) | Partial | CSS 2.1 | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/KnpLabs/snappy?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/KnpLabs/snappy?style=flat-square&label=) | ⚠️ upstream archived |
| [laravel-snappy](https://github.com/barryvdh/laravel-snappy) | PHP | 📦 Snappy (Laravel) | Partial | CSS 2.1 | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/barryvdh/laravel-snappy?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/barryvdh/laravel-snappy?style=flat-square&label=) | ⚠️ upstream archived |
| [phpwkhtmltopdf](https://github.com/mikehaertl/phpwkhtmltopdf) | PHP | 📦 wkhtmltopdf (QtWebKit) | Partial | CSS 2.1 | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/mikehaertl/phpwkhtmltopdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/mikehaertl/phpwkhtmltopdf?style=flat-square&label=) | ⚠️ upstream archived |
| [OpenHTMLtoPDF](https://github.com/openhtmltopdf/openhtmltopdf) | Java | 🧩 Own engine + PDFBox | ❌ | CSS 2.1+ | ✅ | LGPL-3.0 | ![Stars](https://img.shields.io/github/stars/openhtmltopdf/openhtmltopdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/openhtmltopdf/openhtmltopdf?style=flat-square&label=) | ✅ |
| [Flying Saucer](https://github.com/flyingsaucerproject/flyingsaucer) | Java | 🧩 Own CSS 2.1 engine | ❌ | CSS 2.1 | ✅ | LGPL-2.1 | ![Stars](https://img.shields.io/github/stars/flyingsaucerproject/flyingsaucer?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/flyingsaucerproject/flyingsaucer?style=flat-square&label=) | ✅ |
| [OpenPDF](https://github.com/LibrePDF/OpenPDF) (openpdf-html) | Java | 🧩 Flying Saucer fork + PDF lib | ❌ | CSS 2.1+ | ✅ | MPL-2.0 / LGPL-2.1 | ![Stars](https://img.shields.io/github/stars/LibrePDF/OpenPDF?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/LibrePDF/OpenPDF?style=flat-square&label=) | ✅ |
| [iText pdfHTML](https://github.com/itext/itext-pdfhtml-java) | Java | 🧩 iText 7 renderer | ❌ | Modern-ish | ✅ | **AGPL-3.0** (dual) | ![Stars](https://img.shields.io/github/stars/itext/itext-pdfhtml-java?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/itext/itext-pdfhtml-java?style=flat-square&label=) | ✅ |
| [PuppeteerSharp](https://github.com/hardkoded/puppeteer-sharp) | .NET | 🌐 Chromium | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/hardkoded/puppeteer-sharp?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/hardkoded/puppeteer-sharp?style=flat-square&label=) | ✅ |
| [Playwright for .NET](https://github.com/microsoft/playwright-dotnet) | .NET | 🌐 Chromium / Firefox / WebKit | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/microsoft/playwright-dotnet?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/microsoft/playwright-dotnet?style=flat-square&label=) | ✅ |
| [DinkToPdf](https://github.com/rdvojmoc/DinkToPdf) | .NET | 📦 wkhtmltopdf (QtWebKit) | Partial | CSS 2.1 | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/rdvojmoc/DinkToPdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/rdvojmoc/DinkToPdf?style=flat-square&label=) | ⚠️ abandoned |
| [Grover](https://github.com/Studiosity/grover) | Ruby | 📦 Puppeteer (via Node) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/Studiosity/grover?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/Studiosity/grover?style=flat-square&label=) | ✅ |
| [ferrum_pdf](https://github.com/excid3/ferrum_pdf) | Ruby | 🌐 Chromium (CDP, no Node) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/excid3/ferrum_pdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/excid3/ferrum_pdf?style=flat-square&label=) | ✅ |
| [wicked_pdf](https://github.com/mileszs/wicked_pdf) | Ruby | 📦 wkhtmltopdf (QtWebKit) | Partial | CSS 2.1 | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/mileszs/wicked_pdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/mileszs/wicked_pdf?style=flat-square&label=) | ⚠️ upstream archived |
| [PDFKit (Ruby)](https://github.com/pdfkit/pdfkit) | Ruby | 📦 wkhtmltopdf (QtWebKit) | Partial | CSS 2.1 | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/pdfkit/pdfkit?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/pdfkit/pdfkit?style=flat-square&label=) | ⚠️ upstream archived |
| [chromedp](https://github.com/chromedp/chromedp) | Go | 🌐 Chromium (CDP) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/chromedp/chromedp?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/chromedp/chromedp?style=flat-square&label=) | ✅ |
| [go-rod](https://github.com/go-rod/rod) | Go | 🌐 Chromium (CDP) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/go-rod/rod?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/go-rod/rod?style=flat-square&label=) | ✅ |
| [go-wkhtmltopdf](https://github.com/SebastiaanKlippert/go-wkhtmltopdf) | Go | 📦 wkhtmltopdf (QtWebKit) | Partial | CSS 2.1 | ✅ | MIT | ![Stars](https://img.shields.io/github/stars/SebastiaanKlippert/go-wkhtmltopdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/SebastiaanKlippert/go-wkhtmltopdf?style=flat-square&label=) | ⚠️ upstream archived |
| [headless_chrome](https://github.com/rust-headless-chrome/rust-headless-chrome) | Rust | 🌐 Chromium (CDP) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/rust-headless-chrome/rust-headless-chrome?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/rust-headless-chrome/rust-headless-chrome?style=flat-square&label=) | ✅ |
| [chromiumoxide](https://github.com/mattsse/chromiumoxide) | Rust | 🌐 Chromium (CDP, async) | ✅ | Modern | Partial | MIT / Apache-2.0 | ![Stars](https://img.shields.io/github/stars/mattsse/chromiumoxide?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/mattsse/chromiumoxide?style=flat-square&label=) | ✅ |
| [Gotenberg](https://github.com/gotenberg/gotenberg) | Any (REST) | ☁️ Docker API: Chromium + LibreOffice | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/gotenberg/gotenberg?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/gotenberg/gotenberg?style=flat-square&label=) | ✅ |
| [Stirling-PDF](https://github.com/Stirling-Tools/Stirling-PDF) | Any (web app) | ☁️ Self-hosted; HTML→PDF via WeasyPrint | ❌ | Modern | ✅ | MIT (open-core) | ![Stars](https://img.shields.io/github/stars/Stirling-Tools/Stirling-PDF?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/Stirling-Tools/Stirling-PDF?style=flat-square&label=) | ✅ |
| [Vivliostyle CLI](https://github.com/vivliostyle/vivliostyle-cli) | Any (CLI) | 🧩 Own paged-media engine | ❌ | Modern | ✅ | **AGPL-3.0** | ![Stars](https://img.shields.io/github/stars/vivliostyle/vivliostyle-cli?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/vivliostyle/vivliostyle-cli?style=flat-square&label=) | ✅ |
| [Chromium `--print-to-pdf`](https://github.com/chromium/chromium) | Any (CLI) | 🌐 Chromium, built-in flag | ✅ | Modern | Partial | BSD-3-Clause | - | - | ✅ |
| [LibreOffice headless](https://www.libreoffice.org/) | Any (CLI) | 📦 LibreOffice HTML import filter | ❌ | Limited | Partial | MPL-2.0 | - | - | ✅ |
| [wkhtmltopdf](https://github.com/wkhtmltopdf/wkhtmltopdf) | Any (CLI) | 🌐 QtWebKit (~2012 vintage) | Partial | CSS 2.1 | ✅ | LGPL-3.0 | ![Stars](https://img.shields.io/github/stars/wkhtmltopdf/wkhtmltopdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/wkhtmltopdf/wkhtmltopdf?style=flat-square&label=) | ⚠️ **archived 2023** |
| [Athena PDF](https://github.com/arachnys/athenapdf) | Any (Docker) | ☁️ Electron (Chromium) | ✅ | Modern | Partial | MIT | ![Stars](https://img.shields.io/github/stars/arachnys/athenapdf?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/arachnys/athenapdf?style=flat-square&label=) | ⚠️ archived 2023 |
| [PhantomJS](https://github.com/ariya/phantomjs) | Any (CLI) | 🌐 WebKit | ✅ | CSS 2.1 | Partial | BSD-3-Clause | ![Stars](https://img.shields.io/github/stars/ariya/phantomjs?style=flat-square&label=) | ![Last commit](https://img.shields.io/github/last-commit/ariya/phantomjs?style=flat-square&label=) | ⚠️ abandoned 2017 |

\* Client-side tools run in the live page, so the page's JavaScript has already executed but they capture the DOM as a **raster image**: output is not selectable, not searchable, and not accessible.

---

## By language

### JavaScript / TypeScript

**Default choice: [Puppeteer](https://github.com/puppeteer/puppeteer)** with `page.pdf()` gives you exactly what Chrome prints. [Playwright](https://github.com/microsoft/playwright) is equally good and adds Firefox/WebKit engines plus a first-class test runner. Both support header/footer templates and `@page` size, but real paged-media features (page counters in running headers, footnotes, TOC page references) are limited by Chromium's print engine.

For **print-grade output**, put [Paged.js](https://github.com/pagedjs/pagedjs-cli) on top of Chromium. It polyfills the W3C Paged Media spec (`@page` margin boxes, `counter(page)`, running headers) and then prints. [Vivliostyle CLI](https://github.com/vivliostyle/vivliostyle-cli) is the other serious paged-media option (book/magazine typesetting, but note AGPL license).

**In the browser**, [html2pdf.js](https://github.com/eKoopmans/html2pdf.js) bundles [html2canvas](https://github.com/niklasvh/html2canvas) + [jsPDF](https://github.com/parallax/jsPDF). The DOM is screenshotted to canvas, so the PDF contains *pictures of text*; no selection, no search, no accessibility, large file sizes.

Avoid the old wrappers: [node-html-pdf](https://github.com/marcbachmann/node-html-pdf) (PhantomJS-based, archived July 2026, its own README says migrate to Puppeteer) and [node-wkhtmltopdf](https://github.com/devongovett/node-wkhtmltopdf) (wkhtmltopdf is archived). Convenience wrappers like [html-pdf-node](https://github.com/mrafiqk/html-pdf-node) save little over ~10 lines of raw Puppeteer and lag behind it.

### Python

- **[WeasyPrint](https://github.com/Kozea/WeasyPrint)** is arguably the best open-source paged-media engine in any language. Own CSS engine (no browser), excellent `@page` support: margin boxes, page counters, running headers/footers, page-break control, PDF/A and PDF/UA variants. Deliberately does **not** execute JavaScript. The default for invoices, reports, contracts. For Django, [django-weasyprint](https://github.com/fdemmer/django-weasyprint) wraps it in a class-based view.
- **[Playwright for Python](https://github.com/microsoft/playwright-python)** for when the HTML needs a real browser (JS charts, modern CSS edge cases). Official Microsoft bindings, actively maintained.
- [xhtml2pdf](https://github.com/xhtml2pdf/xhtml2pdf) (ReportLab-based) is still maintained and pure-Python, but its CSS support is a generation behind WeasyPrin. 
- [python-pdfkit](https://github.com/JazzCore/python-pdfkit) depends on unmaintained wkhtmltopdf
- [Pyppeteer](https://github.com/pyppeteer/pyppeteer) is unmaintained (its own README points to Playwright/Puppeteer alternatives)

### PHP


- **[dompdf](https://github.com/dompdf/dompdf)**: the most-used PHP converter. Pure PHP, decent CSS (float-based layouts reliable; flexbox/grid support still limited), `@page` support. LGPL-2.1. There's also a Laravel wrapper: [laravel-dompdf](https://github.com/barryvdh/laravel-dompdf).
- **[mPDF](https://github.com/mpdf/mpdf)**: stronger than dompdf for complex tables, RTL/CJK text, TOC generation, headers/footers; CSS engine is older-style. GPL-2.0 (see [licensing](#licensing-gotchas)).
- **[TCPDF](https://github.com/tecnickcom/TCPDF)**: primarily a programmatic PDF writer; its HTML support is a convenience subset. Choose it for barcodes/digital signatures, not HTML fidelity.

- **[Browsershot](https://github.com/spatie/browsershot)** (by Spatie) drives Puppeteer from PHP
- [laravel-pdf](https://github.com/spatie/laravel-pdf)** (also by Spatie) adds a clean Laravel API with swappable backends
- [chrome-php](https://github.com/chrome-php/chrome) talks to Chrome directly (no Node env needed)

Then there's the wkhtmltopdf wrappers which remain maintained as code, but the binary they shell out to is [unmaintained](#the-wkhtmltopdf-problem):
 - [Snappy](https://github.com/KnpLabs/snappy)
 - [laravel-snappy](https://github.com/barryvdh/laravel-snappy)
 - [phpwkhtmltopdf](https://github.com/mikehaertl/phpwkhtmltopdf)

### Java / JVM

The JVM is own-engine territory (no browser dependency):

- **[OpenHTMLtoPDF](https://github.com/openhtmltopdf/openhtmltopdf)**: the strongest pure-Java option: CSS 2.1 plus useful extensions, excellent paged media, SVG, and first-class accessible-PDF output. Development moved from [danfickle/openhtmltopdf](https://github.com/danfickle/openhtmltopdf) (stale since 2024) to the community org.
- **[Flying Saucer](https://github.com/flyingsaucerproject/flyingsaucer)**: the original XHTML/CSS 2.1 renderer OpenHTMLtoPDF forked from; still actively maintained. Requires well-formed XHTML.
- **[OpenPDF](https://github.com/LibrePDF/OpenPDF)**: LGPL/MPL fork of the last open iText 4, now with an `openpdf-html` module (Flying Saucer lineage) for HTML rendering.
- **[iText pdfHTML](https://github.com/itext/itext-pdfhtml-java)**: commercially polished, good CSS coverage, PDF/A and tagging but it has an **AGPL-3.0** license unless you buy a commercial license. Read the [licensing section](#licensing-gotchas) before adopting.

For browser-fidelity on the JVM, use Chromium wrappers:
- [Playwright for Java](https://github.com/microsoft/playwright-java)
- [Gotenberg](https://github.com/gotenberg/gotenberg) over REST.

### C# / .NET

No production-grade own-engine converter exists in open-source .NET (the polished native options like IronPDF, SelectPdf are proprietary), so the open-source path is Chromium:

- **[PuppeteerSharp](https://github.com/hardkoded/puppeteer-sharp)**: faithful .NET port of Puppeteer, MIT, very actively maintained.
- **[Playwright for .NET](https://github.com/microsoft/playwright-dotnet)**: official Microsoft bindings, multi-browser.

Other options:

[DinkToPdf](https://github.com/rdvojmoc/DinkToPdf) still shows up in tutorials, but it hasn't seen a release since 2017 and wraps archived wkhtmltopdf
- [Gotenberg](https://github.com/gotenberg/gotenberg) as a sidecar container

### Ruby

- **[Grover](https://github.com/Studiosity/grover)**: Rails-friendly Puppeteer wrapper (needs Node.js alongside Ruby). Actively maintained, middleware support.
- **[ferrum_pdf](https://github.com/excid3/ferrum_pdf)**: newer and leaner: drives Chrome via CDP through the Ferrum gem, **no Node dependency**. From the GoRails team.
- [Prawn](https://github.com/prawnpdf/prawn) for programmatic PDF generation
- [wicked_pdf](https://github.com/mileszs/wicked_pdf): wkhtmltopdf wrapper
- [PDFKit](https://github.com/pdfkit/pdfkit): wkhtmltopdf wrapper

### Go

- **[chromedp](https://github.com/chromedp/chromedp)**: chromium-based, `page.PrintToPDF()` does the work. No external Go dependencies.
- **[go-rod](https://github.com/go-rod/rod)**:higher-level, auto-waiting, friendlier API
[go-wkhtmltopdf](https://github.com/SebastiaanKlippert/go-wkhtmltopdf) is a well-typed wrapper.
- [Gotenberg](https://github.com/gotenberg/gotenberg): stateless Docker container exposing a REST API, with Chromium

### Rust

All roads lead to Chrome.

- **[headless_chrome](https://github.com/rust-headless-chrome/rust-headless-chrome)**: sync API, `print_to_pdf` built in
- **[chromiumoxide](https://github.com/mattsse/chromiumoxide)**: async (Tokio), auto-generated full CDP bindings.

For paged-media quality:
- [WeasyPrint](https://github.com/Kozea/WeasyPrint)
- [Gotenberg](https://github.com/gotenberg/gotenberg)

### Any language: CLIs & self-hosted services

- **[Gotenberg](https://github.com/gotenberg/gotenberg)**: a stateless Docker container exposing a REST API, with Chromium (HTML/Markdown→PDF), LibreOffice (Office→PDF), and PDF post-processing (merge, PDF/A) inside. One container serves every language in your stack; client libraries exist for JS, Python, PHP, Java, Go, and more.
- **[Stirling-PDF](https://github.com/Stirling-Tools/Stirling-PDF)**: hugely popular self-hosted PDF toolbox (50+ operations); HTML→PDF is one feature among many (WeasyPrint-backed), not its core focus.
- **[Chromium `--print-to-pdf`](https://www.chromium.org/developers/how-tos/run-chromium-with-flags/)**: zero-dependency baseline: `chromium --headless --print-to-pdf=out.pdf page.html`. This is what most tools above ultimately call.
- **[Vivliostyle CLI](https://github.com/vivliostyle/vivliostyle-cli)**: professional CSS typesetting (books, magazines, academic) with full Paged Media support. AGPL-3.0.
- **[LibreOffice headless](https://www.libreoffice.org/)**: `soffice --headless --convert-to pdf file.html`. Its HTML import filter is basic; right tool for Office documents, last resort for HTML.

---

## Choosing guide

### Browser engine vs. own engine

Almost every decision in this space reduces to one question: **real browser or dedicated engine?**

| | 🌐 Headless browser (Chromium) | 🧩 Own engine (WeasyPrint, dompdf, OpenHTMLtoPDF, …) |
|---|---|---|
| Rendering fidelity | Identical to Chrome: if it looks right in the browser, it prints right | Good on supported CSS; surprises on modern layouts |
| JavaScript | ✅ full | ❌ almost never |
| Footprint | Chromium binary (~300 MB+), ~0.5–2 s launch, ~100–500 MB RAM per page | A library import; renders in-process |
| Determinism | Browser updates can shift output between versions | Output stable for years |
| Untrusted input | Sandboxed by design (still configure it) | HTML parser + your process = larger attack surface |

Rule of thumb: **web-looking documents from web content → browser; document-looking documents from templates you control → own engine.**

### Paged media: headers, footers, page numbers

Print documents need things screens don't have: page numbers, running headers, "continued on next page", widow/orphan control, a TOC with page references. CSS defines all of this ([Paged Media Module](https://www.w3.org/TR/css-page-3/)) but support varies wildly:

- **Best:** [WeasyPrint](https://github.com/Kozea/WeasyPrint), [Vivliostyle](https://github.com/vivliostyle/vivliostyle-cli), [Paged.js](https://github.com/pagedjs/pagedjs-cli): margin boxes, `counter(page)` / `counter(pages)`, named pages, running elements.
- **Good:** [OpenHTMLtoPDF](https://github.com/openhtmltopdf/openhtmltopdf), [mPDF](https://github.com/mpdf/mpdf), [dompdf](https://github.com/dompdf/dompdf), [iText pdfHTML](https://github.com/itext/itext-pdfhtml-java): `@page` size/margins, page breaks, header/footer mechanisms (sometimes via proprietary tags/APIs).
- **Limited:** Chromium-based tools: page size, margins, and an HTML header/footer *template API* ([Puppeteer](https://github.com/puppeteer/puppeteer)'s `headerTemplate`/`footerTemplate`), but templates are styled in isolation and `@page` margin boxes don't work. Paged.js exists precisely to fill this gap.

If your document is a *report* rather than a *page*, weight this criterion heavily.


### Performance & deployment

- **Browser-based** tools pay a launch cost (~0.5–2 s) and hold ~100–500 MB RAM per page. In production: reuse one browser instance across conversions (new *page* per job, not new *browser*), pool pages, and recycle the browser periodically to contain leaks.
- **Serverless:** stock Chromium exceeds Lambda-style size limits. Use trimmed builds like [@sparticuz/chromium](https://github.com/Sparticuz/chromium). Own-engine libraries (WeasyPrint, dompdf) deploy trivially by comparison.
- **Docker:** Chromium needs fonts and shared libraries so start from images that include them, or use [Gotenberg](https://github.com/gotenberg/gotenberg) / [browserless](https://github.com/browserless/browserless) as a sidecar and keep your app image clean.
- **Fonts** are the #1 "works locally, broken in prod" cause: install the fonts your templates use in the container (`fonts-liberation`, `fonts-noto`, CJK packs as needed).
- **Throughput:** own-engine converters typically render simple documents faster per-document at low memory; browsers win when amortized over a warm pool and required for fidelity anyway.

### The wkhtmltopdf problem

For a decade, [wkhtmltopdf](https://github.com/wkhtmltopdf/wkhtmltopdf) *was* HTML-to-PDF, and an entire wrapper ecosystem grew around it. The project was **archived in January 2023** and there is no maintenance, no security patches, and a rendering engine (QtWebKit) frozen around 2012: no flexbox you can trust, no grid, no modern JS.

Every wrapper inherits this: [python-pdfkit](https://github.com/JazzCore/python-pdfkit), [Snappy](https://github.com/KnpLabs/snappy)/[laravel-snappy](https://github.com/barryvdh/laravel-snappy), [phpwkhtmltopdf](https://github.com/mikehaertl/phpwkhtmltopdf), [wicked_pdf](https://github.com/mileszs/wicked_pdf), [PDFKit (Ruby)](https://github.com/pdfkit/pdfkit), [go-wkhtmltopdf](https://github.com/SebastiaanKlippert/go-wkhtmltopdf), [DinkToPdf](https://github.com/rdvojmoc/DinkToPdf), [node-wkhtmltopdf](https://github.com/devongovett/node-wkhtmltopdf). The wrapper being "maintained" doesn't help when the binary is the problem.

**Migration paths:**

| Coming from | Fidelity path (browser) | Paged-media path (own engine) |
|---|---|---|
| python-pdfkit | [Playwright](https://github.com/microsoft/playwright-python) | [WeasyPrint](https://github.com/Kozea/WeasyPrint) |
| Snappy / laravel-snappy | [Browsershot](https://github.com/spatie/browsershot) / [laravel-pdf](https://github.com/spatie/laravel-pdf) | [dompdf](https://github.com/dompdf/dompdf) / [mPDF](https://github.com/mpdf/mpdf) |
| wicked_pdf / PDFKit | [Grover](https://github.com/Studiosity/grover) / [ferrum_pdf](https://github.com/excid3/ferrum_pdf) | - |
| DinkToPdf | [PuppeteerSharp](https://github.com/hardkoded/puppeteer-sharp) | - |
| go-wkhtmltopdf | [chromedp](https://github.com/chromedp/chromedp) / [go-rod](https://github.com/go-rod/rod) | - |
| node-wkhtmltopdf | [Puppeteer](https://github.com/puppeteer/puppeteer) | [Paged.js](https://github.com/pagedjs/pagedjs-cli) |
| CLI usage | Chromium `--print-to-pdf` / [Gotenberg](https://github.com/gotenberg/gotenberg) | [WeasyPrint](https://github.com/Kozea/WeasyPrint) CLI / [Vivliostyle](https://github.com/vivliostyle/vivliostyle-cli) |

The main functional regression to plan for: wkhtmltopdf's `--header-html`/`--footer-html` with page variables. Chromium's template API covers simple cases; [Paged.js](https://github.com/pagedjs/pagedjs-cli) or an own-engine tool covers the rest.

---

## Deprecated / do not use for new projects

| Tool | State | Use instead |
|---|---|---|
| [wkhtmltopdf](https://github.com/wkhtmltopdf/wkhtmltopdf) | Archived Jan 2023, unpatched CVEs | See [migration table](#the-wkhtmltopdf-problem) |
| [PhantomJS](https://github.com/ariya/phantomjs) | Abandoned 2017 | [Puppeteer](https://github.com/puppeteer/puppeteer) / [Playwright](https://github.com/microsoft/playwright) |
| [node-html-pdf](https://github.com/marcbachmann/node-html-pdf) | Archived 2026 (PhantomJS-based) | [Puppeteer](https://github.com/puppeteer/puppeteer) |
| [Athena PDF](https://github.com/arachnys/athenapdf) | Archived 2023 | [Gotenberg](https://github.com/gotenberg/gotenberg) |
| [DinkToPdf](https://github.com/rdvojmoc/DinkToPdf) | No release since 2017 + dead upstream | [PuppeteerSharp](https://github.com/hardkoded/puppeteer-sharp) |
| [Pyppeteer](https://github.com/pyppeteer/pyppeteer) | Unmaintained | [Playwright for Python](https://github.com/microsoft/playwright-python) |

---

## Not quite HTML→PDF: programmatic & template-based alternatives

These come up in every "generate a PDF" search, but they **don't consume HTML/CSS**. Instead you describe the document in code or a schema instead. Often the better choice when you control the layout and don't have existing HTML:

- [react-pdf](https://github.com/diegomura/react-pdf) (JS) — declare PDFs with React components and flexbox-style props.
- [pdfmake](https://github.com/bpampuch/pdfmake) (JS) — declarative JSON document definitions.
- [pdfme](https://github.com/pdfme/pdfme) (TS) — schema/template-based, with a WYSIWYG designer.
- [jsPDF](https://github.com/parallax/jsPDF) (JS) — imperative canvas-like API (its `.html()` mode rasterizes via html2canvas).
- [Prawn](https://github.com/prawnpdf/prawn) (Ruby), [ReportLab](https://www.reportlab.com/opensource/) (Python), [Apache PDFBox](https://github.com/apache/pdfbox) (Java), [pdf-lib](https://github.com/Hopding/pdf-lib) (JS, also edits existing PDFs) — the low-level PDF construction workhorses per ecosystem.
