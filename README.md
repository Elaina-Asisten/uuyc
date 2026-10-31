# uuyc — UU Remote 32-bit (armv7)

Repack 网易UU远程 (NetEase UU Remote) TV V3.5.0 agar bisa diinstal & jalan di perangkat Android **32-bit (armeabi-v7a)**.

## Latar belakang

Versi mobile resmi UU Remote (V4.x) hanya bundel native lib `arm64-v8a`, jadi tidak bisa jalan di HP 32-bit (mis. Redmi 8A Pro yang menjalankan Android armv7 userland). Native lib (`libstreamer.so` dll.) tidak bisa dikonversi arm64 → armv7.

Untungnya versi **TV (V3.5.0)** dibundel lengkap dengan `armeabi-v7a` + `arm64-v8a`, paket-nya sama (`com.netease.uuremote`), dan `minSdk 23` / `targetSdk 34`.

## Download

Lihat **[Releases](../../releases)**:

| File | Isi |
|---|---|
| `uuremote_tv32.apk` | Repack 32-bit — siap instal di HP |
| `uuremote_tv_original.apk` | APK TV asli dari https://uuyc.163.com |

## Bedah APK / temuan RE

| Versi | Paket | ABI |
|---|---|---|
| Mobile V4.45.0 | `com.netease.uuremote` | hanya `arm64-v8a` |
| TV V3.5.0 | `com.netease.uuremote` | `armeabi-v7a` + `arm64-v8a` |

Penyebab blokir "aplikasi khusus device TV" adalah manifest:

```xml
<uses-feature android:name="android.software.leanback" android:required="true"/>
```

Runtime gate di `smali/ad/g.smali` (determinasi mode TV via `leanback || television || !touchscreen`) **tidak diubah** — di HP default-nya `false` dan aplikasi tetap berfungsi.

## Repack yang dilakukan

1. `apktool d` → ubah `leanback required="true"` → `required="false"`
2. `apktool b` → `zipalign -f -p 4`
3. Sign ulang (`apksigner`, v2 scheme), keystore key baru

## Cara pakai

1. Uninstall dulu aplikasi UU Remote lama (signature repack beda dari resmi)
2. Instal `uuremote_tv32.apk`
3. Login & scan seperti biasa

## Catatan

- UI masih mirip versi TV: landscape, ada elemen D-pad — tapi berfungsi untuk remote kontrol PC dari HP
- **Jangan update via dalam aplikasi** (xupdate) — signature-nya beda. Perbarui dengan repack ulang versi terbaru
- Diverifikasi build-tools `zipalign` + `apksigner` (Android 14 / r34)
