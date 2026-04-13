# 📝 Scapy ile Python Dosyası (`.py`) Oluşturma ve Yazma Rehberi

Bu doküman, Scapy kullanırken **Python dosyası oluşturma**,  
**dosya yapısını düzenleme**, **kodu yazma**, **kaydetme**, **çalıştırma** ve  
**iyi bir script düzeni kurma** konularını işlevsel şekilde anlatır.

Amaç:
- `.py` dosyası nasıl oluşturulur?
- Scapy kodu dosya içine nasıl yazılır?
- VS Code’da nasıl düzenlenir?
- Script nasıl çalıştırılır?
- Daha temiz ve düzenli kod nasıl yazılır?

---

# 📌 İçindekiler

1. `.py` dosyası nedir?
2. Neden script dosyası kullanılır?
3. VS Code’da `.py` dosyası oluşturma
4. Temel Scapy dosya yapısı
5. Dosya içine kod yazma mantığı
6. `main` yapısı kullanma
7. Fonksiyonlu yapı kurma
8. Kullanıcıdan giriş alma
9. Sonuçları dosyaya yazma
10. Hata yakalama
11. Örnek scriptler
12. Klasör düzeni önerisi
13. Sık yapılan hatalar
14. Cheat Sheet

---

# 1. 🧠 `.py` Dosyası Nedir?

`.py`, Python kodlarının yazıldığı dosya uzantısıdır.

Örnek:

```text
ping_test.py
arp_scan.py
dns_query.py
sniffer.py
```

Bu dosyalar içinde Python kodu bulunur ve terminalden çalıştırılır.

---

# 2. 🎯 Neden Script Dosyası Kullanılır?

Scapy’yi Python kabuğunda da kullanabilirsin ama `.py` dosyası kullanmak daha iyidir.

Çünkü:

- kod kaybolmaz
- tekrar tekrar çalıştırabilirsin
- düzenli proje kurarsın
- fonksiyon yazabilirsin
- hata ayıklama kolay olur
- GitHub’a koyabilirsin

---

# 3. 💻 VS Code’da `.py` Dosyası Oluşturma

## Yöntem 1: Explorer üzerinden

1. VS Code’u aç
2. Proje klasörünü aç
3. Sol tarafta **New File** seç
4. Dosya adını yaz

Örnek:

```text
ping_test.py
```

---

## Yöntem 2: Terminal üzerinden

```bash
touch ping_test.py
```

Windows’ta PowerShell:

```powershell
New-Item ping_test.py
```

---

## Yöntem 3: Klasör + dosya oluşturma

```bash
mkdir scapy-project
cd scapy-project
touch main.py
```

Windows PowerShell:

```powershell
mkdir scapy-project
cd scapy-project
New-Item main.py
```

---

# 4. 🧱 Temel Scapy Dosya Yapısı

En basit Scapy scripti şöyle başlar:

```python
from scapy.all import *

pkt = IP(dst="8.8.8.8")/ICMP()
reply = sr1(pkt, timeout=2)

if reply:
    reply.show()
else:
    print("Cevap yok")
```

Bu yapı çalışır, ama daha düzenlisi vardır.

---

# 5. ✍️ Dosya İçine Kod Yazma Mantığı

Bir Scapy scripti yazarken genelde şu sırayı kullan:

1. importlar
2. ayarlar / sabitler
3. fonksiyonlar
4. ana çalışma bölümü
5. çıktı / kayıt

---

## Örnek şablon

```python
from scapy.all import *

TARGET_IP = "8.8.8.8"

def ping_target(ip):
    pkt = IP(dst=ip) / ICMP()
    reply = sr1(pkt, timeout=2, verbose=0)
    return reply

reply = ping_target(TARGET_IP)

if reply:
    print("Hedef cevap verdi")
    reply.show()
else:
    print("Cevap gelmedi")
```

---

# 6. 🚀 `main` Yapısı Kullanma

Büyük scriptlerde kodu direkt alta yazmak yerine `main` yapısı kullanılır.

Bu çok daha temizdir.

---

## Neden kullanılır?

- kod düzenli olur
- script import edildiğinde otomatik çalışmaz
- proje büyüyünce yönetmek kolaylaşır

---

## Örnek

```python
from scapy.all import *

def ping_target(ip):
    pkt = IP(dst=ip) / ICMP()
    return sr1(pkt, timeout=2, verbose=0)

def main():
    target = "8.8.8.8"
    reply = ping_target(target)

    if reply:
        print(f"{target} cevap verdi")
        reply.show()
    else:
        print(f"{target} cevap vermedi")

if __name__ == "__main__":
    main()
```

---

# 7. 🧩 Fonksiyonlu Yapı Kurma

Scapy scriptlerini fonksiyonlara bölmek en iyi yöntemlerden biridir.

---

## Örnek: temiz yapı

```python
from scapy.all import *

def build_packet(ip):
    return IP(dst=ip) / ICMP()

def send_packet(pkt):
    return sr1(pkt, timeout=2, verbose=0)

def print_result(reply):
    if reply:
        print("Cevap geldi")
        reply.show()
    else:
        print("Cevap gelmedi")

def main():
    pkt = build_packet("8.8.8.8")
    reply = send_packet(pkt)
    print_result(reply)

if __name__ == "__main__":
    main()
```

---

# 8. ⌨️ Kullanıcıdan Giriş Alma

Scripti daha kullanışlı yapmak için kullanıcıdan IP, port gibi bilgiler alabilirsin.

---

## Örnek: IP alma

```python
from scapy.all import *

def main():
    target = input("Hedef IP gir: ")
    pkt = IP(dst=target) / ICMP()
    reply = sr1(pkt, timeout=2, verbose=0)

    if reply:
        print("Cevap geldi")
    else:
        print("Cevap gelmedi")

if __name__ == "__main__":
    main()
```

---

## Örnek: IP ve port alma

```python
from scapy.all import *

def main():
    target = input("Hedef IP: ")
    port = int(input("Hedef port: "))

    pkt = IP(dst=target) / TCP(dport=port, flags="S")
    reply = sr1(pkt, timeout=2, verbose=0)

    if reply:
        reply.show()
    else:
        print("Cevap yok")

if __name__ == "__main__":
    main()
```

---

# 9. 💾 Sonuçları Dosyaya Yazma

Scapy scriptlerinde sonuçları sadece ekrana değil, dosyaya da yazabilirsin.

---

## Basit metin dosyasına yazma

```python
result = "Tarama tamamlandı\n"

with open("sonuc.txt", "w", encoding="utf-8") as f:
    f.write(result)
```

---

## Listeyi dosyaya yazma

```python
hosts = ["192.168.1.1", "192.168.1.5", "192.168.1.10"]

with open("aktif_hostlar.txt", "w", encoding="utf-8") as f:
    for host in hosts:
        f.write(host + "\n")
```

---

## Sniff sonuçlarını PCAP olarak kaydetme

```python
from scapy.all import *

packets = sniff(count=20)
wrpcap("capture.pcap", packets)
```

---

# 10. 🛡️ Hata Yakalama

Scapy scriptlerinde hata çıkabilir.  
Özellikle:

- yanlış IP
- yetki problemi
- timeout
- yanlış arayüz
- eksik paket desteği

Bunlar için `try-except` kullanmak iyidir.

---

## Örnek

```python
from scapy.all import *

def main():
    try:
        target = input("Hedef IP: ")
        pkt = IP(dst=target) / ICMP()
        reply = sr1(pkt, timeout=2, verbose=0)

        if reply:
            print("Cevap geldi")
        else:
            print("Cevap yok")

    except Exception as e:
        print("Hata oluştu:", e)

if __name__ == "__main__":
    main()
```

---

# 11. 🧪 Uygulamalı Script Örnekleri

---

## Örnek 1: Ping scripti

Dosya adı:

```text
ping_test.py
```

Kod:

```python
from scapy.all import *

def ping_target(ip):
    pkt = IP(dst=ip) / ICMP()
    return sr1(pkt, timeout=2, verbose=0)

def main():
    target = "8.8.8.8"
    reply = ping_target(target)

    if reply:
        print(f"{target} aktif")
        reply.show()
    else:
        print(f"{target} cevap vermedi")

if __name__ == "__main__":
    main()
```

Çalıştırma:

```bash
python ping_test.py
```

---

## Örnek 2: TCP SYN kontrol scripti

Dosya adı:

```text
syn_check.py
```

Kod:

```python
from scapy.all import *

def check_port(ip, port):
    pkt = IP(dst=ip) / TCP(dport=port, flags="S")
    reply = sr1(pkt, timeout=2, verbose=0)
    return reply

def main():
    target = "scanme.nmap.org"
    port = 80

    reply = check_port(target, port)

    if reply and reply.haslayer(TCP):
        if reply[TCP].flags == 0x12:
            print(f"{port} portu açık")
        elif reply[TCP].flags == 0x14:
            print(f"{port} portu kapalı")
        else:
            print("Farklı bir cevap geldi")
    else:
        print("Cevap yok")

if __name__ == "__main__":
    main()
```

---

## Örnek 3: Basit sniff scripti

Dosya adı:

```text
sniffer.py
```

Kod:

```python
from scapy.all import *

def process_packet(pkt):
    print(pkt.summary())

def main():
    sniff(filter="icmp", prn=process_packet, count=10)

if __name__ == "__main__":
    main()
```

---

## Örnek 4: ARP scan scripti

Dosya adı:

```text
arp_scan.py
```

Kod:

```python
from scapy.all import *

def arp_scan(network):
    packet = Ether(dst="ff:ff:ff:ff:ff:ff") / ARP(pdst=network)
    answered, _ = srp(packet, timeout=2, verbose=0)

    results = []
    for _, recv in answered:
        results.append((recv.psrc, recv.hwsrc))
    return results

def main():
    network = "192.168.1.0/24"
    results = arp_scan(network)

    for ip, mac in results:
        print(f"IP: {ip} | MAC: {mac}")

if __name__ == "__main__":
    main()
```

---

## Örnek 5: Sonucu dosyaya yazan script

Dosya adı:

```text
save_results.py
```

Kod:

```python
from scapy.all import *

def arp_scan(network):
    packet = Ether(dst="ff:ff:ff:ff:ff:ff") / ARP(pdst=network)
    answered, _ = srp(packet, timeout=2, verbose=0)

    results = []
    for _, recv in answered:
        results.append(f"IP: {recv.psrc} | MAC: {recv.hwsrc}")
    return results

def save_results(filename, data):
    with open(filename, "w", encoding="utf-8") as f:
        for line in data:
            f.write(line + "\n")

def main():
    network = "192.168.1.0/24"
    results = arp_scan(network)
    save_results("arp_results.txt", results)
    print("Sonuçlar kaydedildi")

if __name__ == "__main__":
    main()
```

---

# 12. 📁 Klasör Düzeni Önerisi

Küçük projelerde bile düzenli klasör yapısı çok iş görür.

---

## Basit yapı

```text
scapy-project/
│
├── main.py
├── ping_test.py
├── sniffer.py
├── arp_scan.py
└── results/
```

---

## Daha düzenli yapı

```text
scapy-project/
│
├── src/
│   ├── main.py
│   ├── ping_test.py
│   ├── arp_scan.py
│   └── sniffer.py
│
├── outputs/
│   ├── capture.pcap
│   └── results.txt
│
└── README.md
```

---

# 13. ❌ Sık Yapılan Hatalar

---

## 1. Dosya adı yanlış koymak

Şunları dosya adı yapma:

```text
scapy.py
socket.py
random.py
```

Çünkü Python gerçek modüllerle karışabilir.

---

## 2. `from scapy.all import *` yazmayı unutmak

Scapy fonksiyonları çalışmaz.

---

## 3. Script çalıştırırken yanlış klasörde olmak

```bash
python ping_test.py
```

komutunu doğru klasörde çalıştırmalısın.

---

## 4. Yönetici yetkisi olmadan sniff denemek

Sniff veya bazı paket gönderme işlemleri hata verebilir.

---

## 5. Kodu direkt alta yığmak

Her şeyi tek yerde yazmak yerine fonksiyonlara böl.

---

# 14. ⚡ Cheat Sheet

---

## Yeni dosya oluşturma

Windows PowerShell:

```powershell
New-Item ping_test.py
```

Linux / macOS:

```bash
touch ping_test.py
```

---

## Script çalıştırma

```bash
python ping_test.py
```

---

## Temel dosya yapısı

```python
from scapy.all import *

def main():
    print("Scapy script başladı")

if __name__ == "__main__":
    main()
```

---

## Dosyaya yazma

```python
with open("sonuc.txt", "w", encoding="utf-8") as f:
    f.write("Merhaba")
```

---

## PCAP kaydetme

```python
packets = sniff(count=10)
wrpcap("capture.pcap", packets)
```

---

# 🎯 Sonuç

Scapy için `.py` dosyası yazarken en iyi mantık şudur:

1. Dosyayı oluştur
2. importları ekle
3. fonksiyonları yaz
4. `main()` oluştur
5. sonucu ekrana veya dosyaya yaz
6. terminalden çalıştır

En temiz başlangıç şablonu:

```python
from scapy.all import *

def main():
    pkt = IP(dst="8.8.8.8") / ICMP()
    reply = sr1(pkt, timeout=2, verbose=0)

    if reply:
        print("Cevap geldi")
    else:
        print("Cevap yok")

if __name__ == "__main__":
    main()
```

Böyle başlarsan hem düzenli hem büyütülebilir bir Scapy projesi kurarsın.

---