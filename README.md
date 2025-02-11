#Moblnformation Programı


##1)PROGRAMIN TEMEL AMACI
Programın tasarladığım kısmınn temel amacı ağa bağlı olan 14 adet el terminalinin uzaktan oturumunu sonlandırmak.


##2)TASARIM SÜRECİNDE İZLEDİĞİM YOL HARİTASI


ilk olarak basic toolar ile basit bir form tasarladım ihtiyacım olan 14 adet buttonu koydum
ve labeları ekleyip buttonları şekilendırdım ve form tasarımı ihtiyaçlarımı karşılayacak hale getirdim


##3)RDP OTURUMUNU NASIL SONLANDIRACAĞIM ?
RDP oturumunu uzak masaüstü bağlantılarını yöneterek nasıl sonlandıracağımı araştırmaya başladım ve bir kaç yöntem buldum
ilk bulduğum yöntem psexec metoduydu bu metodun amacı:


##3.1)PSEXEC NEDİR?
Psexec sizle aynı ağa bağlı olan kullanıcı oturumlarını listeler ve bunlar üzerinde gerekli yetkiler ile kontol sağlamanıza yarar


##3.2)PSEXEC Yİ NASIL KULLANDIM
ağdaki oturumları listeledikten sonra cmd üzerinden seçilen ID ye göre oturumu sonlandırıyordu fakat bu yöntemi manuel olarak çalışıyordu buda bana sorun yaratı ben tam otomatik bir sistem istiyordum program ise listelerken kullanılan ID yi almak için farklı bir komut logof vermek için farklı bir komut ve en son olarak benden oturum kapatma aşamasında bir admin şifresi istiyordu bu yüzden psexec kullanmaktan vazgeçtim


##4)QWİNSTA VE RWİNSTA KOMUTU
psexec metodu işime yaramayınca direk komut üzerinden herşeyi otomatik bir yönteme bağlamayı araştırmaya başladım. qwinsta ve rwinsta komutlarını keşfetim bu iki komut ile psexec ile hedeflediğim herşeyi kodlar ile devam ederek yapabilirdim
qwinsta ağda kayıtlı kullanıcıları listeler ve ID leri verir rwinsta ise gösterilen ID yi seçer ve oturumu kapatır temelinde işime yarayan komutları bir metin dosyasında denedim ve işime yaradı şimdi bu kodu otomatik hale getirmem gerekiyordu bu şekilde manuel olan tüm işlemleri otomatik hale getirebilecektim şu şekilde yaptım tğm el terminalerine bir metin belgesi .bat dosyası açtım ve kullanıcı adlarına bir değişken atadım ve bu kullanıcıların seçilmesini sağladım seçildikten sonra logof komutu ile oturumu kapatım
formda eklemiş olduğum tüm buttonlara .bat dosyalarını atadım ve basıldığı zaman çalıştırmasını istedim ve çalıştırdı güvenlik sebebiyle domainuserı ve admin şifresini şifrelemek istedim ve AES şifreleme yöntemi ile 
domain userı ve admin şifresini base64 formatında şifreledim ve bir json dosyasında sakladım fakat ne yaparsam yapayım benden oturum kapatma evresinde admin şifresini istiyordu sonradan fark ettimki bu kullandığım yöntemde admin şifresini şifrelemenin bir yolu yoktu


##5)FORM DETAYLI TASARIM
Eklediğim buttonların ve labeların sizeını orantılayıp buttonları transparan yaptım ardından arka plan rengini transparan yaptım ve renk düzenini sağladım yazı büyüklüklerini ve fontlarını ayarlayıp tasarımı bitirdim tasarım için ek kütüphaneler kullandım 
using 2D drawing
using 3D drawing 
ve benzeri tasarım kütüphaneleri


##6)ADMİN ŞİFRESİNİN ÇÖZÜMÜ VE PSEXEC YE GERİ DÖNÜŞ 
Önceden psexec nin kendi komutlarını kullanarak yapmaya çalıştığım sistemi .bat ile yapmayı denedim ve başardım daha önceden yapmış olduğum her el termimali için açmış olduğum .bat dosyalarını psexec ye uyarladım ve onlar üzerinden runas komutu logof komutlarını otomatikleştirdim önceden şifrelemiş olduğum admin şifresini değişken olarak aldım ve değişkenede json dosyasına tanımlamış olduğum admin şifresini atadım ve program çalışır hale geldi.


##7)PROGRAM KULLANICI DENETLEMESİ
Programı edinen tüm bilgisayarlarda çalışmaması için bir bir yöntem geliştirdim bir label ekledim ve görünmez yaptım label programın çalıştığı bilgisayarın kullanıcı ismini alacak ve gösterecek ardındna bir liste oluşturdum listede programı çalıştırabilecek bilgisayar isimlerini ekledim eğer listede isim varsa program sorunsuz çalışacak ama listede ismi yoksa buttona basıldığı zaman yetkiniz yok diye bir messagebox ta hata verecek bu sayede bir filtreleme yöntemi eklemiş oldum


##7)PROGRAMIN ÇALIŞMA ÖZETİ
program çalıştırılır ve buttona basılır
buttona basıldığı anda ilk olarak görünmez labelda yazan isimle listedeki isim aynımı kontrol eder
eğer aynı ise çalışır değil ise yetkiniz yoktur
ardından json dosyasındaki değişkenlerdeki veriyi alır
.bat dosyasını çalıştırır
json dosyasındaki veriyi aktarır yetki varsa ve doğru ise 
psexec çalışır ve ilk başta aktif kullanıcıları listeler(kullanıcı göremez)
eğer listede seçili kullanıcı varsa oturumunu sonlandırır yoksa kullanıcı bulunamadı hatası cmd ekranı üzerinden gözükür oturum kapatıldıktan sonra görünmez cmd ekranı kendini kapatır 
ve formda bir messagebox çıkar oturum başarı ile sonlandırıldı diye


##  .bat Dosyaları ve PsExec Metodu  

```batch
@echo off
set "server=111.111.1.11"
set "username=kullanici"
set "psexecPath=C:\Windows\System32\PsExec.exe"

echo Kullanıcı kontrol ediliyor...

for /f "tokens=3" %%A in ('%psexecPath% \%server% qwinsta ^| findstr /R /C:"%username%"') do (
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
```

---

##  Şifreleme Yöntemi  

Aşağıdaki C# kodu, AES şifreleme metodunu kullanarak verileri çözer:  

```csharp
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
```

---

##  JSON Dosyasından Kullanıcı Bilgilerini Okuma  

```csharp
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
    }
}
```

---

## Bat dosyasına okuma ve çalıştırma  

```csharp
private void button1_Click(object sender, EventArgs e) 
{
    try 
    {
        // Yetkili bilgisayar isimleri listesi
        List<string> authorizedMachines = new List<string> { "ITSTAJYERNEW", "PC2", "PC3" };

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
```

---



                                                                






