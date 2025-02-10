Moblnformation Programı

Programın benim tasarladığım kısmının temel amacı ağa bağlı olan 14 adet el terminalinin uzaktan otumunu sonlandırmak programı tasarlarken
izlediğim yol haritası ve denediğim çalışmalar sırası ile şu şekilde ilk olarak basic toolar ile basit bir form tasarladım ihitiyacım olan 
14 adet buttonu koydum ve label ları ekleyip buttonları şekilendirdim form tasarımı ihtiyaçlarımı karşılayacak hale geldikten sonra RDP oturumunu
nasıl sonlandıracağımı araştırmaya başladım ve bir kaç farklı yöntem buldum bunlardan ilk denediğim şey psexec metoduydu bu metodun amacı
ağdaki kişileri listeliyor ardından cmd den seçilen ID ye göre oturumu sonlandırıyordu bu yöntem manuel olarak çalışıyordu fakat bir problem vardı 
bu yöntem sadece manuel olarak çalışıyordu bir otomasyona bağlayamıyordum önce domainuserı sonra şifre ardından açılan açılan cmd ekranında oturumu
seçip /logof komutu ile kendimiz kapatıyorduk programı otomatik bir hale alamamıştım. ardından harici bir metod yerine cmd temeli qwinsta ve rwinsta
komutlarını kullanmaya karar verdim bu komutlarn işlevleri şu şekildedir qwinsta ağda aktif olan oturumları listeler ve ID lerini verir rwinsta ise
seçili ID yi seçer ve ve oturumu kapatır temelinde işime yarayan komutlardı bir metin dosyasında denediğim vakit işe yaradı şimdi bu kodu otomatik 
hale getirmem gerekiyordu ve şu şekilde yaptım tüm el terminaleri yani tüm kullanıcılar için bir bat dosyası oluşturdum kullanıcı adlarını bir değişkene atadım
ve onları seçmesini sağladım ardından seçtikten sonra direk oturumu kapatması için log of komutunu ekledim ama benden yetki için admin şifresi istiyordu ama kodun çalışma
prensibinde her türlü açılan cmd ekranında bende kapatmadan önce admin şifresi istiyordu bende admin kullanıcı adını domain userı bir değişkene atadım ve kodun içine attım çalışır hale gedli herşey otomatikti
sadece oturumu kapatmadan önce admin şifresi istiyordu güvenlik açısından şifrelemiş olduğum DomainUserı AES şifreleme yöntemi ile base64 formatında şifreleyip
programa yerleştridim ardından çalışır hale geldi fakat güvenlik sebeplerinden dolayı ve admin şifresini programı kullanacak olan her kullanıcıya veremeyeceğimden dolayı admin şifresini şifrelemek için
geri psexec metoduna döndüm qwinsta ve rwinsta metodunda yaptıklarımı psexec metoduna bir .bat dosyası üzerinden uyguladım ve psexec oturum sonlandırma işleminde şifre istemediği için
admin şifresinide şifreledim ve program çalışır hale geldi ardından form tasarımına geçtim tasarım kütüphaneleri kullanarak arka plan rengini ayarladım
buttonların bulunduğu kısmı transparan yaptım button içlerini aynı şekilde şefaf bir görüntü için transparan yaptım text boyutlarını ve fontlarını ayarladım
tasarımı tamamladıktan sonra uygulamayı edinen tüm bilgisayarların kullanmaması için bir filtre ekledim filtre şu şekilde çalışıyor bir label ekledim label bilgisayarın machine name ini alacak ardından yazdıracak bunun görünmez olmasını sağladım sadece kontrol amaçlı
ardından eğer labelda benim listeye eklediğim bilgisayar isimleri varsa tüm buttonlar çalışır hale gelecek labelda liste dışı bir isim varsa buttona basıldığı zaman messahebox ile bir uyarı verecek
yetkiniz yok diye program bu şekilde


Bahsetiğim metinde kullandığım kodlar
.bat kodlar ve psexec metodu
@echo off
set "server=192.168.2.42"
set "username=et001"
set "psexecPath=C:\Windows\System32\PsExec.exe"

echo Kullanıcı kontrol ediliyor...

for /f "tokens=3" %%A in ('%psexecPath% \\%server% qwinsta ^| findstr /R /C:"%username%"') do (
    echo Oturum bulundu, ID: %%A
    echo Oturum kapatılıyor...

    %psexecPath% \\%server% rwinsta %%A

    if %errorlevel% neq 0 (
        echo Hata: Oturum kapatılamadı!
    ) else (
        echo Oturum başarıyla kapatıldı.
    )

    echo Kalan oturumlar listeleniyor...
    %psexecPath% \\%server% qwinsta
)

echo İşlem tamamlandı.

Şifreleme Yöntemi

  private string decryptData(string encryptedText)
  {
      try
      {
          byte[] encryptedBytes = Convert.FromBase64String(encryptedText);
          byte[] iv = fixedIV;
          byte[] actualCiphertext = encryptedBytes.Skip(16).ToArray();
          string keyString = "your-16-character-key";
          byte[] key = Encoding.UTF8.GetBytes(keyString.PadRight(16, '0').Substring(0, 16));

          using (RijndaelManaged rijAlg = new RijndaelManaged())
          {
              rijAlg.Key = key;
              rijAlg.IV = iv;
              rijAlg.Mode = CipherMode.CBC;
              rijAlg.Padding = PaddingMode.PKCS7;

              using (var decryptor = rijAlg.CreateDecryptor(rijAlg.Key, rijAlg.IV))
              using (var ms = new MemoryStream(actualCiphertext))
              using (var cs = new CryptoStream(ms, decryptor, CryptoStreamMode.Read))
              {
                  byte[] decryptedData = new byte[actualCiphertext.Length];
                  int bytesRead = cs.Read(decryptedData, 0, decryptedData.Length);
                  return Encoding.UTF8.GetString(decryptedData, 0, bytesRead).Trim('\0');
              }
          }
      }
      catch (Exception ex)
      {
          MessageBox.Show("Şifre çözme hatası: " + ex.Message);
          return null;
      }
  }

 string jsonContent = File.ReadAllText(jsonFilePath);
 dynamic config = JsonConvert.DeserializeObject(jsonContent);

 string encryptedDomainUser = config.domainUser;
 string encryptedDomainPassword = config.domainPassword;

 byte[] encryptedBytes = Convert.FromBase64String(encryptedDomainUser);

 byte[] iv = fixedIV;
 byte[] actualCiphertext = encryptedBytes.Skip(16).ToArray();

 string keyString = "your-16-character-key";
 byte[] key = Encoding.UTF8.GetBytes(keyString.PadRight(16, '0').Substring(0, 16));

 using (RijndaelManaged rijAlg = new RijndaelManaged())
 {
     rijAlg.Key = key;
     rijAlg.IV = iv;
     rijAlg.Mode = CipherMode.CBC;
     rijAlg.Padding = PaddingMode.PKCS7;

     using (var decryptor = rijAlg.CreateDecryptor(rijAlg.Key, rijAlg.IV))
     using (var ms = new MemoryStream(actualCiphertext))
     using (var cs = new CryptoStream(ms, decryptor, CryptoStreamMode.Read))
     {
         byte[] decryptedData = new byte[actualCiphertext.Length];
         int bytesRead = cs.Read(decryptedData, 0, decryptedData.Length);
         string domainUser = Encoding.UTF8.GetString(decryptedData, 0, bytesRead).Trim('\0');

         string batFilePath = @"C:\\Users\\itstajyer\\Desktop\\MobelAgent-master\\rdp_Connection\\et001.bat";
         if (!File.Exists(batFilePath))
         {
             MessageBox.Show($"Belirtilen .bat dosyası bulunamadı: {batFilePath}");
             return;
         }


.bat çalıştırma dosya okuma
  private void button1_Click(object sender, EventArgs e)
  {
      try
      {
          // Yetkili bilgisayar isimleri listesi
          List<string> authorizedMachines = new List<string> { "ITSTAJYERNEW", "PC2", "PC3" }; // Buraya yetkili bilgisayar isimlerini ekleyin

          string currentMachineName = label2.Text; // Makine adını label2'den al

          // Yetki kontrolü
          if (!authorizedMachines.Contains(currentMachineName))
          {
              MessageBox.Show("Yetkiniz yok!", "Uyarı", MessageBoxButtons.OK, MessageBoxIcon.Warning);
              return;
          }

          string jsonFilePath = Path.Combine(Application.StartupPath, "Domainuser.json");

          if (!File.Exists(jsonFilePath))
          {
              MessageBox.Show($"{jsonFilePath} dosyası bulunamadı!");
              return;
          }

          string jsonContent = File.ReadAllText(jsonFilePath);
          dynamic config = JsonConvert.DeserializeObject(jsonContent);

          string encryptedDomainUser = config.domainUser;
          string encryptedDomainPassword = config.domainPassword;

          byte[] encryptedBytes = Convert.FromBase64String(encryptedDomainUser);

          byte[] iv = fixedIV;
          byte[] actualCiphertext = encryptedBytes.Skip(16).ToArray();

          string keyString = "your-16-character-key";
          byte[] key = Encoding.UTF8.GetBytes(keyString.PadRight(16, '0').Substring(0, 16));

          using (RijndaelManaged rijAlg = new RijndaelManaged())
          {
              rijAlg.Key = key;
              rijAlg.IV = iv;
              rijAlg.Mode = CipherMode.CBC;
              rijAlg.Padding = PaddingMode.PKCS7;

              using (var decryptor = rijAlg.CreateDecryptor(rijAlg.Key, rijAlg.IV))
              using (var ms = new MemoryStream(actualCiphertext))
              using (var cs = new CryptoStream(ms, decryptor, CryptoStreamMode.Read))
              {
                  byte[] decryptedData = new byte[actualCiphertext.Length];
                  int bytesRead = cs.Read(decryptedData, 0, decryptedData.Length);
                  string domainUser = Encoding.UTF8.GetString(decryptedData, 0, bytesRead).Trim('\0');

                  string batFilePath = @"C:\\Users\\itstajyer\\Desktop\\MobelAgent-master\\rdp_Connection\\et001.bat";
                  if (!File.Exists(batFilePath))
                  {
                      MessageBox.Show($"Belirtilen .bat dosyası bulunamadı: {batFilePath}");
                      return;
                  }

                  ExecuteBatFileWithProgress(batFilePath);
              }
          }
      }
      catch (Exception ex)
      {
          MessageBox.Show("Hata: " + ex.Message);
      }
  }







