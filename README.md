# Redis_Cache_Delete_Operations
 
Redis Cache Delete Operations
1- DEL ve  UNLINK
Redis'te veri silmek için temelde DEL komutunu kullanırız ve bu işlem atomic çalışır.
Eğer büyük key'ler varsa ve main thread'i bloklamak istemiyorsak DEL yerine UNLINK kullanırız, unlink silme işlemini background thread'e atar
2 - Expire Silme Mekanizması (Passive – Active)
passif silme işlemi cache için get işlemi yapıldığında süresi dolmuş bir veri ise silinir. 
active silme ise her 100ms de redis süresi dolmuş verileri siler 

3- Redis Eviction (Bellek Dolu Olduğunda Silme)
Redis’in bellek limiti dolduğunda yeni bir veri yazmak istediğinde yer açmak için bazı keyleri otomatik silmesidir.  sonrasında veriyi redise yazar. 
En az kullanılanı (LRU) - En az erişilen keyi siler (LFU) -TTL süresi en yakında dolacak olanı siler - random silme
4- Pattern Silme (belirli keylerin toplu olarak silinmesi)
örnek :  user* ⇒ user ile başlayan tüm keyleri sil. bu işlem için öncelikle SCAN ile veriler aranıp sonra Unlike ile silinmesi sağlıklı olacaktır. SCAN → UNLINK
5- Redis Cluster’da Silme
Rediste yapısal olarak kayıt edilen keyler farklı node karda tutulur
Bunun için silme işleminde
Diyelim dek user 1 user 2 user 3 diye ister atılırsa ve keyler farklı node lardaysa cross-slot hatası verir bunun basit çözümü bu sorguları tek tek
Del user 1
Del user 2
Del user 3 olarak atmak
Ama temel çözüm scab unlike ile silmektir
Böylece silinecek olan verileri belirlenip bir duraksama olmadan veriler silinebilir
6- Distributed Lock
Dağınık cache yapısı kod ile yapılabilir. bir key için gelen silme ve güncelleme isteklerinin birbirini bekleyerek yapması üzerine kurulu bir yapıdır. 
product ekibi için bir uygulama yaptık ve ürün kodu yazıp butona bastıklarında silecek ve güncelleme yapacak. aynı anda birden fazla kullanıcı bu isteği atarsa sağlıklı bir güncelleme yapılmaz bunun için cache locklanır ve işlem bittiğinde diğer işlem başlar.
microserice yapısında user 1 için bir veriye erişilmek isteniliyor güncelleme işlemleri sadece customer projesinden yapılır.  
