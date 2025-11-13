# 06: Functional Testing with WebTest (Pengujian Fungsional)

## Tujuan
Langkah ini meningkatkan kualitas pengujian dari unit test (menguji fungsi tunggal) menjadi Functional Testing (pengujian end-to-end) menggunakan WebTest.

- Menginstal paket webtest sebagai dependensi pengembangan.
- Menulis Functional Test menggunakan WebTest untuk memverifikasi fungsionalitas full-stack.
- Menguji konten HTML yang dikembalikan oleh aplikasi.

## Perubahan Kode dan Konfigurasi

### 1. setup.py: Menambahkan webtest
Tambahkan webtest ke daftar dependensi pengembangan (dev_requires) di dalam extras_require.

```python
# ...
dev_requires = [
	'pyramid_debugtoolbar',
	'pytest',
	'webtest', # <--- Tambahan baru
]
# ...
```

Instalasi:

```powershell
pip install -e ".[dev]"
```

### 2. File Tes (tutorial/tests.py)
Tambahkan kelas baru, TutorialFunctionalTests, untuk pengujian end-to-end.

```python
# ... [Bagian TutorialViewTests yang lama tetap ada]

class TutorialFunctionalTests(unittest.TestCase):
	def setUp(self):
		# 1. Memuat Factory Aplikasi
		from tutorial import main
		app = main({}) # Panggil WSGI factory untuk mendapatkan aplikasi
		# 2. Membuat TestApp
		from webtest import TestApp
		self.testapp = TestApp(app)
	def test_hello_world(self):
		# 3. Mensimulasikan Permintaan GET
		res = self.testapp.get('/', status=200)
		# 4. Assertion (Memeriksa konten respons)
		self.assertIn(b'<h1>Hello World!</h1>', res.body)
```

## Hasil Aplikasi (Menjalankan Tes)

Menjalankan tes menggunakan pytest sekarang menghasilkan dua tes unit dan satu tes fungsional.

```powershell
pytest tutorial/tests.py -q
```

Output:

```
..2 passed in 0.25 seconds
```

Output menunjukkan sukses: dua tes (..) telah dijalankan dan total dua tes (unit dan functional) telah lulus.

## Analisis Konsep Utama

### 1. Functional Testing vs. Unit Testing

| Tipe Tes | Fokus | Keterangan |
|---|---|---|
| Unit Test | Fungsi tunggal (hello_world) | Menguji kode Pyramid View Callable secara terisolasi menggunakan DummyRequest. |
| Functional Test | Full Stack (Routing, View, Response) | Mensimulasikan Request dan Response HTTP penuh terhadap Aplikasi WSGI yang telah dikonfigurasi. |

### 2. Peran WebTest
- **Simulasi Server:** WebTest mengambil objek WSGI Application (app yang dibuat oleh main({})) dan memungkinkannya dipanggil seolah-olah server HTTP yang sebenarnya sedang berjalan, tetapi sangat cepat karena melompati tumpukan jaringan (network stack).
- `self.testapp.get('/', status=200)`: Mensimulasikan request GET ke jalur root (/) dan mengharapkan kode status 200 (OK).

### 3. Assertion dan res.body
- `res.body`: Properti ini berisi seluruh konten respons HTTP (HTML) dalam bentuk bytes.
- `self.assertIn(b'<h1>Hello World!</h1>', res.body)`: Tes ini memastikan bahwa string HTML tertentu ada di dalam tubuh respons.

### 4. Ekstra Kredit: Mengapa menggunakan b''?
- `b''`: Literal string dalam format bytes (misal, b'hello').
- **Alasan:** Respons HTTP selalu dikirimkan sebagai aliran data biner. WebTest menyimpan tubuh respons di `res.body` sebagai bytes. Untuk mencocokkannya, string yang kita cari juga harus dalam format bytes.

## Kesimpulan

Langkah 06 memberikan lapisan pengujian yang penting. Dengan WebTest, kita sekarang memiliki kemampuan untuk melakukan pengujian end-to-end yang cepat dan andal, memverifikasi kualitas seluruh alur aplikasi mulai dari routing hingga konten HTML yang dikirim ke browser.
