# ADCS Microsoft Certificate Services Migration

## HSM ve Ön Hazırlıklar

 ```cmd
-Eğer CA donanım güvenlik modülü (HSM) kullanıyorsa, HSM üreticisinin sağladığı prosedürleri izleyin.
-Eğer kaynak sunucudan CA rolünü kaldırmadan hedef sunucuya yüklemek istiyorsanız, hedef sunucuda AD CS hizmetini (CertSvc) devre dışı bırakın.
-Eski CA’yı hemen kapatmak yerine bir süre paralel çalıştırın; istemcilerin yeni CA’dan sertifika alıp CRL doğrulaması yaptığını gözlemleyin.
-CA rol hizmetini kaynak sunucudan kaldırılması önerilir. önce CertSvc hizmetini durdurun. CA rol hizmetini kaldırmak, CA’nın yapılandırma verilerini AD DS’den temizler.
-CA rolü kaldırıldıktan sonra sunucuyu yeniden başlatın.
 ```

## 
