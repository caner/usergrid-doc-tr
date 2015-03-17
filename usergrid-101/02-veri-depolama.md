# Veri Depolama

Turkcell Usergrid'in API destekli bir arkayüz servisi olma özelliği, uygulamalarınızın tüm verilerini hızlı ve verimli olmak kaydıyla esnek ve güvenli bir ortamda saklayıp yönetmenizi sağlar. NoSQL veritabanından gelen gücü sayesinde herşeyi JSON nesnesi olarak saklayabilir ve değiştirebilirsiniz. Buna ikili (binary) formatta tüm dosyalarınızı barındırmak ve üzerlerinde standart CRUD (Create, Retrieve, Update, Delete) işlevlerini gerçekleştirmek de dâhildir.



## Usergrid API'nin veri modeli

Usergrid veri deposu, uygulamanızın tüm verilerini (bundan sonra veri varlıkları olarak anılacak) JSON nesnelerinden oluşan koleksiyonlar şekilde modelleyebileceğiniz basit ve genişletilebilir bir yapı sunar. Her varlığın "type" isminde bir özelliği vardır ve bu sayede doğru isimlendirilmiş bir koleksiyonla ilişkilendirilirler.

API destekli arkayüz servisi Usergrid, gücünü NoSQL veritabanından (Apache Cassandra) aldığından herhangi bir varlık tipi için zorla uymanız gereken bir şema yoktur. Bu özelliği verilerinizi modellerken size enfes bir esneklik verir ve varlıklar ile özelliklerinin atomik seviyede tanımlanıp güncellemesine de olanak tanır. Bu şu anlama da gelir: isterseniz farklı koleksiyonlar arasındaki farklı varlıklar arasında ilişkiler kurabilirsiniz.

### Koleksiyonlar

Geleneksel ilişkisel veritabanındaki **tablolara benzer** şekilde **koleksiyonlar**, ortak amaç veya özellikteki birçok varlığı gruplamak amacıyla sıkça kullanılır. Usergrid veri deposundaki verilerinizle çalışırken genellikle varlık seviyesinde çalışacaksınız ancak toplu güncelleme işlemleri gibi tüm koleksiyona API isteği yapmak gibi kullanışlı işler yaptığınız zamanlar da olacaktır.

Koleksiyonlar hakkında daha ayrıntılı bilgileri almaya [buradaki bağlantıdan]() başlayabilirsiniz.

### Varlıklar

Geleneksel ilişkisel veritabanındaki **satırlara benzer** olarak, UserGrid veri deposundaki **varlıklar** tek bir kaydı temsil ederler ve JSON nesnesi olarak saklanır. Her varlık "uuid", "created" gibi varsayılan özellikler içermektedir ve bunlar varlık oluşturulduğunda API tarafından otomatik eklenir. Varlıkların JSON formatına uygun, anahtar-değer (key-value) çiftleri olacak şekilde istenildiği kadar özelliği olabilir. Bu durum varlıkların neredeyse her şeyi temsil etmesini ve uygulamanızın gerektirdiği kadar çok veya az bağlamsal veri saklanmasını sağlamaktadır.

Varlıklar hakkında daha ayrıntılı bilgileri almaya [bu bağlantıdaki]() eğitimimizden başlayabilirsiniz.