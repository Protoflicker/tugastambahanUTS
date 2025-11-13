# 02: Python Packages for Pyramid Applications

## Tujuan
Langkah ini mentransformasi aplikasi single-file menjadi paket Python yang benar, sesuai praktik standar pengembangan Python modern.

- Membuat struktur proyek Python (`setup.py`).
- Membuat paket Python (`tutorial/` dan `__init__.py`).
- Instalasi proyek dalam mode pengembangan dengan `pip install -e .`.

## Struktur Proyek

```
package/
├── setup.py
└── tutorial/
    ├── __init__.py
    └── app.py
```

## Kode yang Dianalisis

### 1. setup.py (The Project)
File ini mendefinisikan proyek Python dan dependensinya.

```python
from setuptools import setup

requires = [
    'pyramid',
    'waitress',
]

setup(
    name='tutorial',
    install_requires=requires,
)
```

**Analisis:**
- `from setuptools import setup`: Mengimpor alat untuk membangun/menginstal paket.
- `install_requires`: Daftar dependensi (pyramid, waitress).
- `name='tutorial'`: Nama resmi proyek.

### 2. tutorial/__init__.py (The Package)
File ini mengubah direktori tutorial menjadi Python Package yang dapat diimpor.

```python
# package (kosong, tapi wajib ada)
```

**Analisis:**
- `__init__.py`: Penanda direktori sebagai package Python.

### 3. tutorial/app.py (The Module)
Kode aplikasi Hello World dari Langkah 01 dipindahkan ke modul ini. Isinya tidak berubah.

```python
from waitress import serve
from pyramid.config import Configurator
from pyramid.response import Response

def hello_world(request):
    print('Incoming request')
    return Response('<body><h1>Hello World!</h1></body>')

if __name__ == '__main__':
    with Configurator() as config:
        config.add_route('hello', '/')
        config.add_view(hello_world, route_name='hello')
        app = config.make_wsgi_app()
    serve(app, host='0.0.0.0', port=6543)
```

## Hasil Aplikasi

- **Tampilan Browser:** Halaman sederhana `<h1>Hello World!</h1>`.
![Tampilan browser](1.jpg)
- **Keluaran Konsol:** Setiap permintaan mencetak `Incoming request`.

## Hasil Analisis Utama

| Tindakan / File | Konsep | Keterangan |
|---|---|---|
| `pip install -e .` | Development Mode Install | Proyek diinstal editable, perubahan kode langsung aktif. |
| `setup.py` | Python Project | Kerangka dependensi & metadata proyek. |
| `tutorial/` + `__init__.py` | Python Package | Namespace modular, mencegah konflik nama. |
| `tutorial/app.py` | Aplikasi WSGI | Logika aplikasi tidak berubah, hanya lokasi file. |

## Kesimpulan

Langkah 02 mengalihkan fokus dari skrip tunggal ke paket Python terstruktur. Proyek kini siap untuk pengembangan serius, pengujian, dan manajemen dependensi.

Catatan: Menjalankan modul langsung dari package hanya untuk demonstrasi. Untuk aplikasi Pyramid nyata, gunakan entry point di `setup.py` (misal: `pserve`).