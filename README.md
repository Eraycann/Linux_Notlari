# Linux İşletim Sistemi Notları

## Terminal Nedir ve Neden Önemlidir?

Terminal, Linux sistemlerde en güçlü araçtır. Grafiksel arayüz olmadan sistem yönetimi yapabilmenizi sağlar. Günlük kullanımda terminal bilgisi:
- Sistem sorunlarını hızlı çözmenize
- Otomatize işlemler yapmanıza
- Sistem kaynaklarını verimli kullanmanıza yardımcı olur

# 1. GNU ve Unix Komutları

## Temel Kullanıcı Yetkileri

### Kullanıcı Tipleri ve Promptlar
```bash
$ # Normal kullanıcı promptu
# # Root kullanıcı promptu
```

Terminalde gördüğünüz bu işaretler size hangi yetkide olduğunuzu gösterir:
- `$` işareti: Sınırlı yetkili normal kullanıcı
- `#` işareti: Tam yetkili root kullanıcı

### Root Kullanıcıya Geçiş
```bash
sudo su      # Root kullanıcıya geçer
exit         # Normal kullanıcıya döner
whoami       # Aktif kullanıcıyı gösterir
```
## Kullanıcı ve Grup Yönetimi

### Kullanıcı Oluşturma ve Yönetme
```bash
# Yeni kullanıcı oluşturma
sudo useradd -s /bin/bash -m -d /home/yenikullanici -g sudo yenikullanici

# Kullanıcı şifresi belirleme
sudo passwd yenikullanici
```

Parametreler ve açıklamaları:
- `-s` : Shell belirtme - kullanıcının hangi shell'de çalışacağını belirler
- `-m` : Home dizini oluşturma (mkdir) - kullanıcıya özel dizin oluşturur
- `-d` : Home dizini lokasyonu - kullanıcı dizininin nerede oluşturulacağını belirler
- `-g` : Birincil grup atama - kullanıcının ana grubunu belirler


### Pratik Kullanım Örneği
Diyelim ki yeni bir stajyer için hesap oluşturacaksınız:

```bash
# Stajyer grubu oluştur
sudo groupadd stajyerler

# Stajyer kullanıcı oluştur
sudo useradd -s /bin/bash -m -d /home/stajyer1 -g stajyerler stajyer1

# Şifre belirle
sudo passwd stajyer1

# Sınırlı yetkiler ver
sudo usermod -G stajyerler stajyer1
```

## Dosya Sistemi ve Temel Komutlar

### Dizin Yapısı
Linux'ta her şey bir dosyadır ve hiyerarşik bir yapı vardır:

/Root dizin (her şeyin başlangıcı) <br>
├── home/ Kullanıcı dizinleri <br>
├── etc/ Sistem ayarları <br>
├── var/ Değişken dosyalar (loglar vs.) <br>
└── usr/ Programlar ve kütüphaneler <br>

### Günlük Kullanım Örnekleri

1. Dosya Arama:
```bash
# Masaüstünde .pdf uzantılı dosyaları bul
find ~/Desktop -name "*.pdf"

# Son 7 günde değiştirilmiş dosyaları bul
find /home -mtime -7
```

2. Disk Kullanımı:
```bash
# Dizin boyutlarını görüntüle
du -sh *

# Disk kullanımını kontrol et
df -h
```

3. Metin Dosyası İşlemleri:
```bash
# Dosya içeriğini görüntüle
cat dosya.txt

# Dosyada kelime ara
grep "aranacak_kelime" dosya.txt

# Dosyaya metin ekle
echo "yeni satır" >> dosya.txt
```

### Kullanıcı Bilgilerini Görüntüleme
```bash
# Tüm kullanıcıları listeler
cat /etc/passwd
# Çıktı örneği: kullanıcı:x:1000:1000::/home/kullanıcı:/bin/bash

# Kullanıcı detaylarını gösterir
id hacker
# Çıktı örneği: uid=1000(hacker) gid=1000(hacker) groups=1000(hacker),27(sudo)

# Kullanıcı şifre durumunu gösterir
cat /etc/shadow
# Çıktı örneği: hacker:$6$xyz...:18734:0:99999:7:::
```

### Grup İşlemleri
```bash
# Yeni grup oluşturma
groupadd pentesters

# Grupları listeleme
cat /etc/group

# Kullanıcıyı gruba ekleme
usermod -G pentesters tunahan

# Kullanıcıyı gruptan çıkarma
gpasswd -d hacker pentesters
```

### Kullanıcı Yönetimi
```bash
# Kullanıcı kilitleme
usermod -L hacker

# Kullanıcı ve tüm dosyalarını silme
userdel -r hacker

# Sadece kullanıcıyı silme (dosyaları kalır)
userdel hacker

# Kullanıcı dizinlerini listeleme
ls -al /home/
```

## Sudo Yapılandırması

### Sudo Dosyası Düzenleme ve Örnekler
```bash
# Sudo dosyasını düzenleme
sudo vi /etc/sudoers

# Örnek yapılandırmalar:
# Kullanıcı yetkilendirme
hacker ALL=(ALL:ALL) ALL  # Tüm komutlara yetki
hacker ALL=(ALL:ALL) /usr/bin/systemctl  # Sadece systemctl komutuna yetki

# Grup yetkilendirme
%sudo ALL=(ALL:ALL) ALL  # Sudo grubu tam yetki
%juniors ALL=(ALL:ALL) /usr/bin/apt  # Juniors grubu sadece apt komutu
```

## Ağ Yönetimi

### Temel Ağ Kontrolü
```bash
# İnternet bağlantısı kontrolü
ping google.com

# Ağ arayüzlerini listele
ip addr show

# DNS ayarlarını kontrol et
cat /etc/resolv.conf
```

### Pratik Ağ Sorun Giderme
Ağ sorunu yaşadığınızda:
1. Önce fiziksel bağlantıyı kontrol edin
2. IP adresini kontrol edin:
```bash
ip addr show
```
3. Ağ arayüzünü yeniden başlatın:
```bash
sudo ip link set eth0 down
sudo ip link set eth0 up
```
4. DNS çözümlemesini test edin:
```bash
nslookup google.com
```

### Network Arayüz Yönetimi
```bash
# Ağ arayüzlerini listeleme
ip addr
# Çıktı örneği:
# 1: lo: <LOOPBACK,UP,LOWER_UP>
# 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>

# Arayüz kapatma/açma
ip link set eth0 down
ip link set eth0 up

# Arayüz detayları
ethtool -i eth0
# Çıktı: driver, version, firmware-version bilgileri

# Arayüz istatistikleri
ethtool -S eth0
# Çıktı: rx_packets, tx_packets, collisions vb.
```

### DNS Yapılandırması
```bash
# DNS ayarlarını görüntüleme
cat /etc/resolv.conf

# DNS sunucu ekleme örneği
nameserver 8.8.8.8
nameserver 1.1.1.1
```

## Sistem İzleme ve Performans

### Sistem Kaynaklarını İzleme
```bash
# Anlık sistem durumu
top

# Bellek kullanımı
free -h

# Disk kullanımı
df -h
```


### Pratik İzleme Senaryosu
Sistem yavaşladığında:
1. CPU kullanımını kontrol et:
```bash
top
```
2. Bellek kullanımını kontrol et:
```bash
free -h
```
3. Disk kullanımını kontrol et:
```bash
df -h
```
4. Yüksek kaynak kullanan süreçleri bul:
```bash
ps aux --sort=-%cpu | head
```

## Servis Yönetimi

### Temel Servis Komutları
```bash
# Servis durumunu kontrol et
systemctl status nginx

# Servisi başlat
systemctl start nginx

# Servisi durdur
systemctl stop nginx

# Servisi yeniden başlat
systemctl restart nginx

# Otomatik başlatma ayarları
systemctl enable nginx  # Sistem açılışında otomatik başlar
systemctl disable nginx  # Otomatik başlatma devre dışı
```


### Pratik Servis Yönetimi
Web sunucusu örneği:
```bash
# Apache'yi kur
sudo apt install apache2

# Servisi başlat
sudo systemctl start apache2

# Otomatik başlatmayı etkinleştir
sudo systemctl enable apache2

# Konfigürasyonu kontrol et
sudo apache2ctl configtest
```

## Güvenlik ve Yedekleme

### Temel Güvenlik Pratikleri
1. Düzenli güncelleme:
```bash
sudo apt update
sudo apt upgrade
```

2. Önemli dosyaları yedekleme:
```bash
# Ev dizinini yedekle
tar -czf backup.tar.gz /home/kullanici

# Belirli bir dizini yedekle
rsync -av kaynak/ hedef/
```

3. İzinleri kontrol etme:
```bash
# Dosya izinlerini görüntüle
ls -l

# İzinleri değiştir
chmod 644 dosya.txt
```

## Süreç Yönetimi

### Süreç İzleme ve Kontrol
```bash
# Aktif süreçleri görüntüleme
ps
ps aux  # Tüm süreçler
ps axjf  # Ağaç yapısında gösterim

# Süreç ağacı
pstree

# Sistem kaynakları izleme
top
htop  # Daha gelişmiş arayüz

# Bellek kullanımı
free -h  # İnsan okunabilir format
```


## Faydalı İpuçları

### Terminal Kısayolları
- `Ctrl + C`: Çalışan programı durdur
- `Ctrl + Z`: Programı arka plana al
- `Ctrl + R`: Komut geçmişinde ara
- `Ctrl + L`: Ekranı temizle

### Komut Geçmişi
```bash
# Son kullanılan komutları göster
history

# Son 10 komutu göster
history | tail

# Belirli bir komutu ara
history | grep "apt"
```

### Sistem Bilgisi
```bash
# Sistem bilgilerini göster
uname -a

# Donanım bilgilerini göster
lshw

# İşlemci bilgilerini göster
cat /proc/cpuinfo
```


## Dosya Sistemi İşlemleri

### Dizin Gezinme Örnekleri
```bash
# Mevcut dizin
pwd
# Çıktı: /home/kullanıcı

# Dizin değiştirme
cd /home/hacker/Desktop
cd ..  # Üst dizin
cd /   # Kök dizin
cd ~   # Kullanıcı ana dizini
```

### Dosya/Dizin İşlemleri
```bash
# Dizin oluşturma
mkdir linuxculer
mkdir -p dizin1/dizin2/dizin3  # İç içe dizinler

# Dosya oluşturma
touch dosya.txt
touch {1..5}.txt  # 1.txt'den 5.txt'ye kadar dosya oluşturur

# Kopyalama işlemleri
cp dosya.txt yeni.txt
cp -r kaynak_dizin/ hedef_dizin/

# Taşıma/Yeniden adlandırma
mv eski.txt yeni.txt
mv dizin1 /yeni/konum/

# Silme işlemleri
rm dosya.txt
rm -rf dizin/  # Dizin ve içeriğini sil
```

## Ardışık Komutlar

### Komut Zincirleme Örnekleri
```bash
# Çıktı yönlendirme
ls -al | grep "txt"
more /etc/passwd | grep root

# Koşullu çalıştırma
mkdir dizin && cd dizin
ping google.com && ls -al

# Paralel çalıştırma
ls -al & ping google.com
mkdir linux & touch linux/dosya.txt
```

## Run-Level (SystemD) Yönetimi

### Run-Level Detayları
```bash
# Mevcut run-level kontrolü
systemctl get-default
# Çıktı: graphical.target

# Run-level değiştirme
systemctl isolate multi-user.target  # CLI modu
systemctl isolate rescue.target      # Kurtarma modu
systemctl isolate emergency.target   # Acil durum modu
```

Run-Level açıklamaları:
- `rc0.d` - Sistemi güvenli şekilde kapatır
- `rc1.d` - Tek kullanıcı modu, sistem bakımı için
- `rc2.d` - Çok kullanıcılı mod, GUI yok, yüksek performans
- `rc3.d` - Ağ servisleri için optimize edilmiş mod
- `rc4.d` - Özelleştirilebilir seviye
- `rc5.d` - GUI destekli tam fonksiyonel mod
- `rc6.d` - Sistemi yeniden başlatma
- `rcS.d` - Sistem başlangıç seviyesi, kernel seviyesi müdahale

## Önemli Dizinler ve Açıklamaları

```bash
/etc/      # Sistem konfigürasyon dosyaları
/home/     # Kullanıcı dizinleri
/usr/bin/  # Sistem uygulamaları
/var/log/  # Sistem logları
/opt/      # Üçüncü parti uygulamalar
/mnt/      # Geçici bağlama noktaları
```


## Sorun Giderme

### Genel Sorun Giderme Adımları
1. Log dosyalarını kontrol et:
```bash
tail -f /var/log/syslog
```

2. Disk alanını kontrol et:
```bash
df -h
du -sh /*
```

3. Sistem yükünü kontrol et:
```bash
uptime
top
```

4. Ağ bağlantılarını kontrol et:
```bash
netstat -tuln
ss -tuln
```

### Yaygın Sorunlar ve Çözümleri

1. "Komut bulunamadı" hatası:
```bash
# PATH'i kontrol et
echo $PATH

# Programı yeniden kur
sudo apt install paket_adi
```

2. Disk dolu hatası:
```bash
# Büyük dosyaları bul
find / -size +100M

# Eski log dosyalarını temizle
sudo journalctl --vacuum-time=7d
```

3. Servis başlatma hatası:
```bash
# Servis durumunu kontrol et
systemctl status servis_adi

# Servis loglarını incele
journalctl -u servis_adi
```

## Güvenlik Notları ve Best Practices

1. Root Yetkileri:
   - Root hesabını doğrudan kullanmaktan kaçının
   - Sudo kullanımında en az yetki prensibini uygulayın
   - Root şifresini güvenli ve karmaşık tutun

2. Sistem Güvenliği:
   - Kritik sistem dosyalarını değiştirmeden önce yedek alın
   - `rm -rf /` gibi tehlikeli komutlardan kaçının
   - Sistem loglarını düzenli kontrol edin

3. Kullanıcı Yönetimi:
   - Kullanıcı şifrelerini düzenli değiştirin
   - Kullanılmayan hesapları devre dışı bırakın
   - Grup yetkilerini düzenli gözden geçirin





# 2. Linux Paket Yönetimi Notlari

## Temel Kavramlar ve Giriş

### Linux'ta Paket Nedir?
Bir paket, yazılımın kendisi, konfigürasyon dosyaları ve kurulum talimatlarını içeren bir arşiv dosyasıdır. Linux'ta yazılım kurulumu ve yönetimi bu paketler üzerinden yapılır.

### Man (Manual) Sayfaları
Herhangi bir komutun detaylı kullanımını öğrenmek için:
```bash
man komut_adi
# Örnek:
man apt     # apt komutunun tüm detaylarını gösterir
```

## Kullanıcı Yetkileri ve Terminal Kullanımı

### Terminal Promptları
```bash
$    # Normal kullanıcı promptu
#    # Root kullanıcı promptu
```

### Yetki Yükseltme
```bash
sudo komut     # Tek bir komutu root yetkisiyle çalıştırır
sudo su        # Root kullanıcıya geçiş yapar
exit           # Root oturumundan çıkar
whoami         # Aktif kullanıcıyı gösterir
```

**Önemli Not**: Root yetkilerini dikkatli kullanın. Yanlış komutlar sistem bütünlüğüne zarar verebilir.

## Paket Yönetim Sistemleri

### APT (Advanced Package Tool)
Debian tabanlı sistemlerin (Ubuntu, Kali Linux vb.) ana paket yöneticisidir.

**Özellikler**:
- Online paket kurulumu
- Otomatik bağımlılık çözümleme
- Repo tabanlı güncelleme sistemi

### DPKG (Debian Package)
Debian paket sisteminin temel aracıdır.

**Özellikler**:
- Offline paket kurulumu
- Düşük seviye paket yönetimi
- `.deb` uzantılı paketlerle çalışır

## Detaylı Paket Yönetim Komutları

### Paket Arama ve Bilgi Alma
```bash
# Paket arama
apt search sqlmap
# Çıktı örneği:
# sqlmap/kali-rolling,now 1.6.12-1 all
#   Automatic SQL injection tool

# Detaylı paket bilgisi
apt show sqlmap
# Çıktı: Versiyon, bağımlılıklar, açıklama gibi detaylı bilgiler

# Kurulu paketleri listeleme
dpkg -l
# Çıktı formatı:
# ii  paket-adi  versiyon  mimari  açıklama

# Belirli bir paketi arama
dpkg -l | grep nmap
# Çıktı: nmap ile ilgili kurulu paketler

# Paket bağımlılıklarını görüntüleme
apt-cache depends nmap
# Çıktı: nmap'in çalışması için gereken tüm paketler
```

### Paket Kurulum İşlemleri

#### APT ile Online Kurulum
```bash
# Temel kurulum
sudo apt install paket_adi

# Örnek wget kurulumu
sudo apt install wget
# Not: wget bir dosya indirme aracıdır
# Kullanım: wget https://site.com/dosya.zip

# Birden fazla paket kurulumu
sudo apt install paket1 paket2 paket3
```

#### DPKG ile Offline Kurulum
```bash
# .deb paketi kurulumu
sudo dpkg -i paket_adi.deb

# Örnek Chrome kurulumu
cd Downloads
sudo dpkg -i google-chrome-stable_current_amd64.deb

# Kurulum sonrası bağımlılık düzeltme
sudo apt --fix-broken install
```

### Paket Kaldırma İşlemleri

#### Temel Kaldırma
```bash
# APT ile kaldırma
sudo apt remove paket_adi

# DPKG ile kaldırma
sudo dpkg -r paket_adi

# Tam kaldırma (konfigürasyon dosyaları dahil)
sudo apt-get purge paket_adi
```

#### Örnek Kaldırma Senaryosu
```bash
# Chrome'u kaldırma
# Önce tam paket adını bulalım
dpkg -l | grep chrome
# Çıktı: google-chrome-stable

# Kaldırma işlemi
sudo apt remove google-chrome-stable

# Konfigürasyon dosyalarını da kaldırma
sudo apt-get purge google-chrome-stable
```

### Paket Güncelleme İşlemleri

#### Güncelleme Türleri
1. **apt update**: Paket listesini günceller
2. **apt upgrade**: Paketleri yükseltir
3. **apt --only-upgrade install paket_adi**: Tek paket güncelleme

```bash
# Sistem güncellemesi
sudo apt update    # Repo listesini günceller
sudo apt upgrade   # Yükseltilebilir paketleri günceller

# Yükseltilebilir paketleri listeleme
apt list --upgradable

# Tek paket güncelleme
sudo apt --only-upgrade install firefox
```

## Kaynak Koddan Program Kurulumu

### Adım Adım Kurulum
```bash
# 1. Arşivi açma
bzip2 -d program.tar.bz2
tar xvf program.tar

# 2. Dizine girme
cd program_dizini

# 3. Yapılandırma
sudo ./configure
# Bu adım sistem uyumluluğunu kontrol eder

# 4. Derleme
sudo make
# Kaynak kodu derler

# 5. Kurulum
sudo make install
# Derlenmiş programı sisteme kurar

# Kaldırma
sudo make uninstall
```

## Repository (Depo) Yönetimi

### Sistem Repoları
```bash
# Ana repo dosyası
/etc/apt/sources.list

# Ek repo dizini
/etc/apt/sources.list.d/
```

### Repo Yapısı Açıklaması
```bash
deb http://http.kali.org/kali kali-rolling main contrib non-free-firmware

# Bileşenler:
# deb: Paket formatı (debian)
# http://...: Repo URL'si
# kali-rolling: Dağıtım sürümü
# main contrib...: Paket kategorileri
```

## Local Repository Oluşturma

### Detaylı Adımlar

1. **Repo Dizini Oluşturma**
```bash
sudo mkdir /var/localrepo
cd /var/localrepo
```

2. **Paketleri İndirme**
```bash
sudo apt install golang-go -d --reinstall \
    --allow-downgrades \
    --allow-remove-essential \
    --allow-change-held-packages

# Parametreler:
# -d: Sadece indir, kurma
# --reinstall: Yeniden kuruluma izin ver
# --allow-downgrades: Sürüm düşürmeye izin ver
# --allow-remove-essential: Temel paketleri kaldırmaya izin ver
# --allow-change-held-packages: Tutulan paketleri değiştirmeye izin ver
```

3. **Paketleri Local Repo'ya Taşıma**
```bash
# Cache'den paketleri kopyalama
sudo cp /var/cache/apt/archives/*.deb /var/localrepo/
```

4. **Paket İndeksi Oluşturma**
```bash
cd /var/localrepo
sudo dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz
```

5. **Local Repo'yu Sisteme Ekleme**
```bash
# Repo dosyası oluşturma
echo "deb [trusted=yes] file:///var/localrepo ./" | \
    sudo tee /etc/apt/sources.list.d/local.list

# Repo listesini güncelleme
sudo apt update
```

### Local Repo Kullanımının Avantajları
1. Offline paket yönetimi
2. Hızlı kurulum
3. Versiyon kontrolü
4. Güvenlik
5. Bant genişliği tasarrufu

## Güvenlik ve Best Practices

### Güvenlik Önerileri
1. Sadece güvenilir repolardan paket kurun
2. Root yerine sudo kullanın
3. Sistem güncellemelerini düzenli yapın
4. Kritik sistem paketlerini dikkatli yönetin

### Performans İpuçları
1. Düzenli cache temizliği yapın
2. Kullanılmayan paketleri kaldırın
3. Repo listesini güncel tutun
4. Gereksiz repoları devre dışı bırakın

### Sorun Giderme
```bash
# Bozuk bağımlılıkları düzeltme
sudo apt --fix-broken install

# Paket durumunu kontrol etme
sudo apt-get check

# Paket veritabanını yeniden oluşturma
sudo apt-get update --fix-missing
```

## Gelişmiş Paket Yönetimi Teknikleri

### APT Konfigürasyon Yönetimi

#### APT Konfigürasyon Dosyaları
```bash
# Ana konfigürasyon dizini
cd /etc/apt/

# Önemli dosyalar
sources.list          # Ana repo listesi
sources.list.d/       # Ek repo dizini
preferences           # Paket öncelikleri
apt.conf.d/          # APT yapılandırma dosyaları
```

#### Sources.list Dosya Yapısı
```bash
# Örnek kaynak satırı incelemesi
deb http://http.kali.org/kali kali-rolling main contrib non-free-firmware

# Bileşenlerin açıklaması:
# deb: İkili paket deposu
# http://...: Depo URL'si
# kali-rolling: Dağıtım sürümü
# main: Resmi desteklenen özgür yazılımlar
# contrib: Özgür ama bağımlılıkları özgür olmayan yazılımlar
# non-free-firmware: Özgür olmayan firmware paketleri
```

### Özel Repository Yönetimi

#### Yeni Repo Ekleme
```bash
# Manuel repo ekleme
sudo touch /etc/apt/sources.list.d/ozel_repo.list
echo "deb [trusted=yes] http://repo.adres.com/ stable main" | \
    sudo tee /etc/apt/sources.list.d/ozel_repo.list

# PPA ekleme (Ubuntu sistemlerde)
sudo add-apt-repository ppa:repo_adi
```

#### Repo Öncelikleri
```bash
# Öncelik dosyası oluşturma
sudo nano /etc/apt/preferences.d/ozel_oncelik

# İçerik örneği:
Package: *
Pin: release o=Kali
Pin-Priority: 1000
```

### Paket Cache Yönetimi

#### Cache Temizleme
```bash
# İndirilen paket dosyalarını temizleme
sudo apt-get clean

# Eski sürüm paketleri temizleme
sudo apt-get autoclean

# Gereksiz bağımlılıkları temizleme
sudo apt autoremove
```

#### Cache İstatistikleri
```bash
# Cache boyutunu görüntüleme
du -sh /var/cache/apt/archives/

# Cache içeriğini listeleme
ls -lh /var/cache/apt/archives/*.deb
```

### Gelişmiş Kurulum Seçenekleri

#### Özel Kurulum Parametreleri
```bash
# Bağımlılıkları görmezden gelme
sudo dpkg -i --ignore-depends=paket_adi program.deb

# Belirli bir sürümü kurma
sudo apt install paket_adi=versiyon_numarası

# Kurulum simülasyonu
sudo apt install -s paket_adi
```

#### Toplu Paket İşlemleri
```bash
# Toplu kurulum
sudo apt install $(cat paket_listesi.txt)

# Kurulu paketleri yedekleme
dpkg --get-selections > kurulu_paketler.txt

# Paketleri geri yükleme
sudo dpkg --set-selections < kurulu_paketler.txt
sudo apt-get dselect-upgrade
```

### Paket Bağımlılık Yönetimi

#### Bağımlılık Analizi
```bash
# Paket bağımlılıklarını görüntüleme
apt-cache depends paket_adi

# Ters bağımlılıkları görüntüleme
apt-cache rdepends paket_adi

# Bağımlılık ağacını görüntüleme
apt-cache showpkg paket_adi
```

#### Bağımlılık Sorunlarını Çözme
```bash
# Eksik bağımlılıkları kurma
sudo apt-get -f install

# Bozuk paketleri yeniden yapılandırma
sudo dpkg --configure -a

# Bağımlılık çakışmalarını çözme
sudo apt --fix-broken install
```

### Local Repository Gelişmiş Özellikler

#### Çoklu Paket Depoları
```bash
# Farklı sürümler için ayrı dizinler
sudo mkdir -p /var/localrepo/{stable,testing}

# Her dizin için indeks oluşturma
cd /var/localrepo/stable
sudo dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz

cd /var/localrepo/testing
sudo dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz
```

#### Repo Metadata Yönetimi
```bash
# Release dosyası oluşturma
cd /var/localrepo
apt-ftparchive release . > Release

# GPG ile imzalama (opsiyonel)
gpg --armor --detach-sign -o Release.gpg Release
```

### Sistem Güvenliği ve Denetim

#### Paket Doğrulama
```bash
# Paket imzalarını kontrol etme
apt-key list

# Yeni GPG anahtarı ekleme
sudo apt-key add anahtar.gpg

# Paket bütünlüğünü kontrol etme
debsums paket_adi
```

#### Güvenlik Güncellemeleri
```bash
# Sadece güvenlik güncellemelerini listeleme
apt list --upgradable | grep security

# Güvenlik güncellemelerini kurma
sudo unattended-upgrade --dry-run
sudo unattended-upgrade
```

### İzleme ve Raporlama

#### Sistem Durumu İzleme
```bash
# Paket istatistikleri
apt-cache stats

# Kurulum logları
tail -f /var/log/apt/history.log

# Paket değişiklik logları
tail -f /var/log/dpkg.log
```

#### Paket Kullanım Analizi
```bash
# En son kurulan paketler
grep "install " /var/log/dpkg.log

# Belirli bir paketin geçmişi
zgrep "paket_adi" /var/log/apt/history.log*
```

### Otomatizasyon ve Scripting

#### Otomatik Güncelleme Scripti
```bash
#!/bin/bash
# update_system.sh

# Sistem güncellemesi
apt update
apt upgrade -y

# Cache temizliği
apt autoremove -y
apt clean

# Log tutma
echo "Güncelleme tamamlandı: $(date)" >> /var/log/system_updates.log
```

#### Toplu Paket İşlem Scripti
```bash
#!/bin/bash
# bulk_package_ops.sh

# Paket listesini oku ve işle
while read package; do
    apt install -y "$package"
    if [ $? -eq 0 ]; then
        echo "$package kuruldu"
    else
        echo "$package kurulum hatası" >> hata.log
    fi
done < paket_listesi.txt
```

### Sorun Giderme ve Hata Ayıklama

#### Yaygın Sorunlar ve Çözümleri
```bash
# Kilit dosyası sorunu
sudo rm /var/lib/apt/lists/lock
sudo rm /var/cache/apt/archives/lock

# Paket veritabanı yenileme
sudo rm -rf /var/lib/apt/lists/*
sudo apt update

# Bozuk paket kaldırma
sudo dpkg --remove --force-remove-reinstreq paket_adi
```

#### Sistem Durumu Kontrolü
```bash
# Paket sistemi durumu
dpkg --audit

# Yarım kalan işlemleri tamamlama
dpkg --configure -a

# Paket veritabanı tutarlılık kontrolü
apt-get check
```
## İleri Seviye Paket Yönetimi ve Sistem Optimizasyonu

### APT Önbellek (Cache) Yönetimi Detayları

#### APT Önbellek Yapılandırması
```bash
# Önbellek dizini yapısı
/var/cache/apt/archives/      # İndirilen paketler
/var/cache/apt/archives/partial/  # Kısmen indirilen paketler
/var/lib/apt/lists/          # Repo indeksleri

# Önbellek boyutu kontrolü
du -sh /var/cache/apt/archives/
du -sh /var/lib/apt/lists/
```

#### Gelişmiş Önbellek Yönetimi
```bash
# Seçici önbellek temizleme
sudo apt-get clean --dry-run  # Simülasyon modu
sudo apt-get clean specific_package*.deb

# Önbellek politikası ayarlama
sudo nano /etc/apt/apt.conf.d/20archive
# İçerik örneği:
APT::Archives::MaxSize "1000000";
APT::Archives::MinSize "500000";
APT::Archives::MaxAge "30";
```

### Özelleştirilmiş Paket Kurulumları

#### Paket Konfigürasyon Yönetimi
```bash
# Paket yapılandırmasını yedekleme
debconf-get-selections > paket_ayarlari.txt

# Yapılandırmayı geri yükleme
debconf-set-selections < paket_ayarlari.txt

# Belirli bir paketin yapılandırmasını sıfırlama
sudo dpkg-reconfigure paket_adi
```

#### Özel Kurulum Senaryoları
```bash
# Belirli dosyaları koruyarak kurulum
sudo apt install --protect-file=/path/to/file paket_adi

# Çakışan paketleri yönetme
sudo apt install -o Dpkg::Options::="--force-overwrite" paket_adi

# Özel dizine kurulum
sudo dpkg -i --instdir=/alternatif/dizin paket.deb
```

### Repository Yönetimi İleri Seviye

#### Özel Repo Oluşturma ve Yönetme
```bash
# Repo dizin yapısı oluşturma
sudo mkdir -p /var/www/repo/{conf,db,dists,pool}
cd /var/www/repo

# Repo konfigürasyonu
cat > conf/distributions << EOF
Origin: Özel Repo
Label: Özel
Suite: stable
Codename: custom
Architectures: amd64 i386
Components: main
Description: Özel Debian Deposu
EOF

# Paket ekleme
reprepro -b . includedeb custom /path/to/package.deb
```

#### Repo Güvenliği
```bash
# GPG anahtar çifti oluşturma
gpg --full-generate-key

# Repo imzalama
gpg --armor --export your@email.com > repo.gpg
sudo cp repo.gpg /var/www/repo/

# İstemcide anahtar ekleme
sudo apt-key add repo.gpg
```

### Sistem Performans Optimizasyonu

#### APT Performans Ayarları
```bash
# Paralel indirme ayarı
echo 'Acquire::Queue-Mode "host";' | \
    sudo tee /etc/apt/apt.conf.d/99parallel-downloads
echo 'Acquire::http::Pipeline-Depth "5";' | \
    sudo tee -a /etc/apt/apt.conf.d/99parallel-downloads

# Hız limiti ayarlama
echo 'Acquire::http::Dl-Limit "1000";' | \
    sudo tee /etc/apt/apt.conf.d/76download-limits
```

#### Disk Alanı Optimizasyonu
```bash
# Eski kernel paketlerini temizleme
sudo apt autoremove --purge

# Kullanılmayan bağımlılıkları bulma
deborphan

# Büyük paketleri listeleme
dpkg-query -W --showformat='${Installed-Size}\t${Package}\n' | sort -nr | head
```

### Otomatik Güncelleme ve Bakım

#### Unattended-Upgrades Yapılandırması
```bash
# Kurulum
sudo apt install unattended-upgrades

# Yapılandırma
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades

# Örnek yapılandırma:
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
    "${distro_id}:${distro_codename}-updates";
};

# Otomatik güncellemeyi etkinleştirme
sudo dpkg-reconfigure -plow unattended-upgrades
```

#### Periyodik Bakım Scripti
```bash
#!/bin/bash
# system_maintenance.sh

# Log dosyası
LOG="/var/log/system_maintenance.log"

echo "Bakım başladı: $(date)" >> $LOG

# Sistem güncellemesi
apt update >> $LOG 2>&1
apt upgrade -y >> $LOG 2>&1

# Temizlik işlemleri
apt autoremove -y >> $LOG 2>&1
apt clean >> $LOG 2>&1

# Disk kullanımı kontrolü
df -h >> $LOG 2>&1

# Paket sistemi kontrolü
dpkg --audit >> $LOG 2>&1

echo "Bakım tamamlandı: $(date)" >> $LOG
```

### Gelişmiş Hata Ayıklama

#### APT Debug Modu
```bash
# Debug modunda güncelleme
sudo apt update -o Debug::pkgProblemResolver=true

# Ayrıntılı kurulum logs
sudo apt install -o Debug::pkgProblemResolver=true paket_adi

# Tam debug çıktısı
sudo apt install -o Debug::pkgProblemResolver=true \
    -o Debug::Acquire::http=true paket_adi
```

#### Sistem Logs Analizi
```bash
# APT log analizi
grep "Error\|Warning" /var/log/apt/term.log

# DPKG log analizi
grep "error\|warning" /var/log/dpkg.log

# Paket işlem geçmişi
less /var/log/apt/history.log
```

### Güvenlik ve Denetim

#### Paket Doğrulama ve Güvenlik
```bash
# Paket imzalarını kontrol
apt-key verify paket.deb

# Paket bütünlük kontrolü
debsums -c paket_adi

# Güvenlik güncellemelerini kontrol
apt list --upgradable | grep -i security
```

#### Güvenlik Denetimi
```bash
# Yüklü paketlerin güvenlik taraması
sudo apt install debian-security-support
check-support-status

# CVE kontrolü
sudo apt install cve-tracker
cve-checker paket_adi
```

### İleri Seviye Scripting Örnekleri

#### Otomatik Paket Yönetimi
```bash
#!/bin/bash
# advanced_package_management.sh

# Konfigürasyon
BACKUP_DIR="/var/backups/packages"
LOG_FILE="/var/log/package_management.log"
ERROR_LOG="/var/log/package_errors.log"

# Fonksiyonlar
backup_package_list() {
    mkdir -p $BACKUP_DIR
    dpkg --get-selections > "$BACKUP_DIR/package_list_$(date +%F).txt"
}

update_system() {
    apt update >> $LOG_FILE 2>&1
    apt upgrade -y >> $LOG_FILE 2>&1
}

clean_system() {
    apt autoremove -y >> $LOG_FILE 2>&1
    apt clean >> $LOG_FILE 2>&1
}

check_errors() {
    if grep -i "error\|fail" $LOG_FILE; then
        echo "Hatalar tespit edildi: $(date)" >> $ERROR_LOG
        grep -i "error\|fail" $LOG_FILE >> $ERROR_LOG
    fi
}

# Ana program
echo "İşlem başladı: $(date)" >> $LOG_FILE
backup_package_list
update_system
clean_system
check_errors
echo "İşlem tamamlandı: $(date)" >> $LOG_FILE
```

### Sistem İzleme ve Raporlama

#### Detaylı Paket Raporu Oluşturma
```bash
#!/bin/bash
# package_report.sh

REPORT_FILE="/var/log/package_report.txt"

echo "Paket Raporu - $(date)" > $REPORT_FILE
echo "===================" >> $REPORT_FILE

# Toplam paket sayısı
echo "Kurulu Paket Sayısı:" >> $REPORT_FILE
dpkg -l | grep "^ii" | wc -l >> $REPORT_FILE

# En büyük 10 paket
echo -e "\nEn Büyük 10 Paket:" >> $REPORT_FILE
dpkg-query -W --showformat='${Installed-Size}\t${Package}\n' | \
    sort -nr | head >> $REPORT_FILE

# Son kurulan paketler
echo -e "\nSon Kurulan Paketler:" >> $REPORT_FILE
grep "install " /var/log/dpkg.log | tail -n 10 >> $REPORT_FILE

# Otomatik güncellenebilir paketler
echo -e "\nGüncellenebilir Paketler:" >> $REPORT_FILE
apt list --upgradable >> $REPORT_FILE
```

## İleri Seviye Sistem Yönetimi ve Best Practices

### Sistem Yedekleme ve Kurtarma

#### Paket Sistemi Yedekleme
```bash
# Tüm paket listesini yedekleme
dpkg --get-selections "*" > ~/paket_listesi.txt
sudo cp /var/lib/apt/extended_states ~/extended_states.backup

# APT kaynakları yedekleme
sudo tar -czf ~/apt_sources_backup.tar.gz /etc/apt/sources.list*

# Paket önbelleklerini yedekleme
sudo tar -czf ~/apt_cache_backup.tar.gz /var/cache/apt/archives/*.deb
```

#### Sistem Geri Yükleme
```bash
# Paket listesini geri yükleme
sudo dpkg --clear-selections
sudo dpkg --set-selections < ~/paket_listesi.txt
sudo apt-get dselect-upgrade

# APT kaynaklarını geri yükleme
sudo tar -xzf ~/apt_sources_backup.tar.gz -C /
sudo apt update
```

### Profesyonel Sistem Bakımı

#### Otomatik Bakım Planı
```bash
# /etc/cron.daily/system_maintenance
#!/bin/bash

# Günlük bakım işlemleri
apt-get update
apt-get -y upgrade
apt-get -y autoremove
apt-get clean

# Disk temizliği
find /var/log -type f -name "*.log" -mtime +30 -delete
find /var/cache/apt/archives/ -type f -name "*.deb" -delete

# Sistem kontrolü
dpkg --audit
apt-get check
```

#### Performans İzleme
```bash
# Sistem kaynak kullanımı raporu
#!/bin/bash
echo "Sistem Performans Raporu - $(date)"
echo "================================"

# CPU kullanımı
top -bn1 | head -n 5

# Bellek kullanımı
free -h

# Disk kullanımı
df -h

# En çok kaynak kullanan süreçler
ps aux --sort=-%mem | head -n 5
```

### Best Practices ve Güvenlik Önlemleri

#### Güvenli Paket Yönetimi İlkeleri
1. **Repo Güvenliği**
   ```bash
   # Sadece güvenilir repolar kullanın
   # Her yeni repo için GPG anahtarını doğrulayın
   apt-key fingerprint ANAHTAR_ID
   ```

2. **Düzenli Güncelleme Politikası**
   ```bash
   # Günlük güvenlik güncellemeleri
   unattended-upgrades --dry-run
   
   # Haftalık tam sistem güncellemesi
   apt update && apt upgrade -y
   ```

3. **Paket Doğrulama**
   ```bash
   # Kurulum öncesi paket bütünlüğü kontrolü
   dpkg --verify paket_adi
   
   # İmza kontrolü
   apt-key verify paket.deb
   ```

#### Sistem Güvenliği Kontrol Listesi
```bash
# 1. Repo listesi kontrolü
ls -l /etc/apt/sources.list.d/

# 2. Paket imzaları kontrolü
apt-key list

# 3. Şüpheli paketleri tespit
debsums -c

# 4. Güvenlik güncellemeleri kontrolü
apt list --upgradable | grep security
```

### Notlar ve İpuçları

#### Verimli Paket Yönetimi
1. **Düzenli Temizlik**
   ```bash
   # Haftalık temizlik rutini
   apt autoremove
   apt clean
   dpkg --purge $(dpkg -l | awk '/^rc/ {print $2}')
   ```

2. **Önbellek Optimizasyonu**
   ```bash
   # Önbellek boyutu sınırlama
   echo 'APT::Cache-Limit "100000000";' > /etc/apt/apt.conf.d/70cache-limit
   ```

3. **Hızlı Kurulum**
   ```bash
   # Paralel indirme
   echo 'Acquire::Queue-Mode "host";' > /etc/apt/apt.conf.d/99parallel
   ```

#### Sorun Giderme Kılavuzu

1. **Paket Kilitleri**
   ```bash
   # Kilit dosyalarını temizleme
   sudo rm /var/lib/dpkg/lock
   sudo rm /var/lib/apt/lists/lock
   sudo rm /var/cache/apt/archives/lock
   ```

2. **Bozuk Bağımlılıklar**
   ```bash
   # Bağımlılık onarımı
   sudo apt --fix-broken install
   sudo dpkg --configure -a
   ```

3. **Repository Sorunları**
   ```bash
   # Repo listesini yenileme
   sudo rm -rf /var/lib/apt/lists/*
   sudo apt update
   ```

# 3. Linux Sistem Yönetimi ve Güvenlik Rehberi

# 4. Linux Yetkilendirme Modeli

### Dosya İzinleri Sistemi

Linux'ta her dosya ve dizin için üç tür kullanıcı grubu vardır:
- Owner (Sahip)
- Group (Grup)
- Others (Diğerleri)

#### İzin Tipleri
```bash
r (read) = 4    # Okuma izni
w (write) = 2   # Yazma izni
x (execute) = 1 # Çalıştırma izni
```

#### İzinleri Okuma
```bash
ls -l
# Çıktı örneği:
# -rwxr-xr-- 1 kullanici grup 4096 Jan 1 12:00 dosya.txt
```

Bu çıktıyı inceleyelim:
- İlk karakter (`-`): Dosya tipini gösterir
  - `-`: Normal dosya
  - `d`: Dizin
  - `l`: Sembolik link
- Sonraki 9 karakter: İzinleri gösterir
  - `rwx`: Sahibin izinleri (4+2+1=7)
  - `r-x`: Grubun izinleri (4+0+1=5)
  - `r--`: Diğerlerinin izinleri (4+0+0=4)

#### İzin Değiştirme Örnekleri
```bash
# Sayısal yöntem
chmod 754 dosya.txt
# 7 (rwx) - Sahip için tam yetki
# 5 (r-x) - Grup için okuma ve çalıştırma
# 4 (r--) - Diğerleri için sadece okuma

# Sembolik yöntem
chmod u+x dosya    # Sahibine çalıştırma izni ekle
chmod g-w dosya    # Gruptan yazma iznini kaldır
chmod o=r dosya    # Diğerlerine sadece okuma izni ver
chmod a+x dosya    # Herkese çalıştırma izni ekle
```

### Özel İzinler

#### SUID (Set User ID)
```bash
# SUID biti ayarlama
chmod u+s /usr/bin/passwd
# Çıktı ls -l ile:
# -rwsr-xr-x

# Örnek kullanım:
# passwd komutu SUID biti ile çalışır
# Normal kullanıcı root yetkisiyle şifre değiştirebilir
```

#### SGID (Set Group ID)
```bash
# SGID biti ayarlama
chmod g+s /shared/folder
# Çıktı ls -l ile:
# drwxrws---

# Örnek:
# /shared/folder altında oluşturulan tüm dosyalar
# otomatik olarak dizinin grubu ile aynı gruba sahip olur
```

#### Sticky Bit
```bash
# Sticky bit ayarlama
chmod +t /tmp
# Çıktı ls -l ile:
# drwxrwxrwt

# Örnek:
# /tmp dizininde herkes dosya oluşturabilir
# ama sadece dosya sahibi silebilir
```

### ACL (Access Control Lists)

ACL, standart Linux izin sisteminin ötesinde daha detaylı izin kontrolü sağlar.

#### ACL Kullanım Örnekleri
```bash
# ACL'leri görüntüleme
getfacl dosya.txt
# Çıktı örneği:
# file: dosya.txt
# owner: kullanici
# group: grup
# user::rwx
# user:john:r--
# group::r-x
# mask::rwx
# other::r--

# Kullanıcı için ACL ekleme
setfacl -m u:john:rx dosya.txt

# Grup için ACL ekleme
setfacl -m g:proje:rwx dosya.txt

# Tüm ACL'leri kaldırma
setfacl -b dosya.txt

# Dizin ve alt öğelerine ACL uygulama
setfacl -Rm u:john:rx dizin/
```

### Sudo Yapılandırması

Sudo, normal kullanıcıların belirli komutları root yetkisiyle çalıştırmasını sağlar.

#### Sudo Konfigürasyonu
```bash
# Sudo dosyasını düzenleme
visudo

# Örnek kurallar:
# Tek komut izni
john ALL=(ALL) /usr/bin/apt

# Şifresiz komut çalıştırma
john ALL=(ALL) NOPASSWD: /usr/bin/systemctl status

# Grup izinleri
%sudo ALL=(ALL:ALL) ALL

# Belirli komutları reddetme
john ALL=(ALL) ALL,!/usr/bin/passwd root
```

#### Sudo Kullanım Örnekleri
```bash
# Komut çalıştırma
sudo apt update

# Root shell'e geçiş
sudo -i

# Başka kullanıcı olarak çalıştırma
sudo -u postgres psql

# Sudo log inceleme
sudo grep sudo /var/log/auth.log
```

### Pratik Güvenlik Senaryoları

#### Senaryo 1: Paylaşımlı Proje Dizini
```bash
# Proje dizini oluşturma
sudo mkdir /proje
sudo groupadd proje_ekibi
sudo chown root:proje_ekibi /proje
sudo chmod 2775 /proje

# Açıklama:
# 2: SGID biti
# 775: rwxrwxr-x izinleri
# Tüm yeni dosyalar proje_ekibi grubuna ait olur
```

#### Senaryo 2: Güvenli Web Sunucu
```bash
# Apache dizini güvenliği
sudo chown -R root:www-data /var/www/html
sudo chmod -R 750 /var/www/html
sudo chmod -R g+s /var/www/html

# SSL sertifika dizini
sudo chmod 700 /etc/ssl/private
sudo chown root:root /etc/ssl/private
```

#### Senaryo 3: Log Dizini Yönetimi
```bash
# Log dizini izinleri
sudo chmod -R 640 /var/log/*
sudo chown -R root:adm /var/log/*

# Özel log dosyası
sudo touch /var/log/ozel.log
sudo chown app_user:app_group /var/log/ozel.log
sudo chmod 660 /var/log/ozel.log
```

# 5. Arşiv ve Yedekleme

### Tar ile Arşivleme

Tar, Linux sistemlerde en yaygın kullanılan arşivleme aracıdır.

#### Temel Tar Komutları
```bash
# Arşiv oluşturma
tar -cvf arsiv.tar dosyalar/
# c: create (oluştur)
# v: verbose (detaylı göster)
# f: file (dosya adı belirt)

# Sıkıştırılmış arşiv oluşturma
tar -czvf arsiv.tar.gz dosyalar/   # gzip sıkıştırma
tar -cjvf arsiv.tar.bz2 dosyalar/  # bzip2 sıkıştırma
tar -cJvf arsiv.tar.xz dosyalar/   # xz sıkıştırma

# Arşiv içeriğini listeleme
tar -tvf arsiv.tar

# Arşiv açma
tar -xvf arsiv.tar
tar -xzvf arsiv.tar.gz
tar -xjvf arsiv.tar.bz2
```

#### Pratik Tar Örnekleri
```bash
# Belirli dosyaları arşivleme
tar -czvf kod.tar.gz *.py *.js

# Hariç tutma
tar -czvf yedek.tar.gz /home --exclude=/home/*/tmp

# Arşivi belirli dizine açma
tar -xzvf arsiv.tar.gz -C /hedef/dizin/

# Arşiv içinden tek dosya çıkarma
tar -xzvf arsiv.tar.gz dosya.txt
```

### Rsync ile Yedekleme

Rsync, güçlü ve verimli bir dosya senkronizasyon aracıdır.

#### Temel Rsync Kullanımı
```bash
# Yerel kopyalama
rsync -av kaynak/ hedef/
# a: archive mode (arşiv modu)
# v: verbose (detaylı göster)

# Uzak sunucuya yedekleme
rsync -avz -e ssh kaynak/ kullanici@sunucu:/hedef/
# z: sıkıştırma kullan

# İlerlemeyi gösterme
rsync -avz --progress kaynak/ hedef/

# Kuru çalıştırma (dry-run)
rsync -avzn kaynak/ hedef/
```

#### Gelişmiş Rsync Örnekleri
```bash
# Ayna yedekleme (silinen dosyaları hedefte de sil)
rsync -av --delete kaynak/ hedef/

# Belirli dosyaları hariç tutma
rsync -av --exclude='*.tmp' --exclude='temp/' kaynak/ hedef/

# Yedek alma ve eski dosyaları koruma
rsync -av --backup --backup-dir=/yedek/$(date +%Y%m%d) kaynak/ hedef/

# Bant genişliği sınırlama
rsync -av --bwlimit=1000 kaynak/ hedef/
```

### DD ile Disk Yedekleme

DD, düşük seviyeli kopyalama aracıdır ve tam disk imajı almak için kullanılır.

#### DD Kullanım Örnekleri
```bash
# Tam disk imajı alma
dd if=/dev/sda of=/yedek/disk.img bs=4M status=progress

# Sıkıştırılmış imaj alma
dd if=/dev/sda bs=4M | gzip > disk.img.gz

# İmajı geri yükleme
dd if=disk.img of=/dev/sda bs=4M status=progress

# Sıkıştırılmış imajı geri yükleme
gunzip -c disk.img.gz | dd of=/dev/sda bs=4M status=progress
```

### Otomatik Yedekleme Stratejileri

#### Cron ile Zamanlanmış Yedekleme
```bash
# Crontab düzenleme
crontab -e

# Örnek zamanlamalar:
# Her gün gece 2'de yedek alma
0 2 * * * tar -czf /yedek/gunluk_$(date +\%Y\%m\%d).tar.gz /veri/

# Her Pazar tam yedek, diğer günler artımlı yedek
0 1 * * 0 rsync -av --delete /veri/ /yedek/tam/
0 1 * * 1-6 rsync -av --link-dest=/yedek/tam /veri/ /yedek/artimli/$(date +\%Y\%m\%d)/
```

#### Yedekleme Scripti Örneği
```bash
#!/bin/bash
# backup.sh

# Değişkenler
BACKUP_DIR="/yedek"
SOURCE_DIR="/veri"
DATE=$(date +%Y%m%d)
LOG_FILE="/var/log/backup.log"

# Yedek dizini kontrolü
if [ ! -d "$BACKUP_DIR" ]; then
    mkdir -p "$BACKUP_DIR"
fi

# Yedekleme fonksiyonu
do_backup() {
    echo "Yedekleme başladı: $(date)" >> "$LOG_FILE"
    
    # Tam yedek alma
    tar -czf "$BACKUP_DIR/tam_$DATE.tar.gz" "$SOURCE_DIR" \
        --exclude="*.tmp" \
        --exclude="temp/" \
        2>> "$LOG_FILE"
    
    # Eski yedekleri temizleme (30 günden eski)
    find "$BACKUP_DIR" -name "tam_*.tar.gz" -mtime +30 -delete
    
    echo "Yedekleme tamamlandı: $(date)" >> "$LOG_FILE"
}

# Disk alanı kontrolü
check_space() {
    SPACE=$(df -h "$BACKUP_DIR" | awk 'NR==2 {print $4}')
    if [[ "${SPACE%G}" -lt 10 ]]; then
        echo "Uyarı: Yetersiz disk alanı" >> "$LOG_FILE"
        exit 1
    fi
}

# Ana program
check_space
do_backup
```

### Yedekleme Best Practices

1. **3-2-1 Kuralı**
   - 3 kopya yedek
   - 2 farklı medya türü
   - 1 offsite yedek

2. **Yedek Doğrulama**
```bash
# Arşiv bütünlük kontrolü
tar -tvf yedek.tar.gz

# Checksum oluşturma ve kontrol
sha256sum yedek.tar.gz > yedek.sha256
sha256sum -c yedek.sha256
```

3. **Yedek Rotasyonu**
```bash
# Günlük-Haftalık-Aylık rotasyon scripti
#!/bin/bash
# Günlük yedekler (son 7 gün)
find /yedek/gunluk -mtime +7 -delete

# Haftalık yedekler (son 4 hafta)
find /yedek/haftalik -mtime +28 -delete

# Aylık yedekler (son 12 ay)
find /yedek/aylik -mtime +365 -delete
```

4. **Felaket Kurtarma Planı**
```bash
# Sistem konfigürasyon yedekleme
tar -czf /yedek/sistem_conf.tar.gz \
    /etc/fstab \
    /etc/passwd \
    /etc/group \
    /etc/shadow \
    /etc/ssh/sshd_config

# Paket listesi yedekleme
dpkg --get-selections > /yedek/paket_listesi.txt
```

# 6. Dosya Düzenleyicileri

### Vim Editörü

Vim, güçlü ve özelleştirilebilir bir metin editörüdür.

#### Vim Modları
1. **Normal Mod** (Başlangıç modu)
2. **Insert Mod** (Düzenleme modu)
3. **Visual Mod** (Seçim modu)
4. **Command Mod** (Komut modu)

#### Temel Vim Komutları
```bash
# Dosya açma
vim dosya.txt

# Temel düzenleme komutları
i         # Insert moduna geç
Esc       # Normal moda dön
:w        # Kaydet
:q        # Çık
:wq       # Kaydet ve çık
:q!       # Kaydetmeden çık
u         # Geri al
Ctrl + r  # İleri al
```

#### Gelişmiş Vim Kullanımı
```bash
# Metin düzenleme
dd        # Satır sil
yy        # Satır kopyala
p         # Yapıştır
/metin    # Metin ara
n         # Sonraki eşleşme
:%s/eski/yeni/g  # Tüm dosyada değiştir

# Çoklu dosya
:sp dosya2.txt   # Yatay bölme
:vsp dosya2.txt  # Dikey bölme
Ctrl + w + ok    # Pencereler arası geçiş
```

#### Vim Konfigürasyonu
```bash
# ~/.vimrc dosyası
syntax on               # Sözdizimi renklendirme
set number             # Satır numaraları
set autoindent         # Otomatik girinti
set tabstop=4         # Tab genişliği
set expandtab         # Tab'ları boşluğa çevir
set hlsearch          # Arama vurgulama
```

### Nano Editörü

Nano, başlangıç seviyesi kullanıcılar için daha basit bir editördür.

#### Temel Nano Kullanımı
```bash
# Dosya açma
nano dosya.txt

# Temel komutlar
Ctrl + O    # Kaydet
Ctrl + X    # Çık
Ctrl + K    # Satır kes
Ctrl + U    # Yapıştır
Ctrl + W    # Ara
Ctrl + \    # Değiştir
Ctrl + G    # Yardım
```

#### Nano Konfigürasyonu
```bash
# ~/.nanorc dosyası
set linenumbers      # Satır numaraları
set autoindent      # Otomatik girinti
set tabsize 4       # Tab genişliği
set backup          # Yedek dosya oluştur
```

### Emacs Editörü

Emacs, çok yönlü ve programlanabilir bir editördür.

#### Temel Emacs Komutları
```bash
# Dosya açma
emacs dosya.txt

# Temel komutlar
C-x C-s    # Kaydet (Ctrl + x, Ctrl + s)
C-x C-c    # Çık
C-k        # Satır kes
C-y        # Yapıştır
C-s        # İleri ara
C-r        # Geri ara
C-g        # İşlemi iptal et
```

#### Emacs Özel Özellikleri
```bash
M-x speedbar         # Dosya gezgini
M-x shell           # Terminal
M-x compile         # Kod derleme
M-x gdb             # Debugger
```

### Metin İşleme Komutları

#### Dosya İçeriği Görüntüleme
```bash
# cat komutu
cat dosya.txt                  # Tüm içeriği göster
cat -n dosya.txt              # Satır numaralı göster
cat dosya1.txt dosya2.txt     # Birden fazla dosya

# less komutu
less dosya.txt                # Sayfa sayfa görüntüle
/aranacak                     # Metin ara
n                            # Sonraki eşleşme
q                            # Çık

# head ve tail
head -n 5 dosya.txt          # İlk 5 satır
tail -n 10 dosya.txt         # Son 10 satır
tail -f /var/log/syslog      # Canlı log takibi
```

#### Metin Arama ve Düzenleme
```bash
# grep ile arama
grep "metin" dosya.txt       # Basit arama
grep -i "metin" dosya.txt    # Büyük/küçük harf duyarsız
grep -r "metin" dizin/       # Özyinelemeli arama
grep -v "metin" dosya.txt    # Eşleşmeyen satırlar

# sed ile değiştirme
sed 's/eski/yeni/' dosya.txt         # İlk eşleşmeyi değiştir
sed 's/eski/yeni/g' dosya.txt        # Tüm eşleşmeleri değiştir
sed -i 's/eski/yeni/g' dosya.txt     # Dosyayı değiştir

# awk ile alan işleme
awk '{print $1}' dosya.txt           # İlk sütun
awk -F":" '{print $1}' /etc/passwd   # Kullanıcı adları
awk '$3 > 1000 {print $1}' dosya.txt # Koşullu yazdırma
```

### Pratik Örnekler

#### Log Analizi Scripti
```bash
#!/bin/bash
# log_analiz.sh

# Hata sayısını bul
echo "Hata Sayısı:"
grep -i "error" /var/log/syslog | wc -l

# Son 10 başarısız giriş
echo -e "\nSon Başarısız Girişler:"
grep "Failed password" /var/log/auth.log | tail -n 10

# IP adresleri ve sayıları
echo -e "\nBaşarısız Giriş Yapan IP'ler:"
grep "Failed password" /var/log/auth.log | \
    awk '{print $11}' | sort | uniq -c | sort -nr
```

#### Toplu Dosya Düzenleme
```bash
#!/bin/bash
# dosya_duzenle.sh

# Tüm Python dosyalarında string değiştirme
find . -name "*.py" -type f -exec sed -i 's/eski_string/yeni_string/g' {} \;

# Tüm dosyalarda satır sonu düzeltme
find . -type f -exec dos2unix {} \;

# Dosya başlıklarına tarih ekleme
for file in *.txt; do
    echo "# $(date) - $file" | cat - "$file" > temp && mv temp "$file"
done
```

# 7. Yürütülebilir Dosyalar ve Programlar

### Program Derleme ve Kurulum

#### Kaynak Koddan Derleme
```bash
# Temel derleme adımları
./configure              # Sistem kontrolü ve Makefile oluşturma
make                    # Kaynak kodu derleme
sudo make install       # Programı sisteme kurma

# Örnek: Nginx derleme
tar xzf nginx-1.20.1.tar.gz
cd nginx-1.20.1
./configure --prefix=/usr/local/nginx \
           --with-http_ssl_module \
           --with-http_v2_module
make
sudo make install

# Derleme bağımlılıklarını kurma
sudo apt install build-essential   # Temel derleme araçları
sudo apt install libssl-dev       # SSL kütüphanesi
sudo apt install zlib1g-dev       # Sıkıştırma kütüphanesi
```

#### Program Kurulum Yönetimi
```bash
# Kurulu programları listeleme
which nginx             # Program konumunu bulma
whereis python         # Tüm ilgili dosyaları bulma
type bash             # Komut tipini gösterme

# Program kaldırma
sudo make uninstall    # Derlenmiş programı kaldırma
sudo rm -rf /usr/local/nginx  # Manuel temizlik
```

### Path ve Çevre Değişkenleri

#### Path Yönetimi
```bash
# Mevcut PATH'i görüntüleme
echo $PATH

# Geçici PATH ekleme
export PATH=$PATH:/yeni/dizin

# Kalıcı PATH ekleme
echo 'export PATH=$PATH:/yeni/dizin' >> ~/.bashrc
source ~/.bashrc

# Sistem geneli PATH ekleme
sudo echo 'export PATH=$PATH:/yeni/dizin' > /etc/profile.d/custom_path.sh
```

#### Çevre Değişkenleri
```bash
# Çevre değişkenlerini görüntüleme
env                    # Tüm değişkenler
echo $HOME            # Tek değişken

# Geçici değişken tanımlama
export JAVA_HOME=/usr/lib/jvm/java-11

# Kalıcı değişken tanımlama
echo 'export JAVA_HOME=/usr/lib/jvm/java-11' >> ~/.bashrc

# Sistem geneli değişken
sudo echo 'JAVA_HOME=/usr/lib/jvm/java-11' > /etc/environment
```

### Servis Yönetimi

#### SystemD Servisleri
```bash
# Servis durumu kontrolü
systemctl status nginx
systemctl is-active mysql
systemctl is-enabled apache2

# Servis yönetimi
systemctl start postgresql
systemctl stop mongodb
systemctl restart docker
systemctl reload nginx    # Konfigürasyonu yeniden yükle

# Otomatik başlatma
systemctl enable redis
systemctl disable memcached
```

#### Özel Servis Oluşturma
```bash
# /etc/systemd/system/myapp.service
[Unit]
Description=My Custom Application
After=network.target

[Service]
Type=simple
User=myapp
ExecStart=/usr/local/bin/myapp
Restart=always
WorkingDirectory=/opt/myapp

[Install]
WantedBy=multi-user.target

# Servisi etkinleştirme
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
```

### Process Yönetimi

#### Process İzleme
```bash
# Process listeleme
ps aux               # Tüm processler
ps -ef | grep nginx  # Belirli process arama
pstree              # Process ağacı

# Sistem kaynakları
top                 # Anlık sistem durumu
htop                # Gelişmiş sistem monitörü
free -h             # Bellek kullanımı
df -h               # Disk kullanımı
```

#### Process Kontrolü
```bash
# Process sonlandırma
kill PID            # Normal sonlandırma (SIGTERM)
kill -9 PID         # Zorla sonlandırma (SIGKILL)
killall nginx       # İsme göre sonlandırma

# Process önceliği
nice -n 10 ./script.sh    # Düşük öncelikle başlat
renice -n -5 PID         # Çalışan processin önceliğini değiştir
```

#### Background İşlemler
```bash
# Arkaplan yönetimi
command &           # Arkaplanda başlat
Ctrl + Z           # Processi durdur
bg                 # Arkaplanda devam ettir
fg                 # Önplana getir
jobs               # Arkaplan işlemlerini listele

# Screen kullanımı
screen             # Yeni oturum
screen -ls         # Oturumları listele
Ctrl + a + d       # Oturumdan ayrıl
screen -r ID       # Oturuma geri dön
```

### Çalıştırma Seviyeleri (Runlevels)

#### SystemD Hedefleri
```bash
# Mevcut hedefi görüntüleme
systemctl get-default

# Hedef değiştirme
systemctl set-default multi-user.target  # CLI modu
systemctl set-default graphical.target   # GUI modu

# Acil durum hedefleri
systemctl isolate emergency.target
systemctl isolate rescue.target
```

#### Özel Başlangıç Scriptleri
```bash
# /etc/rc.local alternatifi
sudo nano /etc/systemd/system/rc-local.service

[Unit]
Description=RC Local
After=network.target

[Service]
Type=oneshot
ExecStart=/etc/rc.local
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target

# rc.local oluşturma
sudo nano /etc/rc.local
#!/bin/bash
# Özel başlangıç komutları
exit 0

sudo chmod +x /etc/rc.local
sudo systemctl enable rc-local
```

### Pratik Örnekler

#### Otomatik Program Güncelleme Scripti
```bash
#!/bin/bash
# update_apps.sh

# Log dosyası
LOG="/var/log/app_updates.log"

# Güncelleme fonksiyonu
update_app() {
    echo "$(date): $1 güncelleniyor..." >> $LOG
    cd /opt/$1
    git pull
    make
    make install
    echo "$(date): $1 güncelleme tamamlandı" >> $LOG
}

# Uygulamaları güncelle
for app in app1 app2 app3; do
    if [ -d "/opt/$app" ]; then
        update_app $app
    fi
done
```

#### Process İzleme ve Alarm Scripti
```bash
#!/bin/bash
# process_monitor.sh

# Kontrol edilecek process
PROCESS="nginx"
EMAIL="admin@example.com"

# Process kontrolü
if ! pgrep -x "$PROCESS" > /dev/null; then
    echo "UYARI: $PROCESS çalışmıyor!"
    systemctl start $PROCESS
    echo "Process yeniden başlatıldı" | \
        mail -s "Process Uyarısı" $EMAIL
fi

# Kaynak kullanımı kontrolü
MEMORY=$(ps aux | grep $PROCESS | awk '{sum+=$4} END {print sum}')
if [ "${MEMORY%.*}" -gt 80 ]; then
    echo "UYARI: Yüksek bellek kullanımı!" | \
        mail -s "Bellek Uyarısı" $EMAIL
fi
```

# 8. UNIX/Linux Dizin Yapısı ve Log Dosyaları

### Temel Dizin Yapısı ve Açıklamaları

#### Kök Dizin (/) Yapısı
```bash
/                   # Kök dizin
├── bin/           # Temel sistem komutları
├── boot/          # Önyükleme dosyaları
├── dev/           # Aygıt dosyaları
├── etc/           # Sistem yapılandırması
├── home/          # Kullanıcı dizinleri
├── lib/           # Sistem kütüphaneleri
├── media/         # Çıkarılabilir medya
├── mnt/           # Geçici bağlama noktaları
├── opt/           # İsteğe bağlı yazılımlar
├── proc/          # Sistem bilgileri
├── root/          # Root kullanıcı dizini
├── sbin/          # Sistem yönetimi komutları
├── srv/           # Servis verileri
├── tmp/           # Geçici dosyalar
├── usr/           # Kullanıcı programları
└── var/           # Değişken veriler
```

#### Önemli Dizinlerin Detaylı Açıklamaları

1. **/etc Dizini**
```bash
/etc/
├── passwd         # Kullanıcı bilgileri
├── shadow         # Şifre bilgileri
├── group          # Grup bilgileri
├── sudoers        # Sudo yapılandırması
├── ssh/           # SSH yapılandırması
├── nginx/         # Nginx yapılandırması
├── apache2/       # Apache yapılandırması
└── systemd/       # SystemD yapılandırması
```

2. **/var Dizini**
```bash
/var/
├── log/           # Sistem logları
├── cache/         # Uygulama önbellekleri
├── spool/         # Kuyruk dosyaları
├── www/           # Web dosyaları
└── backups/       # Yedekler
```

3. **/usr Dizini**
```bash
/usr/
├── bin/           # Kullanıcı komutları
├── sbin/          # Sistem komutları
├── lib/           # Kütüphaneler
├── local/         # Yerel kurulumlar
└── share/         # Paylaşılan veriler
```

### Log Dosyaları ve Log Yönetimi

#### Temel Log Dosyaları
```bash
# Sistem logları
/var/log/syslog        # Genel sistem logları
/var/log/auth.log      # Kimlik doğrulama logları
/var/log/kern.log      # Kernel logları
/var/log/dmesg         # Boot logları

# Uygulama logları
/var/log/apache2/      # Apache web sunucu logları
/var/log/nginx/        # Nginx web sunucu logları
/var/log/mysql/        # MySQL veritabanı logları
/var/log/postgresql/   # PostgreSQL veritabanı logları
```

#### Log İzleme Komutları
```bash
# Canlı log takibi
tail -f /var/log/syslog
tail -f /var/log/auth.log | grep "Failed password"

# Journalctl kullanımı
journalctl                    # Tüm logları göster
journalctl -u nginx          # Nginx logları
journalctl -f               # Canlı log takibi
journalctl --since "1 hour ago"  # Son 1 saatin logları
```

#### Log Rotasyonu
```bash
# logrotate yapılandırması
/etc/logrotate.d/nginx
```
```conf
/var/log/nginx/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 0640 www-data adm
    sharedscripts
    postrotate
        [ -f /var/run/nginx.pid ] && kill -USR1 `cat /var/run/nginx.pid`
    endscript
}
```

# 9. Kali Linux Güvenlik Araçları

### Bilgi Toplama Araçları

#### Network Keşif Araçları

1. **Nmap (Network Mapper)**
```bash
# Temel tarama
nmap 192.168.1.0/24

# Detaylı tarama
nmap -sV -sC -O -p- 192.168.1.100
# -sV: Servis/versiyon tespiti
# -sC: Varsayılan scriptlerle tarama
# -O: İşletim sistemi tespiti
# -p-: Tüm portları tara

# Gizli tarama
nmap -sS -Pn -n --data-length 15 target.com
```

2. **Nikto (Web Sunucu Tarayıcı)**
```bash
# Temel web taraması
nikto -h http://target.com

# SSL ile tarama
nikto -h https://target.com -ssl

# Detaylı rapor
nikto -h target.com -Format htm -output rapor.html
```

3. **Recon-ng (Bilgi Toplama Çerçevesi)**
```bash
# Recon-ng başlatma
recon-ng

# Temel komutlar
workspaces create proje1
marketplace search
marketplace install recon/domains-hosts/google_site_web
modules load recon/domains-hosts/google_site_web
```

### Güvenlik Açığı Analiz Araçları

#### Web Uygulama Güvenlik Testleri

1. **SQLMap (SQL Injection Aracı)**
```bash
# Temel tarama
sqlmap -u "http://target.com/page.php?id=1"

# Otomatik veritabanı çıkarma
sqlmap -u "http://target.com/page.php?id=1" --dbs

# Tablo ve sütun çıkarma
sqlmap -u "http://target.com/page.php?id=1" \
    -D database_name --tables --columns

# Shell alma
sqlmap -u "http://target.com/page.php?id=1" --os-shell
```

2. **Burp Suite (Web Güvenlik Testi)**
```bash
# Proxy yapılandırması
export HTTPS_PROXY="http://127.0.0.1:8080"

# SSL sertifikası kurulumu
curl http://burp/cert -o burp.der
openssl x509 -in burp.der -inform DER -out burp.pem
```

3. **OWASP ZAP**
```bash
# Komut satırından başlatma
zap.sh -daemon -host 0.0.0.0 -port 8080

# API kullanımı
curl 'http://localhost:8080/JSON/core/action/accessUrl/?url=http://target.com'
```

### Şifre Kırma Araçları

#### Hash ve Şifre Kırma

1. **Hashcat**
```bash
# MD5 hash kırma
hashcat -m 0 hash.txt wordlist.txt

# Windows NTLM hash kırma
hashcat -m 1000 ntlm.txt wordlist.txt

# GPU hızlandırma
hashcat -m 0 -d 1 hash.txt wordlist.txt

# Kural tabanlı saldırı
hashcat -m 0 hash.txt -r rules/best64.rule wordlist.txt
```

2. **John the Ripper**
```bash
# Shadow dosyası kırma
john /etc/shadow

# Belirli format
john --format=md5crypt hash.txt

# Wordlist kullanma
john --wordlist=wordlist.txt hash.txt
```

#### Kablosuz Ağ Güvenlik Testleri

1. **Aircrack-ng Suite**
```bash
# Monitor moda geçiş
airmon-ng start wlan0

# Ağları tarama
airodump-ng wlan0mon

# WPA handshake yakalama
airodump-ng -c 1 --bssid MAC -w capture wlan0mon
aireplay-ng -0 2 -a MAC wlan0mon

# WPA şifre kırma
aircrack-ng -w wordlist.txt capture-01.cap
```

2. **Wifite**
```bash
# Otomatik tarama ve saldırı
wifite --dict wordlist.txt

# Belirli ağa saldırı
wifite --bssid MAC --dict wordlist.txt
```

### Ağ Analiz Araçları

#### Paket Analizi

1. **Wireshark**
```bash
# Komut satırından yakalama
tshark -i eth0 -w capture.pcap

# Belirli protokol filtreleme
tshark -i eth0 -f "port 80"

# SSL trafiği analizi
tshark -i eth0 -f "port 443" -o ssl.keys_list:443,private.key
```

2. **Tcpdump**
```bash
# Temel paket yakalama
tcpdump -i eth0

# HTTP trafiği
tcpdump -i eth0 port 80 -w http.pcap

# Belirli IP filtreleme
tcpdump host 192.168.1.100
```

### Güvenlik Duvarı ve IDS

#### Güvenlik Duvarı Yönetimi

1. **IPTables**
```bash
# Temel kurallar
iptables -L
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -j DROP

# NAT kuralları
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

# Kural kaydetme
iptables-save > /etc/iptables/rules.v4
```

2. **UFW (Uncomplicated Firewall)**
```bash
# Temel kullanım
ufw enable
ufw allow 22/tcp
ufw deny from 192.168.1.100
```

#### Saldırı Tespit Sistemleri

1. **Snort**
```bash
# Konfigürasyon testi
snort -T -c /etc/snort/snort.conf

# Paket yakalama ve analiz
snort -dev -l /var/log/snort

# Kural bazlı çalıştırma
snort -c /etc/snort/snort.conf -l /var/log/snort
```

### Adli Analiz Araçları

#### Disk Analizi

1. **Autopsy**
```bash
# Yeni vaka oluşturma
autopsy -h localhost -p 9999

# Komut satırından analiz
autopsy -t disk.img -o rapor.txt
```

2. **DD**
```bash
# Disk imajı alma
dd if=/dev/sda of=disk.img bs=4M status=progress

# Belirli bölüm imajı
dd if=/dev/sda1 of=partition.img bs=4M
```

#### Bellek Analizi

1. **Volatility**
```bash
# Process listesi
volatility -f memory.dump imageinfo
volatility -f memory.dump pslist

# Network bağlantıları
volatility -f memory.dump netscan

# Şifre dökümü
volatility -f memory.dump hashdump
```

### Sosyal Mühendislik Araçları

1. **SET (Social Engineering Toolkit)**
```bash
# Toolkit başlatma
setoolkit

# Phishing saldırısı
1) Social-Engineering Attacks
2) Website Attack Vectors
3) Credential Harvester Attack Method
```

2. **GoPhish**
```bash
# Kampanya oluşturma
gophish &
# Web arayüzü: http://localhost:3333
```

### Raporlama ve Dokümantasyon

1. **Dradis**
```bash
# Proje başlatma
dradis start

# Rapor oluşturma
dradis export -f html -t pentest
```

2. **MagicTree**
```bash
# Yeni proje
magictree
# Test sonuçlarını içe aktarma ve raporlama
```

### Güvenlik Testi Senaryoları

#### Web Uygulama Testi
```bash
#!/bin/bash
# web_test.sh

TARGET="http://target.com"

# Nikto taraması
nikto -h $TARGET -o nikto.txt

# SQLMap taraması
sqlmap -u "$TARGET" --batch --dbs -o sqlmap.txt

# Dirb ile dizin taraması
dirb $TARGET -o dirb.txt

# Sonuçları birleştir
echo "Web Güvenlik Raporu" > rapor.txt
cat nikto.txt sqlmap.txt dirb.txt >> rapor.txt
```

#### Ağ Güvenlik Testi
```bash
#!/bin/bash
# network_test.sh

NETWORK="192.168.1.0/24"

# Nmap taraması
nmap -sV -sC -O $NETWORK -oA nmap_scan

# Açık portları izle
tcpdump -i eth0 -w network.pcap &

# Güvenlik duvarı testi
for port in {1..1000}; do
    nc -zv $TARGET $port 2>&1 | grep "succeeded"
done
```
