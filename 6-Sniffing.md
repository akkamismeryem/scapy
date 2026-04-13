# 📡 Scapy Sniffing (Paket Dinleme) Rehberi

Bu doküman, Scapy ile **ağ trafiğini dinleme (sniffing)** konusunu  
**temelden ileri seviyeye, pratik ve işlevsel** şekilde anlatır.

Amaç:
- sniff() nasıl çalışır?
- filtreleme nasıl yapılır?
- gerçek paketler nasıl analiz edilir?
- veri nasıl çıkarılır?

---

# 📌 İçindekiler

1. Sniffing nedir?
2. sniff() temel kullanımı
3. sniff() parametreleri
4. Filtreleme (BPF)
5. Paket işleme (prn)
6. Gerçek zamanlı analiz
7. Veri çıkarma (payload)
8. Belirli hedefleri izleme
9. PCAP ile sniffing
10. Gelişmiş sniffing teknikleri
11. Performans ve optimizasyon
12. Sık yapılan hatalar
13. Uygulamalı örnekler
14. Cheat Sheet

---

# 1. 🧠 Sniffing Nedir?

Sniffing, ağ üzerinden geçen paketleri **dinleme ve analiz etme işlemidir**.

Scapy ile:
- canlı trafik yakalarsın
- paketleri incelersin
- veri çıkarırsın
- saldırı veya anomali analiz edebilirsin

---

# 2. 📥 sniff() Temel Kullanımı

```python
from scapy.all import *

packets = sniff(count=5)
```

📌 Ne yapar:
- 5 paket yakalar
- liste olarak döner

---

## Paketleri yazdırma

```python
for pkt in packets:
    print(pkt.summary())
```

---

# 3. ⚙️ sniff() Parametreleri

---

## `count`
Kaç paket yakalanacağını belirler

```python
sniff(count=10)
```

---

## `timeout`
Süre ile dinleme

```python
sniff(timeout=5)
```

---

## `iface`
Ağ arayüzü seçme

```python
sniff(iface="Ethernet", count=5)
```

---

## `filter`
BPF filtresi (çok önemli)

```python
sniff(filter="icmp", count=5)
```

---

## `prn`
Her paket geldiğinde çalışacak fonksiyon

```python
sniff(prn=lambda x: x.summary())
```

---

## `store`
Paketleri RAM'de tutma

```python
sniff(store=0)
```

---

## `stop_filter`
Sniff'i durdurma şartı

```python
sniff(stop_filter=lambda x: x.haslayer(ICMP))
```

---

# 4. 🔍 Filtreleme (BPF)

Filtreleme performans için çok önemli.

---

## ICMP filtre

```python
sniff(filter="icmp")
```

---

## TCP filtre

```python
sniff(filter="tcp")
```

---

## Belirli port

```python
sniff(filter="tcp port 80")
```

---

## Belirli IP

```python
sniff(filter="host 192.168.1.1")
```

---

## Kombine filtre

```python
sniff(filter="tcp and port 80")
```

---

## Sadece gelen trafik

```python
sniff(filter="dst host 192.168.1.5")
```

---

# 5. 🧩 Paket İşleme (prn)

Sniff sırasında veri işlemek için kullanılır.

---

## Basit kullanım

```python
sniff(prn=lambda pkt: pkt.summary())
```

---

## Fonksiyon ile

```python
def process(pkt):
    print(pkt.summary())

sniff(prn=process)
```

---

# 6. ⚡ Gerçek Zamanlı Analiz

---

## Kaynak ve hedef IP

```python
def analyze(pkt):
    if pkt.haslayer(IP):
        print(pkt[IP].src, "→", pkt[IP].dst)

sniff(filter="ip", prn=analyze)
```

---

## TCP portları

```python
def analyze(pkt):
    if pkt.haslayer(TCP):
        print(pkt[TCP].sport, "→", pkt[TCP].dport)

sniff(filter="tcp", prn=analyze)
```

---

# 7. 📦 Payload (Veri) Çıkarma

---

## Raw veri çekme

```python
def extract(pkt):
    if pkt.haslayer(Raw):
        print(pkt[Raw].load)

sniff(prn=extract, count=10)
```

---

## HTTP veri yakalama

```python
def http(pkt):
    if pkt.haslayer(Raw):
        try:
            data = pkt[Raw].load.decode(errors="ignore")
            if "HTTP" in data:
                print(data)
        except:
            pass

sniff(filter="tcp port 80", prn=http)
```

---

# 8. 🎯 Belirli Hedef İzleme

---

## Belirli IP

```python
def monitor(pkt):
    if pkt.haslayer(IP) and pkt[IP].src == "8.8.8.8":
        print(pkt.summary())

sniff(prn=monitor)
```

---

## Belirli port

```python
sniff(filter="tcp port 22", prn=lambda x: x.summary())
```

---

# 9. 💾 PCAP ile Sniffing

---

## Sniff edip kaydetme

```python
packets = sniff(count=20)
wrpcap("capture.pcap", packets)
```

---

## PCAP okuyup analiz

```python
packets = rdpcap("capture.pcap")
packets.summary()
```

---

## PCAP + filtre

```python
for pkt in packets:
    if pkt.haslayer(ICMP):
        print(pkt.summary())
```

---

# 10. 🚀 Gelişmiş Sniffing Teknikleri

---

## Belirli sayıda ve filtreli

```python
sniff(filter="tcp port 80", count=10)
```

---

## Arka planda sniff

```python
sniff(prn=lambda x: x.summary(), store=0)
```

---

## Stop condition

```python
sniff(stop_filter=lambda pkt: pkt.haslayer(ICMP))
```

---

## Paket boyutuna göre filtre

```python
def size(pkt):
    if len(pkt) > 100:
        print("Büyük paket:", pkt.summary())

sniff(prn=size)
```

---

# 11. ⚡ Performans ve Optimizasyon

---

## store=0 kullan

```python
sniff(store=0)
```

---

## filtre kullan

```python
sniff(filter="tcp")
```

---

## gereksiz print yapma

```python
sniff(prn=lambda x: None)
```

---

# 12. ❌ Sık Yapılan Hatalar

---

## 1. Yetki problemi
Sniff için admin/root gerekebilir.

---

## 2. Yanlış arayüz

```python
show_interfaces()
```

---

## 3. Filtre çalışmıyor
Npcap/libpcap kurulu olmalı.

---

## 4. Paket gelmiyor
- trafik yok
- yanlış filtre
- yanlış arayüz

---

# 13. 🧪 Uygulamalı Örnekler

---

## ICMP sniff

```python
sniff(filter="icmp", prn=lambda pkt: pkt.summary(), count=10)
```

---

## TCP sniff

```python
sniff(filter="tcp", prn=lambda pkt: pkt.summary(), count=10)
```

---

## DNS sniff

```python
sniff(filter="udp port 53", prn=lambda pkt: pkt.summary())
```

---

## IP trafiği analiz

```python
def analyze(pkt):
    if pkt.haslayer(IP):
        print(pkt[IP].src, "→", pkt[IP].dst)

sniff(prn=analyze)
```

---

## Veri yakalama

```python
def data(pkt):
    if pkt.haslayer(Raw):
        print(pkt[Raw].load)

sniff(prn=data)
```

---

# 14. ⚡ Cheat Sheet

---

## En önemli komut

```python
sniff(filter="tcp", prn=lambda x: x.summary())
```

---

## En kritik parametreler

| Parametre | Açıklama |
|----------|---------|
| count    | kaç paket |
| timeout  | süre |
| filter   | trafik filtresi |
| prn      | işlem fonksiyonu |
| iface    | ağ arayüzü |
| store    | bellekte tut |

---

## En önemli kullanım

```python
from scapy.all import *

sniff(filter="icmp", prn=lambda pkt: pkt.summary(), count=5)
```

---

# 🎯 Sonuç

Sniffing öğrenirken şu 4 şeyi iyi öğren:

1. sniff()
2. filter
3. prn
4. haslayer + pkt[IP]

Bunları biliyorsan Scapy sniffing konusunu çözmüşsün demektir.

---