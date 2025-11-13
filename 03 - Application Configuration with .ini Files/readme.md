# 03: Application Configuration with .ini Files

## Tujuan
Langkah ini memisahkan kode aplikasi dari konfigurasi deployment dengan memperkenalkan file konfigurasi `.ini` dan menjalankan aplikasi menggunakan `pserve`.

- Memahami konsep Entry Point di `setup.py`.
- Membuat file konfigurasi `development.ini` untuk aplikasi dan server WSGI.
- Memindahkan bootstrapping aplikasi ke fungsi `main()` di `__init__.py`.
- Menjalankan aplikasi dengan `pserve --reload`.

## Perubahan Struktur Proyek

| File | Perubahan | Fungsi Baru |
|---|---|---|
| `setup.py` | Ditambahkan `entry_points` | Memberi tahu pserve (Pyramid) di mana menemukan factory aplikasi WSGI. |
| `development.ini` | Baru | Mendefinisikan aplikasi dan server WSGI. |
| `tutorial/__init__.py` | Ditambahkan fungsi `main(global_config, **settings)` | Berisi logika konfigurasi (Configurator) yang sebelumnya ada di blok `if __name__ == '__main__'`. |
| `tutorial/app.py` | Dihapus | Tidak lagi digunakan, karena entry point menunjuk ke `__init__.py:main`. |

## Kode yang Dianalisis

### 1. setup.py: Entry Point

```python
setup(
    # ...
    entry_points={
        'paste.app_factory': [
            'main = tutorial:main'
        ],
    },
)
```

**Analisis:**
- `entry_points`: Fitur setuptools yang memberi tahu alat eksternal (seperti pserve) fungsi apa yang harus dipanggil untuk memuat aplikasi.
- `paste.app_factory`: Kunci yang dicari oleh pserve.
- `'main = tutorial:main'`: Mendefinisikan factory utama (main) dan menunjukkan Pyramid harus memuat fungsi `main` dari paket `tutorial`.

### 2. development.ini: Konfigurasi Terpisah

```ini
[app:main]
use = egg:tutorial

[server:main]
use = egg:waitress#main
listen = localhost:6543
```

**Analisis:**
- `[app:main]`: Mendefinisikan aplikasi WSGI. `use = egg:tutorial` merujuk ke entry point di `setup.py`.
- `[server:main]`: Mendefinisikan server WSGI. `use = egg:waitress#main` menginstruksikan pserve untuk menggunakan Waitress.
- `listen = localhost:6543`: Konfigurasi host dan port.

### 3. tutorial/__init__.py: Factory Function

```python
def main(global_config, **settings):
    config = Configurator(settings=settings)
    config.add_route('hello', '/')
    config.add_view(lambda request: Response('<body><h1>Hello World!</h1></body>'), route_name='hello')
    return config.make_wsgi_app()
```

**Analisis:**
- Fungsi `main` adalah WSGI Application Factory yang dipanggil oleh pserve.
- `settings` otomatis diisi dari `[app:main]` di `.ini`.
- Konfigurasi Pyramid kini bisa dinamis sesuai environment.

### 4. Menjalankan Aplikasi

```powershell
pserve development.ini --reload
```

- `pserve`: Alat pelaksana aplikasi Pyramid.
- `development.ini`: File konfigurasi utama.
- `--reload`: Mode pengembangan, auto-restart saat file berubah.

## Hasil Aplikasi

- **Tampilan Browser:** Halaman sederhana `<h1>Hello World!</h1>`.
- **Keluaran Konsol:** Setiap permintaan tetap mencetak `Incoming request` (jika view menambahkan print/log).

## Hasil Analisis Utama

| Tindakan / File | Konsep | Keterangan |
|---|---|---|
| `entry_points` di `setup.py` | Entry Point | Menentukan factory aplikasi untuk pserve. |
| `development.ini` | Konfigurasi Deployment | Memisahkan pengaturan aplikasi dan server dari kode. |
| `main()` di `__init__.py` | Application Factory | Standar Pyramid untuk aplikasi yang fleksibel dan mudah dikonfigurasi. |
| `pserve --reload` | Pengembangan Profesional | Menjalankan aplikasi dengan auto-reload saat file berubah. |

## Kesimpulan

Dengan Langkah 03, aplikasi Pyramid kini terkonfigurasi secara profesional dan fleksibel. Kode aplikasi dan konfigurasi deployment terpisah, memudahkan pengelolaan berbagai environment (dev, prod, test). Ini adalah pola standar di proyek Pyramid modern.