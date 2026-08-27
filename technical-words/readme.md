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


The main symptom is a p99 latency of 2.4 seconds. Load average is 26 on an 8-core machine, so I would initially investigate whether the load comes from runnable tasks or tasks in uninterruptible I/O wait. CPU utilization is only 22%, which makes CPU saturation less likely. Meanwhile, high disk utilization, high I/O latency and a growing I/O queue strongly suggest storage saturation. Available memory is healthy, swap activity is minimal and major page faults are low, so memory pressure is also less likely. My leading hypothesis would therefore be storage saturation causing tasks to spend time waiting for I/O, potentially in D-state. The growing queue increases waiting time and particularly hurts tail requests, explaining the high p99
