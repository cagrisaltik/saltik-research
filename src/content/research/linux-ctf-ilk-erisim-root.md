---
title: "Linux CTF: İlk Erişimden Root Yetkisine"
description: "İzinli bir CTF laboratuvarında servis keşfi, HTTP incelemesi, SSH erişimi ve Linux yetki kontrolleriyle ilerleyen örnek bir çözümleme."
pubDate: 2026-10-15
category: "CTF"
tags:
  - Linux
  - CTF
  - Nmap
  - Privilege Escalation
draft: false
lang: "tr"
---

# Linux CTF: İlk Erişimden Root Yetkisine

## 1. Giriş

Bu yazıda, kontrollü bir CTF laboratuvarında Linux hedefini analiz ederken izlenebilecek temel adımları inceleyeceğiz. Amaç; servisleri tanımak, web uygulamasından bilgi toplamak ve elde edilen bulguları sistem üzerinde doğrulamaktır.

**Kapsam:** Yalnızca kendi laboratuvarınızda veya test etmeye açıkça yetkili olduğunuz hedeflerde uygulayın.

## 2. Laboratuvar hazırlığı

Örnek hedef IP adresimiz `10.10.10.10` olsun. Bu adres yalnızca örnektir; kendi CTF ortamınızın verdiği hedef adresle değiştirin.

Önerilen kaynaklar:

- [Nmap resmi dokümantasyonu](https://nmap.org/book/man.html)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [GTFOBins](https://gtfobins.github.io/) — Linux ikililerinin yetki bağlamındaki davranışlarını incelemek için
- [TryHackMe](https://tryhackme.com/) — kontrollü siber güvenlik laboratuvarları

## 3. Servis keşfi

İlk adım, hedefin erişilebilir olup olmadığını ve hangi servislerin açık olduğunu belirlemektir.

```bash
ping -c 4 10.10.10.10
```

ICMP yanıtı alınamaması hedefin kapalı olduğu anlamına gelmez; ağ yapılandırması ICMP trafiğini engelliyor olabilir.

Ardından, yalnızca izinli laboratuvar hedefinde servis keşfi yapın:

```bash
nmap -sV -sC -oN nmap-initial.txt 10.10.10.10
```

Bu komut yaygın servisleri tespit etmeye ve temel servis betiklerini çalıştırmaya çalışır. Sonuçları `nmap-initial.txt` dosyasına kaydeder.

Çıktıda özellikle şu bilgileri not edin:

- Açık portlar ve servis adları
- Servis sürümleri
- HTTP veya HTTPS servislerinin varlığı
- SSH erişiminin sunulup sunulmadığı

## 4. HTTP incelemesi

Hedefte bir web servisi varsa önce yanıt başlıklarını kontrol edin:

```bash
curl -sS -I http://10.10.10.10/
```

Ardından ana sayfanın HTML içeriğini inceleyin:

```bash
curl -sS http://10.10.10.10/ -o index.html
```

Sayfa kaynağında yorumlar, yönlendirmeler, kullanılan teknolojiler veya laboratuvarın özellikle bıraktığı ipuçları bulunabilir.

Her bulguyu doğrulanmış gerçek olarak kabul etmeyin. Örneğin, bir sunucu başlığındaki sürüm bilgisi eski veya değiştirilmiş olabilir.

## 5. Erişim elde edildikten sonra

CTF senaryosunda size yetkili bir kullanıcı hesabı veya SSH kimlik bilgisi verilmişse, bu bilgileri yalnızca ilgili laboratuvar ortamında kullanın.

```bash
ssh kullanici@10.10.10.10
```

Sisteme erişim sağlandıktan sonra temel bağlamı kaydedin:

```bash
whoami
id
hostname
pwd
```

Bu komutlar mevcut kullanıcıyı, grup üyeliklerini, makine adını ve çalışma dizinini gösterir.

## 6. Yetki ve yapılandırma kontrolleri

Linux üzerinde mevcut kullanıcıya tanımlanmış `sudo` izinlerini kontrol edin:

```bash
sudo -l
```

Çıktıyı dikkatle inceleyin. Bir komutun `sudo` ile çalıştırılabilmesi, her durumda ayrıcalık yükseltme açığı bulunduğu anlamına gelmez. Sonuç; komutun izinlerine, argümanlarına ve sistem yapılandırmasına bağlıdır.

Ek olarak sistemin temel bilgilerini toplayabilirsiniz:

```bash
uname -a
cat /etc/os-release
find / -type f -perm -4000 2>/dev/null
```

Son komut, erişilebilen dosya sisteminde SUID biti ayarlanmış dosyaları arar. Bu dosyaların varlığı tek başına güvenlik açığı kanıtı değildir.

Şüpheli bir ikili veya yanlış yapılandırma tespit edilirse ilgili davranışı laboratuvarın kuralları çerçevesinde doğrulayın. Gerekirse [GTFOBins](https://gtfobins.github.io/) gibi kaynakları referans alın.

## 7. Bulguların kaydı

Her adım için şu bilgileri not edin:

- **Gözlem:** Ne tespit edildi?
- **Kanıt:** Hangi komut çıktısı veya dosya bu sonucu destekliyor?
- **Etki:** Bulgudan hangi risk doğabilir?
- **Doğrulama:** Sonuç tekrar üretilebildi mi?
- **Öneri:** Yapılandırma nasıl iyileştirilebilir?

Gerçek bir CTF çözümünde flag değerlerini paylaşmanız gerekiyorsa yarışmanın ve platformun yayımlama kurallarına uyun.

## 8. Sonuç

Bu örnek akış, Linux CTF analizinde sistematik ilerlemenin temelini gösterir: keşif, servis incelemesi, yetkili erişimin doğrulanması ve yerel yapılandırmaların değerlendirilmesi.

Başarılı bir analiz yalnızca root yetkisine ulaşmaktan ibaret değildir. Her adımın kanıtlanması ve bulgunun neden önemli olduğunun açıklanması, kaliteli bir teknik write-up'ın asıl değeridir.

---

**Kaynaklar**

- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [GTFOBins](https://gtfobins.github.io/)
- [TryHackMe](https://tryhackme.com/)
