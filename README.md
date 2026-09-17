I saw CacheWarden on your CV. It sounds interesting. Could you walk me through the problem you were trying to solve, what you built, and what you found so far?

---I built three components: a victim, an aggressor, and a load generator.

I wanted to investigate whether two containerized workloads running on the same machine could interfere with each other through shared hardware resources.

First, I measured the victim alone as a baseline. Then I enabled the aggressor and repeated the same test.

Across five runs, the victim's latency increased and the number of completed requests decreased when the aggressor was active.

So I reproduced the interference, but I haven't proven the exact root cause yet.


You said the workloads interfere through shared hardware resources. What exactly do you mean by shared hardware resources? Which resources could be shared between your victim and aggressor?

---For example, the last-level cache and memory bandwidth can be shared between workloads. If the aggressor creates heavy memory traffic, it may affect the victim's access to these shared resources. CPU frequency and package-level power behavior could also affect performance. But at this stage, these are possible causes. I haven't proven which one is responsible yet


Okay. But your aggressor was also using around 200% CPU. How do you know the victim wasn't simply getting less CPU time because of CPU contention?

---That was one of the possibilities I considered. The aggressor was using around 200% CPU, so I wanted to check whether the victim was simply receiving less CPU time.

I measured the victim's task-clock using perf. It was about 9.98 seconds in the baseline and 9.99 seconds with the aggressor active.

So, in that paired measurement, the victim received almost the same CPU execution time. That suggests simple CPU scheduling starvation was not the main explanation for the slowdown


Okay, so the victim got roughly the same CPU time. But you also told me its effective CPU frequency dropped from about 4.19 GHz to 3.78 GHz. If task-clock stayed the same, why would lower CPU frequency make the application slower?

---Task-clock tells me how much CPU execution time the victim received, but it doesn't tell me how much work the CPU could complete during that time.

In my measurement, task-clock was almost unchanged, but effective frequency dropped from about 4.19 to 3.78 GHz. At a lower frequency, the CPU has fewer cycles available per second, so the victim can make less progress during roughly the same CPU execution time.

I also observed a small IPC decrease, so frequency alone doesn't explain everything. That's why I treat the frequency drop as a possible contributor, not as the proven root cause.


Your project is called CacheWarden, but you just told me that you haven't actually proven cache contention. Isn't the name misleading? Why are you building a cache-related project before proving that cache is the problem?

---My first goal wasn't to prove that cache was the problem. First, I wanted to create a reproducible interference scenario and measure its impact on the victim.

Once I reproduced the latency increase, I started investigating the cause step by step. It could involve cache behavior, memory bandwidth, CPU frequency, or a combination of them.

So even though the project started with a cache-related idea, I'm letting the measurements determine the actual direction.

You said you reproduced the interference. How did you make sure that the slowdown wasn't just random variation? How was your benchmark designed?

---I designed two conditions: baseline and interference. In the baseline, only the victim was running. In the interference condition, I enabled the aggressor.

For both conditions, I used the same load generator configuration: twenty seconds with a concurrency of four. The important point was to keep the workload parameters the same and mainly change whether the aggressor was active.

I repeated this experiment five times. All five runs showed the same general direction: latency increased and completed requests decreased when the aggressor was active. So the result was repeatable rather than just a single observation.

“Five runs showed the same direction, so it wasn't random.”
This reduced the likelihood that I was looking at a one-off fluctuation, although five runs are still limited and I haven't done a full statistical analysis.

Okay. Your benchmark shows roughly 29% higher mean latency and 23% fewer completed requests. But Docker showed that the victim was still using around 104% CPU and around 40 MiB of memory. Why isn't docker stats enough to diagnose the problem? Why did you need perf?

---Docker stats wasn't enough because it mainly showed me resource utilization. The victim was still using around the same CPU and memory, even though its performance was worse.

So I needed to look deeper into how the CPU was executing the workload. I used perf to measure task-clock, cycles and instructions, and I calculated IPC. I also looked at effective CPU frequency.

This helped me see that the victim was getting roughly the same CPU time, but its frequency and IPC were lower during interference. It still didn't tell me the exact root cause, but it gave me more information than CPU and memory utilization alone.
