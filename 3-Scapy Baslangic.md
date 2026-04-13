# 🐍 Scapy Başlangıç Rehberi

Bu doküman, **Scapy’ye yeni başlayan biri için kapsamlı ama işlevsel bir giriş** sunar.  
Amaç sadece komut ezberlemek değil, **Scapy ile ne yapılır, nasıl düşünülür, hangi komut ne işe yarar, nasıl test edilir** bunları anlamaktır.

---

# 📌 İçindekiler

1. Scapy nedir?
2. Ne işe yarar?
3. Temel çalışma mantığı
4. Scapy'yi başlatma
5. En çok kullanılan temel yapılar
6. Paket oluşturma
7. Paket gönderme
8. Paket yakalama
9. Paket inceleme
10. Katman mantığı
11. Sık kullanılan protokoller
12. Uygulamalı örnekler
13. PCAP dosyaları ile çalışma
14. Faydalı komutlar
15. Sık yapılan hatalar
16. Güvenlik ve dikkat edilmesi gerekenler
17. Öğrenme sırası
18. Mini çalışma planı

---

# 1. Scapy Nedir?

**Scapy**, Python ile ağ paketleri oluşturmayı, göndermeyi, yakalamayı ve analiz etmeyi sağlayan güçlü bir araçtır.

Scapy ile şunları yapabilirsin:

- Ağ paketi oluşturma
- Paket gönderme
- Paket dinleme
- Ağ cihazlarını keşfetme
- Protokol analizi yapma
- PCAP dosyalarını okuma
- Güvenlik testleri yapma
- Kendi ağ scriptlerini yazma

Scapy’nin en güzel yanı, hem **öğrenme aracı** hem de **pratik test aracı** olmasıdır.

---

# 2. Ne İşe Yarar?

Scapy genelde şu alanlarda kullanılır:

## Ağ öğrenimi
IP, TCP, UDP, ICMP, ARP gibi protokolleri uygulamalı görmeni sağlar.

## Ağ analizi
Gerçek trafikte hangi paketler dönüyor anlayabilirsin.

## Güvenlik testleri
Port keşfi, host keşfi, ağ davranışı gözlemi gibi kontrollü testlerde işe yarar.

## Paket manipülasyonu
Normalde sistemin oluşturduğu paketleri kendin elle üretebilirsin.

## PCAP analizi
Wireshark ile alınmış kayıtları Python tarafında işleyebilirsin.

---

# 3. Temel Çalışma Mantığı

Scapy’de ana fikir şudur:

- Bir paket oluşturursun
- Gerekirse alanlarını değiştirirsin
- Paketi gönderirsin
- Gelen cevabı incelersin
- Ya da ağı dinleyip paketleri toplarsın

Scapy, ağı **paket paketi** görmeni sağlar.

Örneğin normalde tarayıcı ile site açarsın.  
Scapy ile bunun alt seviyesinde:

- IP başlığı
- TCP başlığı
- bayraklar
- kaynak hedef adresleri
- portlar
- veri yükü

gibi alanları kendin kontrol edebilirsin.

---

# 4. Scapy'yi Başlatma

## Python içinde başlatma

```python
from scapy.all import *
```

## Scapy kabuğunu açma

Linux / macOS:

```bash
sudo scapy
```

Windows:

```bash
python
```

sonra:

```python
from scapy.all import *
```

> Not: Paket gönderme ve sniff işlemlerinde admin/root yetkisi gerekebilir.

---

# 5. En Çok Kullanılan Temel Yapılar

Scapy’de çok sık göreceğin bazı temel nesneler:

- `IP()` → IP katmanı
- `ICMP()` → ICMP katmanı
- `TCP()` → TCP katmanı
- `UDP()` → UDP katmanı
- `ARP()` → ARP katmanı
- `Ether()` → Ethernet katmanı
- `Raw()` → ham veri
- `DNS()` → DNS katmanı
- `sr()` → paket gönder, cevap al
- `sr1()` → tek cevap al
- `send()` → gönder
- `sendp()` → Layer 2 seviyesinde gönder
- `sniff()` → trafik yakala
- `wrpcap()` → pcap dosyasına yaz
- `rdpcap()` → pcap dosyasını oku
- `show()` → paket alanlarını göster
- `summary()` → kısa özet göster

---

# 6. Paket Oluşturma

Scapy’de katmanlar `/` işareti ile birleştirilir.

Örnek:

```python
pkt = IP(dst="8.8.8.8")/ICMP()
```

Bu şu anlama gelir:

- Dış katman: IP
- Üst katman: ICMP

Yani bu bir **ping paketi** mantığındadır.

## Paketi inceleme

```python
pkt.show()
```

## Kısa özet

```python
pkt.summary()
```

## Ham görünüm

```python
raw(pkt)
```

---

# 7. Paket Gönderme

Scapy’de gönderme için birkaç temel fonksiyon vardır.

## `send()`
Layer 3 seviyesinde paket gönderir.

```python
send(IP(dst="8.8.8.8")/ICMP())
```

## `sr()`
Paket gönderir ve cevapları toplar.

```python
ans, unans = sr(IP(dst="8.8.8.8")/ICMP(), timeout=2)
```

- `ans` → cevaplananlar
- `unans` → cevapsız kalanlar

## `sr1()`
Tek cevap beklenen durumlarda çok kullanılır.

```python
reply = sr1(IP(dst="8.8.8.8")/ICMP(), timeout=2)
reply.show()
```

## `sendp()`
Layer 2 seviyesinde gönderir. Ethernet katmanı ile çalışırken kullanılır.

```python
sendp(Ether()/ARP(pdst="192.168.1.1"))
```

---

# 8. Paket Yakalama

Scapy ile ağ dinlemek için `sniff()` kullanılır.

## Temel kullanım

```python
packets = sniff(count=5)
```

Bu komut 5 paket yakalar.

## Arayüz belirterek

```python
packets = sniff(iface="Ethernet", count=10)
```

## Filtre ile

```python
packets = sniff(filter="icmp", count=5)
```

## Her paketi anında yazdırma

```python
sniff(filter="icmp", prn=lambda x: x.summary(), count=5)
```

Burada:

- `filter="icmp"` → sadece ICMP paketleri
- `prn=` → her gelen pakette çalışacak fonksiyon
- `count=5` → 5 paket sonra dur

## Süre ile dinleme

```python
sniff(timeout=10)
```

Bu da 10 saniye dinler.

---

# 9. Paket İnceleme

Yakalanan paketleri sonradan da inceleyebilirsin.

## İlk paket

```python
packets[0]
```

## Detaylı göster

```python
packets[0].show()
```

## Özet liste

```python
packets.summary()
```

## Belirli katmana erişme

```python
packets[0][IP]
packets[0][TCP]
```

## Alan okuma

```python
packets[0][IP].src
packets[0][IP].dst
```

Örnek:

```python
print(packets[0][IP].src)
print(packets[0][IP].dst)
```

---

# 10. Katman Mantığı

Scapy’nin en önemli mantığı **katmanlı yapı**dır.

Örnek paket:

```python
pkt = Ether()/IP(dst="1.1.1.1")/TCP(dport=80)/Raw(load="Hello")
```

Burada sırasıyla:

- `Ether()` → Ethernet katmanı
- `IP()` → IP katmanı
- `TCP()` → TCP katmanı
- `Raw()` → veri kısmı

Bu yapı, ağ paketlerinin gerçek mantığını anlamak için çok önemlidir.

## Katman kontrolü

```python
pkt.show()
```

## Belirli katman var mı?

```python
pkt.haslayer(IP)
pkt.haslayer(TCP)
```

---

# 11. Sık Kullanılan Protokoller

## ICMP
Ping gibi işlemlerde kullanılır.

```python
IP(dst="8.8.8.8")/ICMP()
```

## TCP
Bağlantı odaklı iletişim.

```python
IP(dst="1.1.1.1")/TCP(dport=80, flags="S")
```

Buradaki `flags="S"` bir SYN paketidir.

## UDP
Bağlantısız iletişim.

```python
IP(dst="8.8.8.8")/UDP(dport=53)
```

## ARP
Yerel ağda MAC-IP çözümlemesi için kullanılır.

```python
ARP(pdst="192.168.1.1")
```

## DNS
Alan adı çözümleme işlemlerinde kullanılır.

```python
IP(dst="8.8.8.8")/UDP(dport=53)/DNS(rd=1, qd=DNSQR(qname="google.com"))
```

---

# 12. Uygulamalı Temel Örnekler

---

## Örnek 1: Ping atma

```python
from scapy.all import *

pkt = IP(dst="8.8.8.8")/ICMP()
reply = sr1(pkt, timeout=2)

if reply:
    print("Cevap geldi")
    reply.show()
else:
    print("Cevap gelmedi")
```

### Ne yapar?
- 8.8.8.8 adresine ICMP paketi gönderir
- cevap varsa gösterir

---

## Örnek 2: Basit host keşfi

```python
from scapy.all import *

targets = ["192.168.1.1", "192.168.1.2", "192.168.1.3"]

for ip in targets:
    reply = sr1(IP(dst=ip)/ICMP(), timeout=1, verbose=0)
    if reply:
        print(f"{ip} aktif")
    else:
        print(f"{ip} cevap vermedi")
```

### Ne yapar?
- listedeki IP’lere ping gönderir
- hangileri cevap veriyor gösterir

---

## Örnek 3: ARP ile yerel ağ keşfi

```python
from scapy.all import *

arp_request = ARP(pdst="192.168.1.0/24")
broadcast = Ether(dst="ff:ff:ff:ff:ff:ff")
packet = broadcast / arp_request

answered, unanswered = srp(packet, timeout=2, verbose=0)

for sent, received in answered:
    print(f"IP: {received.psrc}  MAC: {received.hwsrc}")
```

### Ne yapar?
- yerel ağdaki cihazları ARP üzerinden bulmaya çalışır
- IP ve MAC bilgilerini gösterir

> Bu yöntem sadece kendi ağında, izinli test ortamında kullanılmalıdır.

---

## Örnek 4: TCP SYN paketi gönderme

```python
from scapy.all import *

pkt = IP(dst="scanme.nmap.org")/TCP(dport=80, flags="S")
reply = sr1(pkt, timeout=2)

if reply:
    reply.show()
else:
    print("Cevap yok")
```

### Ne yapar?
- hedefe SYN paketi yollar
- gelen cevabı gösterir

---

## Örnek 5: ICMP paketlerini sniff etme

```python
from scapy.all import *

sniff(filter="icmp", prn=lambda x: x.summary(), count=10)
```

### Ne yapar?
- ağdaki ICMP paketlerini toplar
- özetlerini ekrana yazdırır

---

## Örnek 6: HTTP / TCP paketlerini gözleme

```python
from scapy.all import *

sniff(filter="tcp port 80", prn=lambda x: x.summary(), count=10)
```

### Ne yapar?
- 80 numaralı porttaki TCP trafiğini dinler

---

## Örnek 7: Belirli IP’den gelen paketleri inceleme

```python
from scapy.all import *

def process_packet(pkt):
    if pkt.haslayer(IP):
        print("Kaynak:", pkt[IP].src, "Hedef:", pkt[IP].dst)

sniff(filter="ip", prn=process_packet, count=5)
```

---

# 13. PCAP Dosyaları ile Çalışma

Scapy sadece canlı trafik için değil, kayıtlı paketler için de kullanılır.

## PCAP okuma

```python
packets = rdpcap("ornek.pcap")
print(len(packets))
```

## İlk paketi göster

```python
packets[0].show()
```

## Tüm özetleri yazdır

```python
packets.summary()
```

## PCAP yazma

```python
wrpcap("kayit.pcap", packets)
```

## Sniff edip dosyaya kaydetme

```python
packets = sniff(count=20)
wrpcap("yakalananlar.pcap", packets)
```

---

# 14. Faydalı Komutlar

## Kısa özet
```python
pkt.summary()
```

## Detaylı alanlar
```python
pkt.show()
```

## Hex görünüm
```python
hexdump(pkt)
```

## Ham byte verisi
```python
raw(pkt)
```

## Paket listesi özeti
```python
packets.summary()
```

## Belirli katmanı çekme
```python
pkt[IP]
pkt[TCP]
```

## Katman kontrolü
```python
pkt.haslayer(ICMP)
```

## Tüm alanları öğrenme
```python
ls(IP)
ls(TCP)
ls(UDP)
ls(ICMP)
```

Bu komutlar çok önemlidir.  
Bir protokolü öğrenirken önce `ls()` ile hangi alanlar var bakman çok faydalıdır.

---

# 15. Sık Yapılan Hatalar

## 1. Yetki hatası
`sniff()` veya paket gönderme işlemlerinde admin/root gerekebilir.

## 2. Yanlış arayüz adı
`iface="Ethernet"` gibi arayüz adı sistemden sisteme değişir.

Arayüzleri görmek için:

```python
show_interfaces()
```

## 3. Cevap gelmemesi
Her hedef cevap vermek zorunda değildir.

Sebep:
- firewall
- ICMP kapalı
- hedef erişilemiyor
- timeout düşük

## 4. Layer 2 ve Layer 3 farkını karıştırmak
- `send()` → Layer 3
- `sendp()` → Layer 2
- `sr()` → cevap bekleyen Layer 3
- `srp()` → cevap bekleyen Layer 2

## 5. Filtrelerin çalışmaması
Bazen `filter=` ifadesi sistemde pcap desteği ile ilişkilidir. Özellikle Windows’ta Npcap kurulu olmalıdır.

---

# 16. Güvenlik ve Dikkat Edilmesi Gerekenler

Scapy çok güçlüdür. Bu yüzden sorumlu kullanmak gerekir.

## Asla şunları izinsiz yapma:
- Başkasının ağını tarama
- Yetkisiz sniff
- Gerçek hedeflere agresif test
- Trafiği bozacak paketler gönderme

## Sadece şuralarda çalış:
- Kendi bilgisayarın
- Kendi ağın
- Lab ortamı
- Sanal makineler
- Eğitim/test ağı

## Güvenli öğrenme ortamı önerileri
- VMware / VirtualBox
- Kali + Ubuntu / Windows lab
- ev içi test ağı
- offline pcap analizi

---

# 17. Öğrenme Sırası

Scapy’yi öğrenirken karışmamak için şu sıra çok iyidir:

## 1. ICMP
- `IP()/ICMP()`
- `sr1()`
- ping mantığı

## 2. Sniff
- `sniff()`
- `summary()`
- `show()`

## 3. TCP / UDP
- port kavramı
- `flags`
- SYN paketi
- basit cevap analizi

## 4. ARP
- yerel ağ keşfi
- IP-MAC ilişkisi

## 5. PCAP
- `rdpcap()`
- `wrpcap()`

## 6. Filtreleme ve otomasyon
- `prn=`
- fonksiyon yazma
- packet processing

---

# 18. Mini Çalışma Planı

Aşağıdaki plan başlangıç için çok uygundur.

## Gün 1
- Scapy kur
- `IP`, `ICMP`, `TCP`, `UDP` katmanlarını tanı
- `show()` ve `summary()` çalış

## Gün 2
- `send`, `sr`, `sr1` dene
- ping scripti yaz
- cevapları yorumla

## Gün 3
- `sniff()` dene
- ICMP ve TCP filtrele
- `prn=lambda x: x.summary()` kullan

## Gün 4
- ARP keşif çalış
- kendi lab ağında cihazları listele

## Gün 5
- `rdpcap()` ile dosya aç
- `wrpcap()` ile kayıt al
- paketleri döngü ile işle

## Gün 6
- küçük bir proje yaz:
  - ping checker
  - ağdaki aktif host listesi
  - belirli protokolü izleme scripti

---

# 19. Hazır Mini Başlangıç Scriptleri

---

## Script 1: Ping kontrolü

```python
from scapy.all import *

target = "8.8.8.8"
reply = sr1(IP(dst=target)/ICMP(), timeout=2, verbose=0)

if reply:
    print(f"{target} cevap verdi")
else:
    print(f"{target} cevap vermedi")
```

---

## Script 2: ICMP sniff

```python
from scapy.all import *

sniff(filter="icmp", prn=lambda pkt: pkt.summary(), count=10)
```

---

## Script 3: Yerel ağ ARP keşfi

```python
from scapy.all import *

network = "192.168.1.0/24"
packet = Ether(dst="ff:ff:ff:ff:ff:ff") / ARP(pdst=network)

answered, _ = srp(packet, timeout=2, verbose=0)

for _, recv in answered:
    print(f"IP: {recv.psrc} | MAC: {recv.hwsrc}")
```

---

## Script 4: PCAP oku

```python
from scapy.all import *

packets = rdpcap("ornek.pcap")
print("Toplam paket:", len(packets))

for pkt in packets[:5]:
    print(pkt.summary())
```

---

# 20. En Önemli Kısa Özet

Scapy öğrenirken önce şu 10 şeyi iyi bil:

1. `from scapy.all import *`
2. `IP()/ICMP()`
3. `show()`
4. `summary()`
5. `send()`
6. `sr()` / `sr1()`
7. `sniff()`
8. `ARP()`
9. `rdpcap()` / `wrpcap()`
10. `haslayer()` ve `pkt[IP].src` gibi alan erişimleri

Bunları öğrendikten sonra Scapy ile çok rahat ilerlersin.

---

# 21. Son Söz

Scapy’yi iyi öğrenmenin yolu sadece okumak değil, **küçük küçük denemeler yapmaktır**.  
Önce tek paket oluştur, sonra gönder, sonra cevap analiz et, sonra sniff et, sonra pcap ile çalış.

Karmaşık şeylere erken girersen kafa karışır.  
Ama ICMP, ARP, TCP SYN ve sniff mantığını oturtursan geri kalanı çok daha kolay gelir.

---