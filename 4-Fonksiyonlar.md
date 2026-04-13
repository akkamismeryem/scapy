# 🐍 Scapy Fonksiyonları Rehberi

Bu doküman, Scapy’de en çok kullanılan fonksiyonları **işlevsel ve pratik odaklı** olarak açıklar.  
Amaç: Hangi fonksiyon ne işe yarar + ne zaman kullanılır + nasıl kullanılır.

---

# 📌 İçindekiler

1. Paket oluşturma fonksiyonları
2. Paket gönderme fonksiyonları
3. Paket yakalama (sniff) fonksiyonları
4. Paket inceleme fonksiyonları
5. PCAP işlemleri
6. Katman ve alan erişimi
7. Filtreleme ve işleme
8. Yardımcı fonksiyonlar
9. Örnek kullanım senaryoları
10. Kısa özet (cheat sheet)

---

# 1. 📦 Paket Oluşturma Fonksiyonları

## `IP()`
IP katmanı oluşturur.

```python
pkt = IP(dst="8.8.8.8")
```

---

## `ICMP()`
ICMP (ping) paketi oluşturur.

```python
pkt = IP(dst="8.8.8.8")/ICMP()
```

---

## `TCP()`
TCP katmanı oluşturur.

```python
pkt = IP(dst="1.1.1.1")/TCP(dport=80, flags="S")
```

### Önemli parametreler:
- `dport` → hedef port
- `sport` → kaynak port
- `flags` → SYN, ACK, FIN vb.

---

## `UDP()`
UDP paketi oluşturur.

```python
pkt = IP(dst="8.8.8.8")/UDP(dport=53)
```

---

## `ARP()`
ARP paketi oluşturur.

```python
pkt = ARP(pdst="192.168.1.1")
```

---

## `Ether()`
Ethernet katmanı oluşturur.

```python
pkt = Ether(dst="ff:ff:ff:ff:ff:ff")
```

---

## `Raw()`
Ham veri ekler.

```python
pkt = IP()/TCP()/Raw(load="Hello")
```

---

# 2. 🚀 Paket Gönderme Fonksiyonları

## `send()`
Layer 3 seviyesinde paket gönderir.

```python
send(IP(dst="8.8.8.8")/ICMP())
```

---

## `sendp()`
Layer 2 seviyesinde paket gönderir.

```python
sendp(Ether()/ARP(pdst="192.168.1.1"))
```

---

## `sr()`
Paket gönderir ve tüm cevapları toplar.

```python
ans, unans = sr(IP(dst="8.8.8.8")/ICMP(), timeout=2)
```

---

## `sr1()`
Tek cevap bekler.

```python
reply = sr1(IP(dst="8.8.8.8")/ICMP(), timeout=2)
```

---

## `srp()`
Layer 2 seviyesinde gönder + cevap al.

```python
srp(Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst="192.168.1.0/24"))
```

---

## `srp1()`
Layer 2 tek cevap al.

```python
srp1(Ether()/ARP(pdst="192.168.1.1"))
```

---

# 3. 📡 Paket Yakalama (Sniff)

## `sniff()`
Ağ trafiğini dinler.

```python
packets = sniff(count=5)
```

---

## Parametreler

### `count`
Kaç paket yakalanacağını belirler.

```python
sniff(count=10)
```

---

### `timeout`
Süre ile dinleme

```python
sniff(timeout=5)
```

---

### `iface`
Ağ arayüzü seçme

```python
sniff(iface="Ethernet", count=5)
```

---

### `filter`
BPF filtresi

```python
sniff(filter="icmp", count=5)
```

---

### `prn`
Her paket geldiğinde çalışacak fonksiyon

```python
sniff(prn=lambda x: x.summary())
```

---

### `store`
Paketleri bellekte tutma

```python
sniff(store=0)
```

---

# 4. 🔍 Paket İnceleme Fonksiyonları

## `show()`
Paketi detaylı gösterir.

```python
pkt.show()
```

---

## `summary()`
Kısa özet verir.

```python
pkt.summary()
```

---

## `hexdump()`
Hex formatında gösterir.

```python
hexdump(pkt)
```

---

## `raw()`
Ham byte verisini verir.

```python
raw(pkt)
```

---

# 5. 💾 PCAP İşlemleri

## `rdpcap()`
PCAP dosyasını okur.

```python
packets = rdpcap("file.pcap")
```

---

## `wrpcap()`
PCAP dosyasına yazar.

```python
wrpcap("out.pcap", packets)
```

---

# 6. 🧱 Katman ve Alan Erişimi

## Katman erişimi

```python
pkt[IP]
pkt[TCP]
```

---

## Alan okuma

```python
pkt[IP].src
pkt[IP].dst
```

---

## Katman kontrolü

```python
pkt.haslayer(ICMP)
```

---

# 7. ⚙️ Filtreleme ve İşleme

## Lambda ile işlem

```python
sniff(prn=lambda pkt: pkt.summary())
```

---

## Fonksiyon ile işlem

```python
def process(pkt):
    if pkt.haslayer(IP):
        print(pkt[IP].src)

sniff(prn=process)
```

---

## Filtre + işlem

```python
sniff(filter="tcp", prn=lambda pkt: pkt.summary())
```

---

# 8. 🧰 Yardımcı Fonksiyonlar

## `ls()`
Katmanın tüm alanlarını gösterir.

```python
ls(IP)
ls(TCP)
```

---

## `show_interfaces()`
Ağ arayüzlerini listeler.

```python
show_interfaces()
```

---

## `conf`
Scapy ayarları

```python
conf.iface
```

---

# 9. 🧪 Uygulamalı Örnekler

---

## ICMP gönder + cevap al

```python
reply = sr1(IP(dst="8.8.8.8")/ICMP(), timeout=2)
reply.show()
```

---

## ICMP sniff

```python
sniff(filter="icmp", prn=lambda pkt: pkt.summary(), count=5)
```

---

## ARP scan

```python
packet = Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst="192.168.1.0/24")
ans, _ = srp(packet, timeout=2)

for s, r in ans:
    print(r.psrc, r.hwsrc)
```

---

## TCP SYN gönder

```python
pkt = IP(dst="scanme.nmap.org")/TCP(dport=80, flags="S")
sr1(pkt)
```

---

## PCAP oku

```python
packets = rdpcap("test.pcap")
packets.summary()
```

---

# 10. ⚡ Cheat Sheet

## En önemli fonksiyonlar

| Fonksiyon | Açıklama |
|----------|---------|
| send     | Paket gönder |
| sendp    | Layer 2 gönder |
| sr       | Gönder + cevap al |
| sr1      | Tek cevap al |
| sniff    | Paket yakala |
| show     | Detay göster |
| summary  | Kısa göster |
| rdpcap   | PCAP oku |
| wrpcap   | PCAP yaz |
| haslayer | Katman kontrol |

---

## En kritik kullanım

```python
from scapy.all import *

# Paket oluştur
pkt = IP(dst="8.8.8.8")/ICMP()

# Gönder ve cevap al
reply = sr1(pkt, timeout=2)

# Göster
if reply:
    reply.show()
```

---

# 🎯 Sonuç

Scapy fonksiyonlarını öğrenirken şuna odaklan:

- Paket oluştur
- Gönder
- Cevap al
- İncele
- Filtrele

Bu 5 adımı öğrenirsen Scapy’nin %80’ini çözmüş olursun.

---