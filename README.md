# .skora
Sebuah ide bahasa teks notasi skor musik untuk di-*render* atau disintesis. *Ini hanya ide bukan kode*, saya harap di antara pembaca ada yang mewujudkannya menjadi kode atau aplikasi yang bisa dieksekusi.

# `.skora` Dasar

`.skora` adalah bahasa teks untuk menotasikan skor musik untuk di-*render* menjadi notasi balok atau disintesis menjadi audio.

---

## 📄 **Struktur Umum File `skora`**

```skora
bpm=120
meter=\frac{4}{4}
not=12
scale=mayor
note=Contoh skor musik

section NamaOpsional {
    // Konfigurasi lokal (opsional)

    voice "NamaVoice" {
        0,1 2,1 4,1 5,1 |
        {0,1:4,1} 2,2 |
        h,1 7,2 5,1
    }
}
```

---

## ⚙️ **Header Konfigurasi (Global)**

Diletakkan di awal file dan berlaku untuk seluruh skor, kecuali jika ditimpa di dalam `section`. Bentuknya `kunci=nilai`.

- `bpm=...` → Kecepatan musik, jumlah ketukan teknis per menit (beats per minute).
- `meter=...` → Tanda birama pakai sintaks LaTeX, hanya visual.
- `not=...` → Notasi nada, dan ada dua bentuknya:
	1. Berbasis ET, dengan jumlah ET dari nilai `not`. Misalnya `not=12` berarti nada dinotasikan dengan 12-ET.
	2. Berbasis derajat dalam `scale`.
- `scale=...` → Bisa bernilai nama atau enumerasi langsung.
    - Contoh: `scale=12et[0,2,4,5,7,9,11]` untuk mayor di 12-ET.
    - Contoh: `scale=r[1,9/8,5/4,...]` untuk skala berbasis rasio.
- `note=...` → Catatan bebas (tidak perlu tanda kutip).
- `b0=...` -> Nada 0 oktaf dasar.

---

## 🧱 **Struktur Section dan Voice**

```skora
section [nama_opsional] {
    // konfigurasi lokal (opsional)

    voice "nama" {
        ... | ...
    }
}
```

- `section` dapat memiliki konfigurasi lokal yang menimpa konfigurasi global.
- `voice` harus diberi nama (dalam tanda kutip).
- Jika untuk di-*render*, `|` menjadi garis vertikal pemisah antarbar. Jika untuk sintesis, `|` diabaikan dan hanya menjadi pembantu visual saat mengedit.

---

## 🎵 **Notasi Nada**

### Format dasar:

```
notasi_nada,durasi,volume_opsional
```

- `notasi_nada`: Dalam ET jika `not={jumlah_et}`, atau derajat dalam `scale` jika `not=d`.
- `durasi`: Dalam unit ketukan teknis yang dihitung bpm (1 = *crotchet*).
- `volume` (opsional): 0–10 → 0–100%.

#### Contoh:

- `2,1` → Nada ke-2 selama 1 ketukan.
- `2,0.5,8` → Nada ke-2 selama setengah ketukan, volume 80%.

### Hening:

- `h,durasi` → Contoh: `h,1` artinya hening 1 ketukan.

### Oktaf Eksplisit

Oktaf eksplisit digunakan untuk menyatakan oktaf _di selain oktaf dasar_, baik secara absolut (A4=440Hz) maupun relatif.

#### Sintaks

Format oktaf eksplisit:

```skora
notasi_nada[indeks_oktaf],durasi,volume_opsional
```

- ​`[oktaf]`: penanda oktaf eksplisit
    - Oktaf absolut → huruf kapital mulai dari `A`:
        - `A` = oktaf 0, `B` = oktaf 1, `C` = oktaf 2, dst.
        - Contoh: `4[A]` → nada 4 di oktaf 0
    - Oktaf relatif → angka relatif terhadap oktaf dasar:
        - Misal `-2` artinya 2 oktaf di bawah oktaf dasar
        - Contoh: `9[-2]` → nada 9 dua oktaf lebih rendah dari oktaf dasar

#### Contoh

```skora
4[A],1     ; nada 4 di oktaf 0 selama 1 ketukan
9[B],0.5   ; nada 9 di oktaf 1 selama setengah ketukan
2[-1],2    ; nada 2 satu oktaf di bawah dasar selama 2 ketukan
```

#### Catatan

- Oktaf dasar secara default adalah oktaf ke-4 (`E`)
- Jika ingin menggunakan oktaf dasar berbeda, maka beri konfigurasi `b0=nilai` untuk nada 0 di oktaf tersebut, dengan nilai bisa berupa nilai frekuensi dalam Hz maupun nilai nada absolut dengan indeks oktaf eksplisit. Cth. `b0=440` atau `b0=9[E]`
- Oktaf eksplisit wajib menggunakan tanda `[]`
- Oktaf eksplisit **menggantikan** posisi default, bukan menambah atau mengurangi dari hasil normalisasi

---

## 🎼 **Polifoni *Inline***
Selain menggunakan `voice`, kita menggunakan tanda kurung kurawal `{...}` dan memisahkan dengan `:` untuk membuat polifoni:

```skora
{0,1:4,1} → Dua nada serempak, masing-masing berdurasi 1 ketukan.
```

- Setiap token boleh lebih dari satu nada (melodi independen).
- Semua nada **harus memiliki durasi eksplisit**, baik di dalam maupun di luar `{}` polifoni.

---

## 💬 **Komentar**

- Baris komentar: `// komentar`
- Komentar blok: `/* komentar */`

Catatan sebagai konfigurasi:  
`note=catatan dalam satu baris`

---

## Fungsi

Untuk memberikan artikulasi dan sebagainya, bisa menggunakan fungsi dengan bentuk `nama_fungsi(nada_yang_terlibat)`. Misalnya:
```
0,1 2,1 4,1 fungsi( 5,1 |
{0,1:4,1} 2,2 )|
h,1 7,2 5,1 |
```

---

Ini hanya dasarnya saja. Saya harap pengembang bisa mengembangkan lebih lagi, secara efektivitas, efisiensi, dan kepraktisan.
