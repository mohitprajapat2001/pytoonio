# PyToonIo

![Python](https://img.shields.io/badge/Python-3.10%2B-green?style=for-the-badge&logo=python)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)
![PyPI](https://img.shields.io/badge/PyPI-pytoonio-orange?style=for-the-badge&logo=pypi)
[![PyPI Downloads](https://static.pepy.tech/personalized-badge/pytoonio?period=total&units=INTERNATIONAL_SYSTEM&left_color=BLACK&right_color=GREEN&left_text=downloads)](https://pepy.tech/projects/pytoonio)

**A lightweight Python library for converting between JSON, XML, and TOON — a modern,
human-friendly data format.**

---

## 🚀 What is TOON?

**TOON** is a clean and expressive data format that focuses on:

- ✅ **Improved human readability** — Easy to read and understand at a glance
- ✅ **Minimal syntax complexity** — Less visual noise compared to JSON and XML
- ✅ **Easier manual editing** — Friendly for developers and non-developers alike
- ✅ **Clear structure representation** — Logical and consistent nesting

It aims to combine the structural clarity of JSON and XML while significantly reducing
visual clutter.

---

## ✨ Features

| Feature        | Description                               |
| -------------- | ----------------------------------------- |
| 🔄 JSON → TOON | Convert any JSON data to the TOON format  |
| 🔄 XML → TOON  | Convert any XML data to the TOON format   |
| 🔄 TOON → JSON | Parse TOON back into JSON                 |
| 🔄 TOON → XML  | Parse TOON back into XML                  |
| 🪶 Lightweight | Minimal dependencies, fast and efficient  |
| 🧩 Simple API  | Clean, intuitive API for easy integration |

---

## 📦 Installation

Install pytoonio via pip:

```bash
pip install pytoonio
```

**Requirements:** Python 3.10+

---

## 🧑‍💻 Usage

### Quick Start

```python
from pytoonio import convert
```

---

### 🔄 JSON → TOON

```python
from pytoonio import convert

data = {
    "users": [
        {"id": 1001, "name": "Emma Wilson", "role": "admin"},
        {"id": 1002, "name": "James Brown", "role": "editor"},
    ],
    "totalCount": 2,
    "active": True,
}

toon = convert.json_to_toon(data)
print(toon)
```

**Output:**

```
users[2]{id,name,role}:
  1001,Emma Wilson,admin
  1002,James Brown,editor
totalCount: 2
active: true
```

> 🔷 Lists of objects with **identical keys** use the compact `key[N]{headers}:`
> annotation. Each data row is comma-separated (using the configured delimiter).

---

### 🔄 TOON → JSON

```python
from pytoonio import convert

toon = """
users[2]{id,name,role}:
  1001,Emma Wilson,admin
  1002,James Brown,editor
totalCount: 2
active: true
"""

json_str = convert.toon_to_json(toon)
print(json_str)
# {"users": [{"id": 1001, ...}], "totalCount": 2, "active": true}

# Or as a Python dict
data = convert.toon_to_json(toon, as_string=False)
print(data["totalCount"])  # 2
```

---

### 🔄 XML → TOON

```python
from pytoonio import convert

xml = """
<company>
  <name>TechCorp International</name>
  <founded>2010</founded>
  <headquarters>
    <city>San Francisco</city>
    <country>USA</country>
  </headquarters>
</company>
"""

toon = convert.xml_to_toon(xml)
print(toon)
```

**Output:**

```
company:
  name: TechCorp International
  founded: 2010
  headquarters:
    city: San Francisco
    country: USA
```

---

### 🔄 TOON → XML

```python
from pytoonio import convert

toon = """
company:
  name: TechCorp International
  founded: 2010
  headquarters:
    city: San Francisco
    country: USA
"""

xml = convert.toon_to_xml(toon)
print(xml)
# <company><name>TechCorp International</name><founded>2010</founded>...</company>
```

---

### ⚙️ Configuration Options

All conversion functions support two options:

| Option      | Values                       | Default   | Description                  |
| ----------- | ---------------------------- | --------- | ---------------------------- |
| `indent`    | `2`, `4`                     | `2`       | Spaces per indentation level |
| `delimiter` | `"comma"`, `"tab"`, `"pipe"` | `"comma"` | Separator for tabular data   |

```python
from pytoonio import convert

data = {
    "products": [
        {"id": "P001", "name": "Headphones", "price": 149.99},
        {"id": "P002", "name": "Smart Watch", "price": 299.99},
    ]
}

# 4-space indent + pipe delimiter
toon = convert.json_to_toon(data, indent=4, delimiter="pipe")
print(toon)
```

**Output with `indent=4, delimiter="pipe"`:**

```
products:
    id | name | price
    P001 | Headphones | 149.99
    P002 | Smart Watch | 299.99
```

**Output with `indent=2, delimiter="tab"`:**

```
products:
  id	name	price
  P001	Headphones	149.99
  P002	Smart Watch	299.99
```

---

### 🏗️ Class-Based API

For more control, use the encoder/decoder classes directly:

```python
from pytoonio.converters import JsonToToonEncoder, ToonToJsonDecoder
from pytoonio.converters import XmlToToonEncoder, ToonToXmlDecoder

# Reuse the same encoder instance
encoder = JsonToToonEncoder(indent=4, delimiter="pipe")

toon1 = encoder.encode({"name": "Alice", "age": 30})
toon2 = encoder.encode({"project": "pytoonio", "version": "0.0.1"})

# Decode with matching delimiter
decoder = ToonToJsonDecoder(delimiter="pipe")
data = decoder.decode(toon1)
```

---

### 📖 TOON Format Reference

| Data                         | TOON Syntax                          |
| ---------------------------- | ------------------------------------ |
| Key-value                    | `key: value`                         |
| Nested object                | Indented `key:` block                |
| Primitive list (under a key) | `key: [item1, item2, item3]`         |
| **Uniform object list**      | `key[N]{col1,col2,...}:` + data rows |
| **Non-uniform object list**  | `key[N]:` + `-` dash blocks          |
| Null                         | `null`                               |
| Boolean                      | `true` / `false`                     |

**Uniform object list** — all objects share the same keys:

```
users[3]{id,name,role}:
  1,Alice,admin
  2,Bob,user
  3,Charlie,user
```

**Non-uniform object list** — objects have different keys:

```
users[2]:
  -
    id: 1
    name: Alice
    email: alice@example.com
  -
    name: Charlie
    email: charlie@example.com
    role: user
```

**Full example — all TOON types:**

```
name: Mohit
age: 25
active: true
score: null
skills: [Python, Django, REST]
analytics:
  period: 2024-11
  metrics:
    pageViews: 125000
    bounceRate: 42.5
topPages[3]{url,views,avgTime}:
  /products,35000,180
  /blog,28000,320
  /pricing,22000,150
```

---

### 🧪 Running Tests

```bash
# Install dev dependencies
pip install pytest pytest-cov

# Run all tests
pytest

# Run with coverage report
pytest --cov=pytoonio --cov-report=term-missing
```

---

## 📌 Project Information

| Field          | Value                                                                                  |
| -------------- | -------------------------------------------------------------------------------------- |
| **Name**       | pytoonio                                                                               |
| **Version**    | 0.0.1                                                                                  |
| **Author**     | Mohit Prajapat                                                                         |
| **Email**      | mohitdevelopment2001@gmail.com                                                         |
| **License**    | MIT                                                                                    |
| **Python**     | ≥ 3.10                                                                                 |
| **Repository** | [github.com/mohitprajapat2001/pytoonio](https://github.com/mohitprajapat2001/pytoonio) |

---

## 🤝 Contributing

Contributions are welcome! Feel free to:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-feature`)
3. Commit your changes (`git commit -m 'Add my feature'`)
4. Push to the branch (`git push origin feature/my-feature`)
5. Open a Pull Request

Please read `CODE_OF_CONDUCT.md` for our community guidelines.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENCE](LICENCE) file for
details.

---

## 👥 Authors & Contributors

See [AUTHORS.rst](AUTHORS.rst) and [CONTRIBUTORS](CONTRIBUTORS) for a full list.

---

<div align="center">
Made with ❤️ by <a href="https://github.com/mohitprajapat2001">Mohit Prajapat</a>
</div>
