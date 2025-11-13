# Pyramid Quick Tutorial: Langkah 11

## Dispatching URLs To Views With Routing (Pengiriman URL dengan Routing)

Langkah ini memperkenalkan fitur routing yang kuat: Replacement Patterns (pola penggantian). Ini memungkinkan Anda untuk mengekstraksi data langsung dari segmen URL, alih-alih hanya dari query string atau form data.

## Tujuan Pembelajaran
- Mendefinisikan Route yang mengandung replacement patterns (`{segment}`).
- Mengakses nilai segmen URL yang diekstrak melalui `self.request.matchdict`.
- Menggunakan data yang diekstrak tersebut untuk menghasilkan konten dinamis.

## Hasil Aplikasi Utama
Aplikasi sekarang akan memparsing dua nilai yang dilewatkan dalam URL dan menampilkannya di halaman HTML.

- **URL yang Diakses:** http://localhost:6543/howdy/amy/smith
- **Tampilan:** Halaman akan menampilkan nama depan (amy) dan nama belakang (smith) yang diambil dari URL.
- **Output:** Teks HTML akan berisi `First: amy, Last: smith`

## Perubahan Kode dan Konfigurasi

### 1. `tutorial/__init__.py`: Route dengan Replacement Patterns
Route diubah untuk menangkap dua segmen URL sebagai variabel.

```python
# ...
config.add_route('home', '/howdy/{first}/{last}')
config.scan('.views')
# ...
```
**Analisis:**
- `/howdy/{first}/{last}`: Segmen dalam kurung kurawal (`{...}`) adalah replacement patterns.
- Ketika URL seperti `/howdy/Jane/Doe` cocok, Pyramid akan menetapkan 'Jane' ke kunci 'first' dan 'Doe' ke kunci 'last'.

### 2. `tutorial/views.py`: Mengakses matchdict
View Class diubah untuk mengambil data dari objek `request.matchdict`.

```python
# ...
class TutorialViews:
    # ... (init method)

    @view_config(route_name='home')
    def home(self):
        # Mengambil nilai segmen URL yang diekstrak
        first = self.request.matchdict['first']
        last = self.request.matchdict['last']
        return {
            'name': 'Home View',
            'first': first,
            'last': last
        }
```
**Analisis:**
- `self.request.matchdict`: Dictionary yang diisi Pyramid dari replacement patterns di URL.
- View mengembalikan data ini ke renderer.

### 3. `tutorial/home.pt`: Menggunakan Data URL
Template diubah untuk merender nilai yang diambil dari matchdict.

```html
<body>
  <h1>${name}</h1>
  <p>First: ${first}, Last: ${last}</p>
</body>
```

### 4. Pembaruan Pengujian
- **Unit Test:** DummyRequest kini harus diisi manual dengan data di `request.matchdict` sebelum memanggil view.
- **Functional Test:** WebTest memanggil URL `/howdy/Jane/Doe` dan memastikan body respons mengandung data tersebut.

## Analisis Konsep Utama
1. **Peran request.matchdict**
   - Ekstraksi Data URL: Cara utama Pyramid untuk mengambil bagian variabel dari URL (misal ID, slug, nama).
   - Data ini berbeda dengan query string dan lebih terstruktur.
2. **Keuntungan Routing eksplisit**
   - Pendaftaran Route (`config.add_route`): Mendefinisikan pola dan urutan pencocokan.
   - Konfigurasi View (`@view_config`): Menentukan view mana yang dipanggil.
   - Dua langkah ini memberi kontrol eksplisit atas urutan pencocokan route, penting untuk mencegah ambiguitas.

## Kesimpulan
Langkah 11 berhasil mendemonstrasikan bagaimana Pyramid mendukung URL desain yang canggih dan RESTful. Dengan replacement patterns dan `request.matchdict`, view dapat mengambil informasi penting langsung dari struktur URL.
