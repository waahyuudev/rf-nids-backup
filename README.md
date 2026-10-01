# RF-NIDS Thesis Backup Documentation

Dokumentasi ini menjelaskan backup final penelitian **Random Forest Network Intrusion Detection System (RF-NIDS)** yang dibuat untuk menjaga source baseline, database, model Random Forest, evidence penelitian, dan konfigurasi penting agar dapat dipulihkan apabila environment utama mengalami kerusakan atau kehilangan data.

---

## 1. Informasi Backup

| Item          | Nilai                                      |
| ------------- | ------------------------------------------ |
| Project       | RF-NIDS                                    |
| Backup Date   | 1 Oktober 2026                             |
| Source Branch | `development`                              |
| Frozen Commit | `d14a821c76865f289a7d8b4c82271125f15a0c90` |
| Runtime Model | RF-v5 (`rf-v5-candidate-01`)               |
| Database      | PostgreSQL `rf_nids`                       |
| Backup Format | `tar.gz`                                   |
| Backup Status | **VERIFIED**                               |

Archive utama:

```text
rf-nids-thesis-freeze-2026-10-01.tar.gz
```

Lokasi backup pada Mac saat dibuat:

```text
~/Documents/rf-nids-backup/rf-nids-thesis-freeze-2026-10-01.tar.gz
```

SHA256 archive:

```text
b985d45706e9193b14ee066a4c023acf6294516169017f600ddea9780e9b9407
```

Archive telah diverifikasi setelah dipindahkan dari VM `ubuntu-nids` ke Mac dan menghasilkan hash yang identik.

---

## 2. Tujuan Backup

Backup dibuat sebagai **recovery point penelitian**.

Backup dapat digunakan apabila terjadi kondisi seperti:

- VM `ubuntu-nids` rusak atau terhapus.
- Database PostgreSQL mengalami kerusakan.
- Artifact model RF-v5 hilang.
- Evidence hasil eksperimen hilang.
- Environment penelitian perlu dibangun kembali.
- Penelitian perlu diverifikasi kembali berdasarkan artifact yang digunakan saat penyusunan skripsi.

Backup ini tidak dimaksudkan sebagai snapshot penuh sistem operasi Ubuntu.

---

## 3. Isi Backup

Struktur utama backup:

```text
rf-nids-thesis-freeze/
├── config/
│   ├── .env.example
│   ├── docker-compose.yml
│   ├── docker/
│   ├── feature_contract/
│   └── migrations/
│
├── database/
│   └── rf_nids_final.dump
│
├── evidence/
│   └── experiment_f/
│       ├── normal_remediation_a2/
│       ├── rf_v5_candidate_01/
│       └── runtime_demo/
│           └── rf_v5_sessions_20_22/
│
├── manifests/
│   ├── environment.txt
│   ├── pip-freeze.txt
│   └── runtime-config.txt
│
├── models/
│   └── experiment_f/
│       ├── random_forest_rf_v5_candidate_01.joblib
│       └── random_forest_rf_v5_candidate_01_metadata.json
│
├── RESTORE.md
└── SHA256SUMS.txt
```

---

## 4. Source Code Baseline

Source code final penelitian dibekukan pada:

```text
Branch : development
Commit : d14a821c76865f289a7d8b4c82271125f15a0c90
```

Source code utama tidak diduplikasi seluruhnya ke dalam archive karena repository telah disimpan melalui Git.

Untuk memperoleh source yang sama:

```bash
git clone https://github.com/waahyuudev/rf-nids.git
cd rf-nids

git checkout d14a821c76865f289a7d8b4c82271125f15a0c90
```

Verifikasi:

```bash
git rev-parse HEAD
git status
```

Commit harus menghasilkan:

```text
d14a821c76865f289a7d8b4c82271125f15a0c90
```

---

## 5. Database Backup

Database PostgreSQL final disimpan pada:

```text
database/rf_nids_final.dump
```

Database:

```text
rf_nids
```

Backup dibuat menggunakan PostgreSQL Custom Archive Format (`pg_dump -Fc`).

SHA256 database dump saat proses freeze:

```text
673e21e8cb403d1edc1c4905ef12f7c9595452ffdc68d005021429f25801eb83
```

Database dump telah diverifikasi menggunakan `pg_restore -l`.

Contoh restore pada **recovery environment**:

```bash
cat database/rf_nids_final.dump | \
docker compose exec -T postgres \
pg_restore -U postgres -d rf_nids --clean --if-exists
```

> **WARNING:** Jangan menjalankan proses restore terhadap database penelitian asli yang masih aktif. Restore sebaiknya dilakukan pada recovery/test environment.

---

## 6. Frozen RF-v5 Model

Model yang digunakan untuk runtime testing skripsi adalah:

```text
rf-v5-candidate-01
```

Artifact:

```text
models/experiment_f/random_forest_rf_v5_candidate_01.joblib
```

Metadata:

```text
models/experiment_f/random_forest_rf_v5_candidate_01_metadata.json
```

SHA256 model:

```text
31d7d50fa79e3400e7d357cab05c780e038bc527b746d0be6ff894828c6b8d17
```

Verifikasi:

```bash
sha256sum models/experiment_f/random_forest_rf_v5_candidate_01.joblib
```

Hash harus sama dengan nilai di atas.

### Scientific Status

RF-v5 tetap memiliki status:

```text
CANDIDATE / NOT_ACTIVE
```

Scientific decision:

```text
RF_V5_VALIDATION_FAIL
```

RF-v5 **tidak dipromosikan menjadi default active model**.

Pada pengujian runtime untuk skripsi, RF-v5 dipilih menggunakan mekanisme:

```text
MANUAL / DEMO SELECTION
```

Runtime demonstration tidak mengubah scientific status model.

---

## 7. Scientific Evidence

Evidence scientific RF-v5 tersimpan pada:

```text
evidence/experiment_f/rf_v5_candidate_01/
```

dan:

```text
evidence/experiment_f/normal_remediation_a2/
```

Evidence tersebut mencakup antara lain:

- scientific freeze;
- dependency verification;
- RF-v5 training audit;
- validation predictions;
- confusion matrix;
- comparative metrics;
- acceptance gate evaluation;
- scientific summary.

File evidence harus dipertahankan dalam kondisi asli dan tidak dimodifikasi.

---

## 8. Runtime Evidence

Runtime evidence final tersimpan pada:

```text
evidence/experiment_f/runtime_demo/rf_v5_sessions_20_22/
```

Evidence mencakup tiga sesi utama.

### Session 20 — Normal HTTP

```text
Flow       : 58
Prediction : 58
Normal     : 46
PortScan   : 12
Alert      : 12 MEDIUM
```

Sebanyak 46 dari 58 flow diklasifikasikan sebagai Normal, sedangkan 12 flow diklasifikasikan sebagai PortScan.

### Session 21 — Controlled DDoS-like HTTP Load

```text
Flow       : 535
Prediction : 535
DDoS       : 453
Normal     : 82
Alert      : 453 HIGH
```

Skenario ini merupakan controlled HTTP load profile pada virtual lab dan tidak dimaksudkan sebagai bukti pengujian DDoS terdistribusi pada lingkungan dunia nyata.

### Session 22 — Bounded PortScan

```text
Flow       : 1000
Prediction : 1000
PortScan   : 1000
Alert      : 1000 MEDIUM
```

Terdapat catatan provenance pada Session 22:

```text
Persisted monitoring target : 10.10.10.2
Actual bounded Nmap target   : 10.10.20.2
```

Perbedaan tersebut dipertahankan sebagaimana evidence asli dan tidak boleh dinormalisasi dengan mengubah historical evidence.

---

## 9. Feature Contract

RF-NIDS menggunakan kontrak **78 fitur CICFlowMeter V3**.

Evidence compatibility dan mapping disimpan pada:

```text
config/feature_contract/
```

File penting:

```text
cicflowmeter_v3_78_feature_crosswalk.csv
live_feature_compatibility.json
live_feature_mapping_audit.json
```

File tersebut digunakan untuk mempertahankan provenance hubungan antara output flow extractor dan input feature model Random Forest.

---

## 10. Runtime Configuration

Konfigurasi environment disimpan dalam:

```text
config/.env.example
manifests/runtime-config.txt
```

Runtime configuration saat freeze mencatat antara lain:

```text
CICFLOWMETER_V3_IMAGE
CICFLOWMETER_V3_IMAGE_DIGEST
DOCKER_SOCKET_GID
RUNTIME_MONITORING_HOST_ROOT
```

Beberapa konfigurasi bersifat host-specific.

Contohnya:

```text
DOCKER_SOCKET_GID
RUNTIME_MONITORING_HOST_ROOT
```

Nilai tersebut dapat perlu disesuaikan ketika sistem dipulihkan pada host atau VM baru.

### CICFlowMeter Provenance

Current runtime configuration dan historical runtime evidence dapat mencatat digest CICFlowMeter yang berbeda.

Historical evidence **tidak boleh diubah** untuk menyesuaikannya dengan konfigurasi environment yang lebih baru.

Kedua informasi harus dipertahankan sesuai provenance masing-masing.

---

## 11. Environment Manifest

Informasi environment pada saat freeze tersedia pada:

```text
manifests/environment.txt
```

Manifest mencatat antara lain:

```text
Git branch
Git commit
Docker Compose version
Docker containers
Docker images
Python version
Pip version
```

Python dependency snapshot tersedia pada:

```text
manifests/pip-freeze.txt
```

---

## 12. Integrity Verification

Seluruh file di dalam freeze memiliki checksum pada:

```text
SHA256SUMS.txt
```

Pada Linux:

```bash
sha256sum -c SHA256SUMS.txt
```

Pada macOS:

```bash
shasum -a 256 -c SHA256SUMS.txt
```

Kondisi backup valid apabila seluruh file menghasilkan:

```text
OK
```

dan tidak terdapat:

```text
FAILED
```

Pada 1 Oktober 2026, seluruh file berhasil melewati integrity verification setelah archive dipindahkan dari `ubuntu-nids` ke Mac.

---

## 13. Archive Verification

Untuk memverifikasi archive:

### macOS

```bash
shasum -a 256 rf-nids-thesis-freeze-2026-10-01.tar.gz
```

Expected SHA256:

```text
b985d45706e9193b14ee066a4c023acf6294516169017f600ddea9780e9b9407
```

Untuk melakukan full verification:

```bash
mkdir verify

tar -xzf rf-nids-thesis-freeze-2026-10-01.tar.gz \
  -C verify

cd verify/rf-nids-thesis-freeze

shasum -a 256 -c SHA256SUMS.txt
```

---

## 14. Recovery Overview

Jika VM penelitian hilang atau rusak, recovery secara umum dilakukan dengan alur:

```text
Prepare Ubuntu / Recovery VM
        ↓
Clone RF-NIDS Repository
        ↓
Checkout Frozen Commit
        ↓
Restore Environment Configuration
        ↓
Build / Start Docker Services
        ↓
Restore PostgreSQL Database
        ↓
Restore Exact RF-v5 Artifact
        ↓
Verify RF-v5 SHA256
        ↓
Restore / Verify Scientific Evidence
        ↓
Restore / Verify Runtime Evidence
        ↓
Verify Application
```

Instruksi recovery tambahan tersedia pada:

```text
RESTORE.md
```

---

## 15. Batasan Backup

Backup ini **bukan full VM snapshot**.

Backup tidak menjamin pemulihan otomatis terhadap:

- instalasi sistem operasi Ubuntu;
- konfigurasi UTM;
- virtual network UTM;
- network interface host;
- seluruh package level sistem operasi;
- konfigurasi host di luar project RF-NIDS.

Jika VM hilang total, environment Ubuntu dan virtual network perlu dipersiapkan kembali sebelum project direstore.

Untuk perlindungan tambahan, snapshot atau clone VM UTM dapat disimpan secara terpisah.

---

## 16. Backup Policy

Archive berikut merupakan **frozen research backup**:

```text
rf-nids-thesis-freeze-2026-10-01.tar.gz
```

File tersebut sebaiknya:

1. Tidak dimodifikasi.
2. Tidak diekstrak lalu ditimpa kembali sebagai archive yang sama.
3. Tidak digunakan sebagai working directory.
4. Tidak dihapus setelah penelitian selesai.
5. Disimpan minimal pada dua lokasi fisik/logis yang berbeda.
6. Diverifikasi menggunakan SHA256 setelah dipindahkan.

Jika penelitian dilanjutkan dan menghasilkan perubahan baru, buat backup baru dengan nama/tanggal berbeda daripada menimpa freeze ini.

---

## 17. Final Verification Status

```text
RF-NIDS THESIS FREEZE
=====================

Source baseline : VERIFIED
Database backup : VERIFIED
RF-v5 model     : VERIFIED
Scientific data : VERIFIED
Runtime evidence: VERIFIED
Configuration   : VERIFIED
Feature contract: VERIFIED
RESTORE.md      : VERIFIED
Internal SHA256 : VERIFIED
Off-VM copy     : VERIFIED
Mac extraction  : VERIFIED

FINAL STATUS:
RF-NIDS THESIS BACKUP VERIFIED
```

### Frozen Source

```text
d14a821c76865f289a7d8b4c82271125f15a0c90
```

### RF-v5 SHA256

```text
31d7d50fa79e3400e7d357cab05c780e038bc527b746d0be6ff894828c6b8d17
```

### Database SHA256

```text
673e21e8cb403d1edc1c4905ef12f7c9595452ffdc68d005021429f25801eb83
```

### Final Archive SHA256

```text
b985d45706e9193b14ee066a4c023acf6294516169017f600ddea9780e9b9407
```

---

**RF-NIDS Thesis Research Backup**  
**Freeze Date: 1 October 2026**
# rf-nids-backup
