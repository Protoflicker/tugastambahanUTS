# 05: Unit Tests and pytest (Pengujian Unit)

## Tujuan
Langkah ini memperkenalkan praktik penting dalam pengembangan perangkat lunak: Unit Testing. Kita mengintegrasikan framework pengujian populer pytest ke dalam proyek Pyramid untuk memastikan kualitas dan fungsionalitas kode.

- Menambahkan pytest sebagai dependensi pengembangan menggunakan Setuptools Extras.
- Menulis unit test dasar untuk view callable Pyramid.
- Memahami penggunaan testing helpers pyramid.testing (setUp, tearDown, DummyRequest).
- Menjalankan tes menggunakan test runner pytest.

## Perubahan Kode dan Konfigurasi

### 1. setup.py: Menambahkan pytest
pytest ditambahkan ke daftar dependensi pengembangan (dev_requires) di dalam extras_require.

```python
# ...
dev_requires = [
	'pyramid_debugtoolbar',
	'pytest', # 
]

setup(
	# ...
	extras_require={
		'dev': dev_requires,
	},
	# ...
)
```

Instalasi:

```powershell
pip install -e ".[dev]"
```

Perintah ini memastikan bahwa pytest diinstal bersama dependensi pengembangan lainnya.

### 2. File Tes Baru (`tutorial/tests.py`)
Kita membuat file baru untuk menampung unit test kita, menargetkan view callable `hello_world`.

```python
import unittest
from pyramid import testing

class TutorialViewTests(unittest.TestCase):
	def setUp(self):
		# Setup helpers (membuat Configurator temporer jika diperlukan)
		self.config = testing.setUp()

	def tearDown(self):
		# Membersihkan konfigurasi temporer
		testing.tearDown()

	def test_hello_world(self):
		# Import View di dalam tes (untuk isolasi)
		from tutorial import hello_world

		# Membuat tiruan (mock) objek Request
		request = testing.DummyRequest()
        
		# Memanggil View Callable dengan request tiruan
		response = hello_world(request)
        
		# Assertion: Memastikan kode status HTTP adalah 200 (OK)
		self.assertEqual(response.status_code, 200)
```

## Hasil Aplikasi (Menjalankan Tes)

Setelah menulis tes, kita menjalankannya menggunakan pytest:

```powershell
pytest tutorial/tests.py -q
```

Output:

```
.1 passed in 0.14 seconds
```

Output ini mengonfirmasi bahwa satu tes telah berjalan dengan sukses (`.1 passed`).

## Analisis Konsep Utama

### 1. Peran pytest
- **Test Runner Alternatif:** pytest adalah alat yang mengeksekusi tes Python. Ini menawarkan sintaks yang lebih ringkas dan fixtures yang lebih kuat daripada framework unittest bawaan Python.

### 2. Isolasi Tes (Unit)
- **Import Lokal:** Baris `from tutorial import hello_world` ditempatkan di dalam fungsi tes, bukan di bagian atas modul.
- **Tujuan:** Untuk memastikan bahwa tes diisolasi (Unit) dan tidak ada impor atau efek samping dari satu tes yang secara tidak sengaja memengaruhi tes lainnya.

### 3. pyramid.testing Helpers
- `testing.setUp()` dan `testing.tearDown()`: Fungsi-fungsi ini dari Pyramid menyediakan lingkungan pengujian yang bersih. Mereka sangat berguna ketika Anda perlu memanipulasi atau menambahkan sesuatu ke konfigurasi aplikasi (config) sebelum menjalankan view.
- `testing.DummyRequest()`: Kelas mock yang membuat objek Request palsu.
- **Tujuan:** Unit test harus menguji view secara terisolasi tanpa memerlukan server HTTP yang sebenarnya atau jaringan. Dummy Request memungkinkan kita memanggil view callable seolah-olah permintaan web nyata telah diterima.

### 4. Assertion
- `self.assertEqual(response.status_code, 200)`: Ini adalah Assertion (penegasan). Ini adalah inti dari tes: memverifikasi bahwa hasil aktual (kode status HTTP dari respons) sesuai dengan hasil yang diharapkan (200 OK).

## Kesimpulan

Langkah 05 berhasil mengintegrasikan pengujian unit ke dalam proyek Pyramid. Dengan menggunakan pytest dan helper Pyramid yang efisien, kita dapat secara cepat dan andal memverifikasi bahwa komponen terkecil dari aplikasi (fungsi view) bekerja sesuai harapan, yang merupakan praktik fundamental untuk kualitas kode jangka panjang.
