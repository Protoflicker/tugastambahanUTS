# 08 - HTML Generation With Templating

## Tujuan Pembelajaran
- Menginstal dan mengaktifkan `pyramid_chameleon` sebagai renderer.
- Mengubah view agar hanya mengembalikan data (dictionary Python), bukan objek Response.
- Menghubungkan view dengan template menggunakan argumen `renderer='template_file.pt'` pada `@view_config`.
- Mengaktifkan `pyramid.reload_templates = true` di file `.ini` untuk kemudahan pengembangan.

## Hasil Aplikasi
Aplikasi kini menampilkan konten dinamis dari template `home.pt`.

### 1. Halaman Utama (`/`)
- **URL:** http://localhost:6543/
- **Tampilan:**
![Tampilan browser](1.jpg)
- **Output:**
  ```html
  <h1>Hi Home View</h1>
  ```

### 2. Halaman Hello (`/howdy`)
- **URL:** http://localhost:6543/howdy
- **Tampilan:**
![Tampilan browser](2.jpg)
- **Output:**
  ```html
  <h1>Hi Hello View</h1>
  ```

## Perubahan Kode dan Konfigurasi

### 1. `setup.py`: Menambahkan pyramid_chameleon
```python
# ...
requires = [
    'pyramid',
    'pyramid_chameleon', 
    'waitress',
]
# ...
```

### 2. `tutorial/__init__.py`: Mengaktifkan Renderer
```python
from pyramid.config import Configurator

def main(global_config, **settings):
    config = Configurator(settings=settings)
    config.include('pyramid_chameleon') 
    config.add_route('home', '/')
    config.add_route('hello', '/howdy')
    config.scan('.views')
    return config.make_wsgi_app()
```
**Analisis:** `config.include('pyramid_chameleon')` mendaftarkan renderer Chameleon sehingga Pyramid dapat memproses file template `.pt`.

### 3. `tutorial/views.py`: View Mengembalikan Data
```python
from pyramid.view import view_config

@view_config(route_name='home', renderer='home.pt')
def home(request):
    return {'name': 'Home View'}

@view_config(route_name='hello', renderer='home.pt')
def hello(request):
    return {'name': 'Hello View'}
```
**Analisis:** Argumen `renderer='home.pt'` pada `@view_config` membuat Pyramid otomatis merender dictionary yang dikembalikan menggunakan template yang ditentukan.

### 4. `tutorial/home.pt`: Template Chameleon
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Quick Tutorial: ${name}</title>
</head>
<body>
    <h1>Hi ${name}</h1>
</body>
</html>
```
**Analisis:** Sintaks `${name}` mengambil nilai dari dictionary yang dikembalikan oleh view.

### 5. `development.ini`: Template Reloading
```ini
[app:main]
use = egg:tutorial
pyramid.reload_templates = true 
pyramid.includes =
    pyramid_debugtoolbar
# ...
```
**Analisis:** `pyramid.reload_templates = true` memastikan perubahan pada file `.pt` langsung diterapkan tanpa restart server.

### 6. Pembaruan Pengujian (`tutorial/tests.py`)
- **Unit Test:** Menguji dictionary yang dikembalikan oleh view, misal: `self.assertEqual('Home View', response['name'])`.
- **Functional Test:** Menguji HTML hasil render, misal: `self.assertIn(b'<h1>Hi Home View', res.body)`.

## Analisis Konsep Utama
1. **Templating sebagai Renderer**
   - Renderer mengubah dictionary Python menjadi objek Response HTTP.
   - View fokus pada logika/data, template fokus pada presentasi.
2. **View vs Renderer**
   - View: menerima request, memproses data, mengembalikan dict.
   - Renderer: menerima dict, mengisi placeholder di template, mengembalikan Response berisi HTML.

## Kesimpulan
Langkah 08 menyelesaikan pemisahan Model-View dasar. Pyramid kini menggunakan templating, membuat kode lebih bersih, mudah diuji, dan profesional.
