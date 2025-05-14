# 📦 Install Script Backup Mikrotik

[![MikroTik](https://img.shields.io/badge/RouterOS-6.x%20%7C%207.x-blue)](https://mikrotik.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Script otomatis untuk melakukan backup konfigurasi perangkat **MikroTik** dan mengunggahnya ke server FTP(khusus yang sudah diinstall oleh kangsigi). Mendukung **RouterOS versi 6.x dan 7.x**.

---

## 📌 Fitur

- Mengekspor konfigurasi perangkat ke file `.rsc`
- Membuat file `.backup` perangkat
- Upload otomatis ke server FTP
- Hapus file lokal setelah upload
- Scheduler otomatis setiap hari pukul 00:00

---

## ⚙️ Script untuk RouterOS 6.x

```mikrotik
:local sysname [/system identity get name]
:local textfilename
:local backupfilename
:local time [/system clock get time]
:local date [/system clock get date]
:local newdate ""

# Format tanggal (hapus tanda "/")
:for i from=0 to=([:len $date] - 1) do={
    :local tmp [:pick $date $i]
    :if ($tmp != "/") do={ :set newdate "$newdate$tmp" }
}

# Ganti spasi pada nama sistem dengan underscore
:if ([:find $sysname " "] != 0) do={
    :local name $sysname
    :local newname ""
    :for i from=0 to=([:len $name] - 1) do={
        :local tmp [:pick $name $i]
        :if ($tmp != " ") do={ :set newname "$newname$tmp" }
        :if ($tmp = " ") do={ :set newname "$newname_" }
    }
    :set sysname $newname
}

# Buat nama file berdasarkan tanggal dan nama sistem
:set textfilename ($newdate . "-" . $sysname . ".rsc")
:set backupfilename ($newdate . "-" . $sysname . ".backup")

# Ekspor konfigurasi dan buat backup
:execute [/export file=$textfilename]
:execute [/system backup save name=$backupfilename]

# Tunggu proses selesai
:delay 2s

# Upload ke server FTP
tool fetch url="ftp://IPSERVER/backup_rsc/$textfilename" src-path=$textfilename user=USER_FTP password=PASS_FTP port=21 upload=yes
tool fetch url="ftp://IPSERVER/backup/$backupfilename" src-path=$backupfilename user=USER_FTP password=PASS_FTP port=21 upload=yes

# Tunggu pengiriman selesai
:delay 5s

# Hapus file lokal setelah upload
/file remove $textfilename
/file remove $backupfilename
```

## ⚙️ Script untuk RouterOS 7.x
```mikrotik
:local sysname [/system identity get name]
:local textfilename
:local backupfilename
:local time [/system clock get time]
:local date [/system clock get date]
:local newdate ""

# Format tanggal (hapus tanda "/")
:for i from=0 to=([:len $date] - 1) do={
    :local tmp [:pick $date $i]
    :if ($tmp != "/") do={ :set newdate "$newdate$tmp" }
}

# Ganti spasi pada nama sistem dengan underscore
:if ([:find $sysname " "] != 0) do={
    :local name $sysname
    :local newname ""
    :for i from=0 to=([:len $name] - 1) do={
        :local tmp [:pick $name $i]
        :if ($tmp != " ") do={ :set newname "$newname$tmp" }
        :if ($tmp = " ") do={ :set newname "$newname_" }
    }
    :set sysname $newname
}

# Buat nama file berdasarkan tanggal dan nama sistem
:set textfilename ($newdate . "-" . $sysname . ".rsc")
:set backupfilename ($newdate . "-" . $sysname . ".backup")

# Ekspor konfigurasi dan buat backup
/export file=$textfilename
/system backup save name=$backupfilename

# Tunggu proses selesai
:delay 2s

# Upload ke server FTP
/tool fetch url="ftp://IPSERVER/backup_rsc/$textfilename" src-path=$textfilename user=USER_FTP password=PASS_FTP port=21 upload=yes
/tool fetch url="ftp://IPSERVER/backup/$backupfilename" src-path=$backupfilename user=USER_FTP password=PASS_FTP port=21 upload=yes

# Tunggu pengiriman selesai
:delay 5s

# Hapus file lokal setelah upload
/file remove $textfilename
/file remove $backupfilename
```

## 📄 Penamaan Script

Simpan script dengan nama script-backup

⚠️ Pastikan nama script persis sama (huruf besar dan kecil harus sesuai).
⏰ Menambahkan Scheduler (Jadwal Backup Otomatis)

Jalankan perintah berikut di Terminal MikroTik untuk menjadwalkan backup otomatis setiap hari pukul 00:00:00
```mikrotik
/system scheduler
add name="daily-backup" start-time=00:00:00 interval=1d on-event="/system script run script-backup" policy=ftp,read,write,policy,test,password,sniff,sensitive,romon
```

✅ Selesai

Backup otomatis perangkat MikroTik kamu sekarang telah aktif!
Jangan lupa untuk mengganti bagian berikut pada script sesuai kebutuhan:

Parameter	Keterangan

IPSERVER	Alamat IP FTP Server (wajib dirubah)

USER_FTP	Username FTP Server (wajib dirubah)

PASS_FTP	Password FTP Server (wajib dirubah)

## 🧪 Tested On

MikroTik RouterOS 6.49.10

MikroTik RouterOS 7.12

## 📜 License

This project is licensed under the MIT License.

## 🙌 Kontribusi

Pull request sangat terbuka untuk perbaikan script, efisiensi, atau pengembangan fitur baru.
