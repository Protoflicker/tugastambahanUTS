# 12 - Templating With jinja2

## Tujuan Pembelajaran
- Menunjukkan dukungan Pyramid untuk berbagai sistem templating.
- Menginstal dan mengaktifkan add-on `pyramid_jinja2`.
- Mengubah konfigurasi renderer untuk menggunakan Jinja2 tanpa memodifikasi logika view utama.

## Hasil Aplikasi
Aplikasi tetap berjalan seperti sebelumnya, namun kini menggunakan Jinja2 sebagai template engine.

## Perubahan Kode dan Konfigurasi

### 1. `setup.py`: Menambahkan pyramid_jinja2
`pyramid_jinja2` ditambahkan ke daftar dependensi wajib.
```python
# ...
requires = [
    'pyramid',
    # 'pyramid_chameleon', # <--- Chameleon dihilangkan
    'pyramid_jinja2',     # <--- Tambahan Baru
    'waitress',
]
# ...
```
Instalasi:
```bash
$VENV/bin/pip install -e .
```

### 2. `tutorial/__init__.py`: Mengaktifkan Jinja2
```python
from pyramid.config import Configurator

def main(global_config, **settings):
    config = Configurator(settings=settings)
    config.include('pyramid_jinja2') # <--- Mendaftarkan Jinja2
    config.add_route('home', '/')
    config.add_route('hello', '/howdy')
    config.scan('.views')
    return config.make_wsgi_app()
```
**Analisis:**
- `config.include('pyramid_jinja2')` mendaftarkan renderer Jinja2 untuk ekstensi file `.jinja2`.

### 3. `tutorial/views.py`: Mengubah Renderer
```python
from pyramid.view import view_config, view_defaults

@view_defaults(renderer='home.jinja2')
class TutorialViews:
    def __init__(self, request):
        self.request = request

    @view_config(route_name='home')
    def home(self):
        return {'name': 'Home View'}

    @view_config(route_name='hello')
    def hello(self):
        return {'name': 'Hello View'}
```
**Analisis:**
- Logika View tetap berfokus pada pengembalian dictionary data.
- View di Pyramid independen dari pilihan templating engine.

### 4. `tutorial/home.jinja2`: Template Jinja2
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Quick Tutorial: {{ name }}</title>
</head>
<body>
    <h1>Hi {{ name }}</h1>
</body>
</html>
```
**Analisis:**
- Sintaks `{{ name }}` digunakan oleh Jinja2 untuk penyisipan variabel.

### 5. Pengujian
- **Unit test dan Functional test tetap sama.**
- Unit test hanya menguji data yang dikembalikan (dictionary Python).
- Functional test memverifikasi output HTML yang dirender.

## Analisis Konsep Utama
1. **Bukti Ketidakberpihakan (Agnosticism)**
   - Pyramid tidak berpihak pada templating engine tertentu.
   - Cukup install add-on, include di config, dan ganti file extension/nama renderer.
2. **Mekanisme Add-on**
   - Add-on seperti `pyramid_jinja2` diinstal sebagai paket Python standar.
   - `config.include()` mengintegrasikan renderer baru ke Pyramid.
3. **Keuntungan Renderer**
   - Model renderer Pyramid mengabstraksikan proses rendering.
   - View hanya mengembalikan data, Pyramid memilih renderer berdasarkan ekstensi file.

## Kesimpulan
Langkah 12 berhasil mengalihkan aplikasi dari satu template engine ke template engine lain dengan modifikasi minimal pada kode inti. Ini menyoroti filosofi desain Pyramid yang menekankan modularitas dan kemudahan penggantian komponen.
