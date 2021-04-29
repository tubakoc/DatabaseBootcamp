System Infrastructure Bootcamp - Database
1.CASE
Database işlemleri için ilk önce docker ve couchbase kurulumu yapıldı. 

Docker Kurulumu: 
Docker kurulumu yapmadan önce eski sürümler var ise bunlar silinmelidir.
#sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
komutu ile bu işlem yapılır.

![01](https://user-images.githubusercontent.com/28953086/116467866-da7b7d00-a878-11eb-947c-7eb1120d7096.png)


#sudo yum install -y yum-utils 
Komutu ile yum-utils install edilir.
![02](https://user-images.githubusercontent.com/28953086/116467908-e6673f00-a878-11eb-8467-3507305daa5d.png)


#sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
![03](https://user-images.githubusercontent.com/28953086/116467964-f848e200-a878-11eb-95ed-7c2cd0c2f1bb.png)


#ls /etc/yum.repos.d/
komutu ile makinedeki repolar listelenir. 

![04](https://user-images.githubusercontent.com/28953086/116468012-05fe6780-a879-11eb-9b14-278a6bb3ded9.png)


Yazılan komutta paketler install edildi. Bunlar default olarak deaktif geldi bunları aktifleştirmek için aşağıdaki komutlar yazıldı.


# sudo yum-config-manager --enable docker-ce-nightly

![05](https://user-images.githubusercontent.com/28953086/116468037-0dbe0c00-a879-11eb-9bb4-8345f4d4ba56.png)



Test kanalını etkinleştirmek için 
#sudo yum-config-manager --enable docker-ce-test

![06](https://user-images.githubusercontent.com/28953086/116468078-1a426480-a879-11eb-9e11-f0a6d536cf5b.png)


Aktifleştirme işlemleri bittikten sonra Docker Engine ve containerd son sürümü 
#sudo yum install docker-ce docker-ce-cli containerd.io  
komutu ile install edilir. 

![07](https://user-images.githubusercontent.com/28953086/116468106-21697280-a879-11eb-948a-f4796287f1f3.png)


#yum list docker-ce --showduplicates | sort –r  komutu ile makinede bulunan sürümler listelenir.

![08](https://user-images.githubusercontent.com/28953086/116468135-2b8b7100-a879-11eb-9740-9216591e5e99.png)


Docker yüklendi ancak henüz başlatılmadı.
# sudo systemctl start docker 
komutu ile yüklenen docker sürümü başlatılır.

![09](https://user-images.githubusercontent.com/28953086/116468170-347c4280-a879-11eb-9b36-570b7a886ca1.png)


Başlatılan Docker ın çalışıp çalışmadığını kontrol etmek için 
# sudo docker run hello-world
komutu ile default Hello World çalıştırılır.

![10](https://user-images.githubusercontent.com/28953086/116468196-3ba35080-a879-11eb-8060-447540a20380.png)


Docker kurulumu ve ayağa kaldırma bittikten sonra Couchbase install etme ve ayağa kaldırma işlemine başlanır.

# curl -O https://packages.couchbase.com/releases/couchbase-release/couchbase-release-1.0-x86_64.rpm
Komutu ile meta paketi download edilir.
# sudo rpm -i ./couchbase-release-1.0-x86_64.rpm
Komutu ile meta paketi install edilir.
Couchbase server son sürümü indirmek için 
# sudo yum install couchbase-server komutu yazılır.
#yum list --showduplicates couchbase-server komutu ile mevcut sürümler listelenir.

Couchbase kurulumu bu adımlarla yapılabildiği gibi docker ile de yapılabilir.
Bunun için önce docker kurulumu yapılmalıdır. Daha önce docker kurulumu yapılmıştı makinede.
#docker run -d --name db -p 8091-8096:8091-8096 -p 11210-11211:11210-11211 couchbase
Komutu ile Couchbase Server container image install edilir.

![11](https://user-images.githubusercontent.com/28953086/116468222-42ca5e80-a879-11eb-96a7-6f50e41ff1bb.png)


# docker logs db
Komutu ile container günlüklerine bakılır.
Bir web tarayıcısından, Couchbase Web Konsoluna erişmek için http: // localhost: 8091'e gidilir.

![12](https://user-images.githubusercontent.com/28953086/116468279-54ac0180-a879-11eb-9691-8e00372afe8d.PNG)




![13](https://user-images.githubusercontent.com/28953086/116468339-67263b00-a879-11eb-8c6e-2146eaa967b7.jpeg)

Cluster setup ı gerçekleştirmek için gerekli adımlar yapılır.


PHP de Couchbase database Kullanımı:
1.	PHP de Couchbase database kullanılabilmek için PHP SDK kurulmalıdır. 
pecl install https://packages.couchbase.com/clients/php/couchbase-3.1.1.tgz


2.	Database bağlantı kodu aşağıdaki gibidir:
$connectionString = "couchbase://localhost";
$options = new \Couchbase\ClusterOptions();
$options->credentials("Administrator", "password");
$cluster = new \Couchbase\Cluster($connectionString, $options);

3.	İlgili bucket açılır. 
$bucket = $cluster->bucket("bucket-name"); // Oluşturulan database, Örnek olarak travel-sample bucket oluşturulabilir.

4.	Referans verme
$collection = $bucket->defaultCollection(); // Collection referansı almak için
$collection = $bucket->scope("myapp")->collection("my-collection"); // isim collection u 

5.	Veri yazma
$upsertResult = $collection->upsert("my-document", ["name" => "Tuba"]);

6.	Veri okuma
$getResult = $collection->get("my-document");


![14](https://user-images.githubusercontent.com/28953086/116468313-5ece0000-a879-11eb-8b5a-3c667a9521f4.jpeg)





