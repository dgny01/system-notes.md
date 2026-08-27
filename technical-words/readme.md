Why Kafka?

Servisleri decouple etmek, event’leri asenkron işlemek ve yüksek throughput sağlamak için.

Why partitions?

Topic’i paralelleştirerek write/read throughput’u artırmak için.

Why same key to same partition?

İlişkili event’lerin ordering’ini korumak için.

What is a consumer group?

Aynı servisin consumer kopyalarının partition’ları paylaşarak paralel tüketim yapması.

What is an offset?

Consumer group’un partition içinde hangi konuma kadar ilerlediğini gösteren konum bilgisi.

What happens when a consumer crashes?

Kafka rebalance yapar, partition başka consumer’a atanır ve committed offset’ten devam edilir.

How do you handle duplicate events?

Consumer’ı idempotent tasarlarım; unique event ID ile daha önce işlenmiş event’leri takip ederim.

What is acks=all?

Producer’a başarı cevabı verilmeden önce gerekli in-sync replica koşullarının sağlanmasını ister; durability yükselir ama latency artar.



Low CPU doesn't mean the server has spare application capacity. Storage demand exceeds its IOPS capacity, so I would investigate I/O saturation and tasks blocked on I/O. That could also explain the high load average despite low CPU utilization.

A successful write() does not necessarily mean the data has reached persistent storage. Without an explicit durability guarantee such as fsync(), a crash may cause recently written data to be lost.
