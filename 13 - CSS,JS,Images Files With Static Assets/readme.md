# 13 - CSS, JS, Images Files With Static Assets

## Tujuan Pembelajaran
- Menggunakan `config.add_static_view` untuk memublikasikan direktori aset statis pada URL tertentu.
- Menggunakan notasi Setuptools Asset Specification (`package:path`) untuk menunjuk ke lokasi aset di disk.
- Menggunakan `request.static_url()` di template untuk menghasilkan URL aset secara fleksibel.

## Hasil Aplikasi
Aplikasi kini dapat melayani file statis (CSS, JS, gambar) dan menghubungkannya secara dinamis di template.

## Perubahan Kode dan Konfigurasi

### 1. `tutorial/__init__.py`: Mendaftarkan Static View
```python
from pyramid.config import Configurator

def main(global_config, **settings):
    config = Configurator(settings=settings)
    # ...
    config.add_route('hello', '/howdy')
    # Menetapkan URL /static/ ke direktori 'static' di dalam paket 'tutorial'
    config.add_static_view(name='static', path='tutorial:static')
    config.scan('.views')
    return config.make_wsgi_app()
```
**Analisis:**
- `name='static'`: Mendefinisikan URL prefix untuk file statis (misal, http://localhost:6543/static/...).
- `path='tutorial:static'`: Menggunakan Setuptools Asset Specification untuk menunjuk ke direktori static di dalam paket tutorial.

### 2. `tutorial/static/app.css`: File Aset Baru
```css
body {
    margin: 2em;
    font-family: sans-serif;
}
```

### 3. `tutorial/home.pt`: Menggunakan request.static_url
```html
<head>
    <title>Quick Tutorial: ${name}</title>
    <link rel="stylesheet"
          href="${request.static_url('tutorial:static/app.css') }"/>
</head>
```
**Analisis:**
- `request.static_url(...)`: Fungsi Pyramid untuk menghasilkan URL lengkap ke aset statis berdasarkan pendaftaran `add_static_view`.
- Jika prefix URL diubah, semua URL di template otomatis ikut berubah.

### 4. Pembaruan Pengujian
```python
    def test_css(self):
        res = self.testapp.get('/static/app.css', status=200)
        self.assertIn(b'body', res.body)
```
**Analisis:**
- Tes ini memastikan URL `/static/app.css` dapat diakses dan mengembalikan konten CSS yang benar.

## Hasil Aplikasi Utama
- Setelah aplikasi dijalankan dan membuka http://localhost:6543/, styling akan diterapkan.
- Teks di halaman menggunakan font sans-serif dan margin 2em, menandakan file CSS berhasil dimuat.

## Analisis Konsep Utama
1. **Setuptools Asset Specification**
   - Format `tutorial:static/app.css` merujuk ke file di dalam paket Python, agnostik terhadap lokasi instalasi.
2. **URL Generation vs. Hardcoding**
   - Hindari hardcoding URL. Gunakan `request.static_url` agar aplikasi tetap fleksibel dan aman terhadap perubahan deployment.

## Kesimpulan
Langkah 13 berhasil mengintegrasikan aset statis ke dalam aplikasi Pyramid. Dengan `config.add_static_view` dan `request.static_url`, Pyramid menyediakan cara fleksibel dan andal untuk mengelola CSS, JS, dan file media lainnya, sesuai kebutuhan aplikasi web modern.
