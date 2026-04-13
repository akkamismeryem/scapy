# 🐍 Scapy Kurulum Rehberi

Bu rehberde Scapy'nin kurulumunu ve gerekli tüm bileşenleri öğreneceksin.

---

## 📌 Genel Adımlar

1. Python kur
2. Scapy kur
3. libpcap / libdnet kur (Windows için)
4. Ek paketleri yükle (isteğe bağlı)
5. Admin/root olarak çalıştır

---

## 🔢 Scapy Versiyonları

### 🔹 Scapy v1.x
- Tek dosya
- Python 2 ile çalışır
- Eski versiyon

### 🔹 Scapy v2.x
- Python 3 ile çalışır
- Günümüzde kullanılan versiyon
- IPv6 ve gelişmiş özellikler içerir

---

## 🚀 Scapy v2.x Kurulumu (Linux / Unix)

Aşağıdaki adımlar Linux, BSD ve macOS için geçerlidir.

### 📥 Kurulum

```bash
cd /tmp
wget scapy.net
unzip scapy-latest.zip
cd scapy-2.*
sudo python setup.py install
```

---

## ⚡ Alternatif Kurulum Yöntemleri

### 1. Zip dosyasını direkt çalıştırma

```bash
chmod +x scapy-latest.zip
sudo ./scapy-latest.zip
```

### 2. Shell ile çalıştırma

```bash
sudo sh scapy-latest.zip
```

### 3. Komut gibi kullanma

```bash
mv scapy-latest.zip /usr/local/bin/scapy
sudo scapy
```

---

## ⚠️ Not

Zip dosyasını çalıştırılabilir yapmak için başına özel byte eklenir.  
Her zip programı bunu desteklemeyebilir.

---

## 🧪 Scapy v1.2 Kurulumu

```bash
wget http://hg.secdev.org/scapy/raw-file/v1.2.0.2/scapy.py
sudo python scapy.py
```

---

## 🪟 Windows Kurulumu

### ✔ Npcap yükle

https://npcap.com/

Kurulum sırasında:

- WinPcap API compatible mode açık olsun

---

### ✔ Scapy yükle

```bash
pip install scapy
```

---

## 🧩 Ek Paketler (İsteğe Bağlı)

---

### 📊 Grafik çizme (plot)

Gerekli:

- GnuPlot
- NumPy

```python
p = sniff(count=50)
p.plot(lambda x: len(x))
```

---

### 📄 PDF / PS çıktısı (pdfdump, psdump)

Gerekli:

- PyX

```python
p = IP()/ICMP()
p.pdfdump("test.pdf")
```

---

### 🧭 Ağ diyagramı (conversations)

Gerekli:

- Graphviz
- ImageMagick

```python
p = readpcap("myfile.pcap")
p.conversations(type="jpg", target="> test.jpg")
```

---

### 🌐 3D analiz (trace3D)

Gerekli:

- VPython

```python
a, u = traceroute(["www.python.org", "google.com","slashdot.org"])
a.trace3D()
```

---

### 🔐 WEP çözme (unwep)

Gerekli:

- PyCrypto

```python
enc = rdpcap("weplab-64bit-AA-managed.pcap")
enc.show()

conf.wepkey = "AA\x00\x00\x00"

dec = Dot11PacketList(enc).toEthernet()
dec.show()
```

---

### 🔎 Fingerprinting (nmap_fp)

Gerekli:

- Nmap

```python
load_module("nmap")
nmap_fp("192.168.0.1")
```

---

### 📞 VOIP (voip_play)

Gerekli:

- SoX

```python
voip_play("dosya.pcap")
```

---

## 🔐 Root / Admin Yetkisi

### Linux / macOS

```bash
sudo scapy
```

### Windows

Terminali **Yönetici olarak çalıştır**

---

## ✅ Test

```bash
python
```

```python
from scapy.all import *
pkt = IP(dst="8.8.8.8")/ICMP()
pkt.show()
```

---

## 🎯 Özet

✔ Python kur  
✔ Scapy kur  
✔ Npcap / libpcap  
✔ Ek paketler  
✔ Admin çalıştır  
