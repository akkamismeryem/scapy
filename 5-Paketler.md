# 🌐 Scapy Protokoller ve Paket Yapıları Rehberi

Bu doküman, Scapy’de kullanılan **temel ve ileri seviye protokolleri**,  
**paket yapılarıyla birlikte** öğretir.

Amaç:
- Hangi protokol ne işe yarar?
- Scapy’de nasıl oluşturulur?
- Hangi alanlar önemlidir?
- Gerçek hayatta nasıl kullanılır?

---

# 📌 İçindekiler

1. Katmanlı yapı (çok önemli)
2. Layer 2 protokolleri
3. Layer 3 protokolleri
4. Layer 4 protokolleri
5. Uygulama katmanı protokolleri
6. Kablosuz (WiFi) protokolleri
7. Tünel / özel protokoller
8. Raw veri
9. Katman kombinasyonları
10. Gerçek paket örnekleri
11. Cheat Sheet

---

# 1. 🧠 Katman Mantığı (EN ÖNEMLİ KONU)

Scapy’de paketler şu şekilde oluşturulur:

```python
pkt = Ether()/IP()/TCP()/Raw()
```

Yani:

| Katman | Açıklama |
|------|--------|
| Ether | Fiziksel / MAC |
| IP    | Ağ |
| TCP   | Taşıma |
| Raw   | Veri |

👉 `/` işareti katmanları birbirine bağlar.

---

# 2. 🔌 Layer 2 (Data Link) Protokolleri

---

## 🧱 Ether()

Ethernet katmanı

```python
pkt = Ether(dst="ff:ff:ff:ff:ff:ff")
```

### Önemli alanlar:
- `src` → kaynak MAC
- `dst` → hedef MAC
- `type` → üst katman

---

## 🔍 ARP()

IP → MAC çözümleme

```python
pkt = ARP(pdst="192.168.1.1")
```

### Önemli alanlar:
- `pdst` → hedef IP
- `psrc` → kaynak IP
- `hwsrc` → kaynak MAC
- `hwdst` → hedef MAC

---

## 🧭 LLC()

Logical Link Control (nadiren kullanılır)

```python
pkt = LLC()
```

---

## 📡 SNAP()

LLC üstünde çalışır

```python
pkt = LLC()/SNAP()
```

---

# 3. 🌍 Layer 3 (Network) Protokolleri

---

## 🌐 IP()

En temel protokol

```python
pkt = IP(dst="8.8.8.8")
```

### Önemli alanlar:
- `src`
- `dst`
- `ttl`
- `id`
- `flags`

---

## 🌐 IPv6()

IPv6 versiyonu

```python
pkt = IPv6(dst="2001:4860:4860::8888")
```

---

## 📡 ICMP()

Ping ve hata mesajları

```python
pkt = IP(dst="8.8.8.8")/ICMP()
```

### Tipler:
- echo-request
- echo-reply

---

## 📡 ICMPv6()

IPv6 için ICMP

```python
pkt = IPv6(dst="::1")/ICMPv6EchoRequest()
```

---

## 📡 IGMP()

Multicast yönetimi

```python
pkt = IP(dst="224.0.0.1")/IGMP()
```

---

# 4. 🚀 Layer 4 (Transport) Protokolleri

---

## 🔗 TCP()

Bağlantı odaklı

```python
pkt = IP(dst="1.1.1.1")/TCP(dport=80, flags="S")
```

### Flags:
- S → SYN
- A → ACK
- F → FIN
- R → RESET

---

## ⚡ UDP()

Bağlantısız

```python
pkt = IP(dst="8.8.8.8")/UDP(dport=53)
```

---

# 5. 🌐 Uygulama Katmanı Protokolleri

---

## 🌍 DNS()

Alan adı çözümleme

```python
pkt = IP(dst="8.8.8.8")/UDP(dport=53)/DNS(rd=1, qd=DNSQR(qname="google.com"))
```

---

## 🌐 HTTP (Raw ile)

```python
pkt = IP(dst="example.com")/TCP(dport=80)/Raw(load="GET / HTTP/1.1\r\n\r\n")
```

---

## 🔐 TLS / SSL

Scapy’de sınırlı destek

```python
pkt = IP()/TCP()/TLS()
```

---

## 📡 DHCP()

IP dağıtımı

```python
pkt = Ether()/IP()/UDP()/BOOTP()/DHCP()
```

---

## 📡 SNMP()

Ağ yönetimi

```python
pkt = IP()/UDP()/SNMP()
```

---

# 6. 📶 Kablosuz (WiFi) Protokoller

---

## 📡 Dot11()

WiFi frame

```python
pkt = Dot11()
```

---

## 📡 Dot11Beacon()

Access Point yayını

```python
pkt = Dot11Beacon()
```

---

## 📡 Dot11ProbeReq()

Ağ arama

```python
pkt = Dot11ProbeReq()
```

---

## 📡 Dot11Auth()

Kimlik doğrulama

```python
pkt = Dot11Auth()
```

---

# 7. 🛣️ Tünel ve Özel Protokoller

---

## 🌐 GRE()

Tünel protokolü

```python
pkt = IP()/GRE()/IP()
```

---

## 🌐 IP-in-IP

```python
pkt = IP()/IP()
```

---

## 🌐 VXLAN

```python
pkt = Ether()/IP()/UDP()/VXLAN()
```

---

# 8. 📦 Raw Veri

Ham veri taşır

```python
pkt = IP()/TCP()/Raw(load="Hello World")
```

---

# 9. 🔗 Katman Kombinasyonları

---

## Basit ping

```python
IP()/ICMP()
```

---

## Web isteği

```python
IP()/TCP()/Raw()
```

---

## DNS sorgusu

```python
IP()/UDP()/DNS()
```

---

## ARP broadcast

```python
Ether(dst="ff:ff:ff:ff:ff:ff")/ARP()
```

---

## WiFi paket

```python
RadioTap()/Dot11()/Dot11Beacon()
```

---

# 10. 🧪 Gerçek Paket Örnekleri

---

## Ping paketi

```python
pkt = IP(dst="8.8.8.8")/ICMP()
pkt.show()
```

---

## SYN paketi

```python
pkt = IP(dst="scanme.nmap.org")/TCP(dport=80, flags="S")
```

---

## DNS sorgusu

```python
pkt = IP(dst="8.8.8.8")/UDP(dport=53)/DNS(rd=1, qd=DNSQR(qname="google.com"))
```

---

## ARP scan

```python
pkt = Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst="192.168.1.0/24")
```

---

## WiFi beacon

```python
pkt = RadioTap()/Dot11()/Dot11Beacon()
```

---

---

## 🔍 1. ICMP Flood (Ping Testi Mantığı)

```python
from scapy.all import *

target = "8.8.8.8"

for i in range(10):
    send(IP(dst=target)/ICMP(), verbose=0)
```

📌 Ne yapar:
- Hedefe art arda ICMP paketleri gönderir  
- Ping test mantığını simüle eder  

---

## 🌐 2. TCP SYN Scan (Port Açık mı?)

```python
from scapy.all import *

target = "scanme.nmap.org"
port = 80

pkt = IP(dst=target)/TCP(dport=port, flags="S")
reply = sr1(pkt, timeout=2, verbose=0)

if reply:
    if reply.haslayer(TCP):
        if reply[TCP].flags == 0x12:
            print("Port açık (SYN-ACK)")
        elif reply[TCP].flags == 0x14:
            print("Port kapalı (RST)")
else:
    print("Cevap yok (filtrelenmiş olabilir)")
```

📌 Ne yapar:
- SYN gönderir  
- Gelen cevaba göre port durumunu analiz eder  

---

## 🌍 3. DNS Sorgusu (Gerçek İstek)

```python
from scapy.all import *

pkt = IP(dst="8.8.8.8")/UDP(dport=53)/DNS(
    rd=1,
    qd=DNSQR(qname="google.com")
)

reply = sr1(pkt, timeout=2, verbose=0)

if reply:
    reply.show()
```

📌 Ne yapar:
- DNS sunucusuna gerçek sorgu gönderir  
- gelen IP cevabını gösterir  

---

## 📡 4. ARP Scan (Ağdaki Cihazları Bulma)

```python
from scapy.all import *

network = "192.168.1.0/24"

packet = Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst=network)
ans, _ = srp(packet, timeout=2, verbose=0)

for s, r in ans:
    print(f"IP: {r.psrc} | MAC: {r.hwsrc}")
```

📌 Ne yapar:
- Local networkteki cihazları bulur  
- IP + MAC listeler  

---

## 🔐 5. HTTP GET Request (Raw ile)

```python
from scapy.all import *

http_request = "GET / HTTP/1.1\r\nHost: example.com\r\n\r\n"

pkt = IP(dst="93.184.216.34")/TCP(dport=80, flags="PA")/Raw(load=http_request)

send(pkt)
```

📌 Ne yapar:
- Ham HTTP isteği gönderir  
- gerçek web trafiğini simüle eder  

---

## 🔁 6. TCP 3-Way Handshake Simülasyonu

```python
from scapy.all import *

target = "scanme.nmap.org"

# SYN
syn = IP(dst=target)/TCP(dport=80, flags="S")
syn_ack = sr1(syn, timeout=2)

# ACK
if syn_ack:
    ack = IP(dst=target)/TCP(
        dport=80,
        sport=syn_ack[TCP].dport,
        seq=syn_ack[TCP].ack,
        ack=syn_ack[TCP].seq + 1,
        flags="A"
    )
    send(ack)
    print("Handshake tamamlandı")
```

📌 Ne yapar:
- TCP bağlantısını manuel kurar  
- ağ protokolünü derin anlamanı sağlar  

---

## 📥 7. Belirli IP’den Gelen Paketleri Yakalama

```python
from scapy.all import *

def process(pkt):
    if pkt.haslayer(IP):
        if pkt[IP].src == "8.8.8.8":
            print(pkt.summary())

sniff(filter="ip", prn=process, count=10)
```

📌 Ne yapar:
- sadece belirli IP’den gelen paketleri yakalar  

---

## 📦 8. DNS Trafiği Sniff Etme

```python
from scapy.all import *

sniff(filter="udp port 53", prn=lambda pkt: pkt.summary(), count=10)
```

📌 Ne yapar:
- DNS trafiğini canlı izler  

---

## 🔎 9. TCP Paketlerinden Veri Çekme

```python
from scapy.all import *

def extract_data(pkt):
    if pkt.haslayer(Raw):
        print(pkt[Raw].load)

sniff(filter="tcp", prn=extract_data, count=10)
```

# 11. ⚡ Cheat Sheet

---

## En önemli protokoller

| Katman | Protokol |
|------|---------|
| L2 | Ether, ARP |
| L3 | IP, IPv6, ICMP |
| L4 | TCP, UDP |
| App | DNS, HTTP, DHCP |

---

## En kritik yapı

```python
Ether()/IP()/TCP()/Raw()
```

---

## En sık kullanılanlar

```python
IP()
ICMP()
TCP()
UDP()
ARP()
DNS()
Raw()
```

---

# 🎯 Sonuç

Scapy’de en önemli şey:

👉 Katmanları anlamak  
👉 Paketleri birleştirmek  
👉 Hangi protokol ne yapıyor bilmek  

Eğer şunları biliyorsan:
- IP
- ICMP
- TCP
- UDP
- ARP
- DNS

👉 Scapy’nin %80’ini çözmüşsün demektir.

---