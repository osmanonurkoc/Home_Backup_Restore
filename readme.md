# 📂 Home Backup & Restore Utility (v11.4)

Windows için tamamen PowerShell ile yazılmış, modern WPF arayüzüne sahip, hafif, güçlü ve akıllı bir yedekleme aracı. Her yedekleme anlık görüntüsünü (snapshot) tamamen erişilebilir tutarken disk alanından tasarruf etmek için NTFS Hard Link (Sabit Bağlantı) teknolojisini kullanan **"Time Machine" tarzı artırımlı yedekleme** özelliğine sahiptir.

[![Download Latest Release](https://img.shields.io/badge/Download-Latest_Release-2ea44f?style=for-the-badge&logo=github&logoColor=white)](https://github.com/osmanonurkoc/HomeBackup/releases/latest)

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Windows-0078D4.svg)
![PowerShell](https://img.shields.io/badge/PowerShell-v5.1%2B-5391FE.svg)

## 📸 Ekran Görüntüleri

![Application Interface](images/screenshot1.png) ![Application Interface](images/screenshot2.png) ![Application Interface](images/screenshot3.png) ![Application Interface](images/screenshot4.png)

*Windows için PowerShell & WPF ile oluşturulmuş akıllı, artırımlı yedekleme aracı. Depolama verimliliği için NTFS sabit bağlantıları, otomatik karanlık mod ve modern bir GUI içerir.*

## ✨ Temel Özellikler

* **🚀 Akıllı Artırımlı Yedekleme (Turbo Mod):** NTFS Hard Link teknolojisini kullanır. Bir dosya son yedeklemeden bu yana değişmediyse, çoğaltmak yerine eski dosyaya bağlantı verilir. Bu, disk alanından ve zamandan büyük tasarruf sağlar.
* **⚡ Diferansiyel (Fark) Geri Yükleme:** Geri yükleme sırasında hedefteki dosya ile yedekteki dosya aynıysa (boyut ve zaman damgası), üzerine yazma işlemi atlanır. Bu sayede geri yükleme işlemleri çok daha hızlı gerçekleşir.
* **🛡️ Güvenli & Hedefli Aynalama (Safe Mirroring):** Geri yükleme işlemi artık bağlam duyarlıdır. Sadece yedeğin kapsadığı klasörler içindeki fazlalık dosyaları temizler. Üst dizinlerdeki (örneğin Home klasöründeki) ilgisiz dosyalara asla dokunmaz.
* **👻 Hayalet Klasör Temizliği (Ghost Folder Cleanup):** Windows tarafından `desktop.ini` veya özel simgelerle kilitlenmiş inatçı boş klasörleri algılar, kilitlerini açar ve temizleyerek hedefi tam olarak yedekle eşleştirir.
* **🎨 Modern UI & Tema Motoru:** Windows temanızı (Açık/Koyu mod) otomatik olarak algılar ve arayüzü gerçek zamanlı olarak ayarlar.
* **🛡️ Gelişmiş Hariç Tutma Sistemi:** Güçlü JSON tabanlı filtreleme desteği. Dosyaları uzantıya, belirli klasörlere, genel klasör adlarına veya belirli dosya yollarına göre yedeğe dahil etmeyin.
* **📂 Geri Yükleme Yöneticisi:** Önceki yedeklere doğrudan arayüzden göz atın, yeniden adlandırın, silin veya geri yükleyin.

## 🚀 Başlarken

### Gereksinimler
* **OS:** Windows 10 veya Windows 11.
* **Dosya Sistemi:** Yedekleme hedef sürücüsü, Hard Link (Sabit Bağlantı) desteği için **NTFS olarak biçimlendirilmiş olmalıdır**.

### Kurulum ve Kullanım

#### Seçenek 1: Uygulamayı Kullanma (Önerilen)
1. En son `HomeBackup.exe` dosyasını **[Releases Sayfasından](https://github.com/osmanonurkoc/HomeBackup/releases/latest)** indirin.
2. Bir klasöre yerleştirin (örn. `C:\Tools\HomeBackup`).
3. Çalıştırmak için `HomeBackup.exe`ye çift tıklayın.
   * *Not:* İlk çalıştırma, aynı klasörde yapılandırma dosyalarını (`settings.json`, `exclude_list.json`) oluşturacaktır.

#### Seçenek 2: Scripti Çalıştırma (Geliştiriciler İçin)
1. Kaynak kodunu indirin.
2. `HomeBackup.ps1` dosyasına sağ tıklayın ve **Run with PowerShell** (PowerShell ile Çalıştır) seçeneğini seçin.
   * *Not:* Execution Policy hatası alırsanız, PowerShell'de şu komutu bir kez çalıştırın:
     ```powershell
     Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
     ```

## 🛠️ Nasıl Çalışır?

### Yedekleme (Turbo Mod)
Bu araç, dosya sistemi düzeyinde tekilleştirme (deduplication) yapmak için Windows Kernel32 API (`CreateHardLink`) kullanır:
1.  **İlk Yedekleme:** Seçilen tüm dosyaları normal şekilde kopyalar.
2.  **Sonraki Yedeklemeler:**
    * Araç, dosyaları *önceki* yedekleme anlık görüntüsüyle karşılaştırır.
    * **Dosya değişmişse (veya yeniyse):** Yeni dosyayı fiziksel olarak kopyalar.
    * **Dosya değişmemişse:** Tekrar kopyalamak yerine, diskteki mevcut veriye işaret eden bir **"Hard Link"** oluşturur.
    * **Sonuç:** Her yedekleme klasörü tam bir yedekleme gibi görünür ve davranır (eskileri silebilirsiniz, yeniler bozulmaz), ancak değişmeyen dosyalar için **neredeyse sıfır ekstra alan** kaplar.

### Geri Yükleme (Restore)
1. **Analiz:** Yedek ile hedef klasörü karşılaştırır.
2. **Akıllı Karar:** Aynı olan dosyaları atlar (Differential Restore).
3. **Temizlik:** Yedekte olmayan dosyaları hedef klasörden siler, ancak bunu yaparken sadece ilgili alt klasörleri hedefler (Safe Mirroring).
4. **Kilit Kırma:** `desktop.ini` nedeniyle silinemeyen boş klasörlerin özniteliklerini (Attributes) sıfırlar ve temizler.

## ⚙️ Yapılandırma (Hariç Tutma)

`exclude_list.json` dosyasını düzenleyerek tam olarak nelerin atlanacağını özelleştirebilirsiniz.

**Örnek `exclude_list.json`:**
```json
{
  "GlobalExtensions": [".tmp", ".log", ".bak", ".dmp", ".vdi", ".vmdk"],
  "GlobalFolders": ["node_modules", "bin", "obj", ".git", ".vs", "AppData", "cache"],
  "IgnoredSystemFolders": ["$RECYCLE.BIN", "System Volume Information", "OneDriveTemp"],
  "FolderSpecific": {
    "Downloads": [".exe", ".msi"],
    "Videos": [".srt"]
  },
  "IgnoredSpecificFolders": [
    "%UserProfile%\\Desktop\\VirtualSpace"
  ],
  "IgnoredFiles": [
    "%UserProfile%\\Desktop\\SpecificApp.exe",
    "%UserProfile%\\Documents\\LargeLogFile.txt"
  ]
}
```

## ⚠️ Disclaimer
This tool modifies system files (`hosts`) and network settings. While it includes safety features (backups and smart merging), **use it at your own risk**. 
- Always ensure you have a system restore point if you are unsure.
- VPNs or other security software might conflict with DNS settings.

## 📄 License
This project is licensed under the [MIT License](LICENSE).

---
*Created by [@osmanonurkoc](https://github.com/osmanonurkoc)*
