# 09 - Organizing Views With View Classes

## Tujuan Pembelajaran
- Mengubah view functions menjadi methods di dalam sebuah kelas (`TutorialViews`).
- Memahami mekanisme Pyramid untuk menginstansiasi Kelas View.
- Menggunakan `@view_defaults` untuk memusatkan konfigurasi yang berulang (seperti renderer) di tingkat kelas.
- Memperbarui unit test untuk mencerminkan pola pengujian berbasis kelas.

## Hasil Aplikasi
Aplikasi tetap menampilkan halaman utama dan hello seperti sebelumnya, namun kode view kini lebih terstruktur dan mudah dikembangkan.

## Perubahan Kode dan Konfigurasi

### 1. `tutorial/views.py`: Introduksi Kelas View
Semua view dikelompokkan dalam kelas `TutorialViews`, dan konfigurasi umum dipindahkan ke kelas.
```python
from pyramid.view import (
    view_config,
    view_defaults # <-- Import baru
    )

# Mengatur 'renderer' default untuk semua method di kelas ini
@view_defaults(renderer='home.pt')
class TutorialViews:
    def __init__(self, request):
        # Pyramid akan otomatis memanggil __init__ dengan objek Request
        self.request = request

    @view_config(route_name='home')
    def home(self):
        # View method tidak lagi menerima 'request' sebagai argumen
        return {'name': 'Home View'}

    @view_config(route_name='hello')
    def hello(self):
        return {'name': 'Hello View'}
```
**Analisis:**
- `@view_defaults(renderer='home.pt')`: Menetapkan renderer default untuk semua method di kelas.
- `__init__(self, request)`: Pyramid menginstansiasi kelas dan meneruskan objek request.
- View method mengakses request melalui `self.request`.

### 2. `tutorial/tests.py`: Memperbarui Unit Tests
Tes unit diubah untuk menguji View Class secara langsung.
```python
    def test_home(self):
        from .views import TutorialViews

        request = testing.DummyRequest()
        inst = TutorialViews(request) # <--- Instansiasi dengan DummyRequest
        response = inst.home()        # <--- Panggil method home()
        self.assertEqual('Home View', response['name'])

    # ... (Test hello memiliki pola yang sama)
```
**Analisis:**
- Pengujian membuat DummyRequest, menginstansiasi kelas, dan memanggil method view.
- Functional tests tidak perlu diubah karena tetap menguji aplikasi secara end-to-end.

## Analisis Konsep Utama
1. **Keuntungan Kelas View**
   - Pengelompokan logis fungsi-fungsi terkait.
   - Berbagi state/helper di antara method.
   - Konfigurasi terpusat dengan `@view_defaults`.
2. **Konfigurasi Gabungan**
   - `@view_defaults` di kelas: nilai default (misal renderer).
   - `@view_config` di method: kriteria spesifik (misal route_name), bisa menimpa default kelas.

## Kesimpulan
Langkah 09 berhasil memindahkan fungsionalitas view ke dalam View Class tanpa mengubah output aplikasi, meningkatkan struktur kode secara signifikan. Pola ini adalah dasar untuk membangun API RESTful dan mengelola view yang berinteraksi dengan sumber daya data yang sama.
