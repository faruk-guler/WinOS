# 📜 ADCS Microsoft Certificate Services Migration

## HSM ve Ön Hazırlıklar:

 ```cmd
-Eğer CA donanım güvenlik modülü (HSM) kullanıyorsa, HSM üreticisinin sağladığı prosedürleri izleyin.
-Eğer kaynak sunucudan CA rolünü kaldırmadan hedef sunucuya yüklemek istiyorsanız, hedef sunucuda AD CS hizmetini (CertSvc) devre dışı bırakın.
-Eski CA’yı hemen kapatmak yerine bir süre paralel çalıştırın; istemcilerin yeni CA’dan sertifika alıp CRL doğrulaması yaptığını gözlemleyin.
-CA rol hizmetini kaynak sunucudan kaldırılması önerilir. önce CertSvc hizmetini durdurun. CA rol hizmetini kaldırmak, CA’nın yapılandırma verilerini AD DS’den temizler.
-CA rolü kaldırıldıktan sonra sunucuyu yeniden başlatın.
 ```

## Yedeklenecek Dosya ve Klasörler:
 ```cmd
C:\Windows\System32\CertSrv\
C:\Windows\System32\CertSrv\CertEnroll
C:\Windows\System32\CertSrv\Database [olmayabilir.]
%SystemRoot%\System32\CertLog
CAPolicy.inf dosyasını yedekleyin [Kaynak CA'nız özel bir CAPolicy.inf dosyası kullanıyorsa]
 ```

## Kritik CA Bileşenlerinin Yedeklenmesi:
 ```cmd
certutil -backupDB C:\CA-Backup
certutil -backupKey C:\CA-Backup
certutil -template > C:\CA-Backup\CA_Sablonlari_Listesi.txt
 ```

## Regedit Yedeklenmesi:
 ```cmd
HKLM\SYSTEM\CurrentControlSet\Services\CertSvc
HKLM\SYSTEM\CurrentControlSet\Services\CertSvc\Configuration
reg export HKLM\SYSTEM\CurrentControlSet\Services\CertSvc\Configuration C:\CA-Backup\CA-Registry.reg
 ```

## Taşıma Öncei Uzatılmış CRL Yayınlama:
 ```cmd
-CRL süresini uzatma: Taşıma uzun sürecekse CRL geçerlilik süresini uzatmak hataları azaltır; ancak zorunlu değildir.
-AIA/CDP kontrolleri: Yeni sunucuda AIA ve CDP URL’lerinin erişilebilirliğini doğrulayın; hostname veya IP değişirse URL’leri güncelleyin.
-CRL yayın noktalarının erişilebilirliği (HTTP/LDAP) ve AD replikasyonu kontrol edilmeli.
 ```

# 📌 YENİ SERVER HAZIRLIKLARI VE MIGRATION

## Önemli:
 ```cmd
Server 2022-2025 etki alanına (domain’e) join edilmiş olmalı
Saat, tarih, timezone eşleşmesi doğru olmalı
DNS / AD replikasyonu düzgün çalışmalı
Aynı CA adı kullanılacak (çok önemli)
Aynı CA türü seçilmelidir:
 ```

## Rollerin kurulumu:
 ```cmd
-Certification Authority
-Certification Authority Web Enrollment
-
-
*Enterprise Root CA
*Enterprise Subordinate CA
*Standalone CA
*
 ```

## CA Geri Yükleme-Import and Services:
 ```cmd
Stop-Service -Name CertSvc
reg import C:\CA-Backup\CA-Registry.reg
certutil -restoreDB C:\Users\Administrator\Desktop\CA-Backup
certutil -restoreKey
Start-Service -Name CertSvc
 ```

## Verifying the Migration:
 ```cmd
certutil -store -enterprise root
certutil -ping
certutil -crl
certutil -pulse
ldp.exe → Connection > Connect → Port 636 → SSL bağlantısı başarılı mı?
flags değeri 10 olmalı (ADSI Edit → CN=Enrollment Services)
adsiedit.msc bağlan > CN = CertificateTemplates, CN = Public Key Services, CN = Services, CN = Configuration, DC = *ForestRootDomainName*, DC = *com/local/...*
 ```

## Son Sözler:
 ```cmd
Yeni sunucunun farklı bir bilgisayar adı varsa, reg dosyasını editleyin. "CAServerName" değerini yeni Sunucu adı ile değiştirin.
CA taşındıktan sonra eski sunucu kapatılabilir veya offline bırakabilir yada silinebilir.
Hatta bazı durumlarda taşıma test edilene kadar eski CA’yı açık bırakmak güvenlidir.
Eski sunucu üzerindeki CA rolü kaldırılmadığı sürece istemciler hala eski CA’yı görebilir, CRL ve sertifika sorgulamaları çalışır.
 ```

## 🔗 Links:
 ```cmd
https://learn.microsoft.com/en-us/windows-server/identity/ad-cs/migrate-certification-authority?tabs=server-manager
https://www.starwindsoftware.com/blog/migrate-root-ca-to-a-new-server/
https://www.petenetlive.com/KB/Article/0001473
https://www.cozumpark.com/windows-server-2012-certificate-services-migration-to-windows-server-2022/
 ```
