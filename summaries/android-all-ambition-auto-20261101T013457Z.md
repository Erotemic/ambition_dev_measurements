# Android profile summary

## Status
- profile-build: 0
- simpleperf-record: 0
- adb-pull-perf: 0
- simpleperf-device-callgraph: 0
- simpleperf-device-flat-comm-dso-symbol: 0
- simpleperf-host-callgraph: 127
- simpleperf-host-flat-comm-dso-symbol: 127
- simpleperf-stat: 0
- gfxinfo-after-record: 0
- gfxinfo-framestats-after-record: 0
- perf.data bytes: 7319698

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 17974. Samples lost: 0.

## Top simpleperf report lines
```text
4.81%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.15%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
0.96%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.92%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.91%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.82%     android_main     /data/app/~~GNADuPUr7Wz9i9kvU1rJ9g==/org.erotemic.ambition.sandbox-zx4BEDTFKqEr0pAdjoU81w==/lib/arm64/libambition_app.so  libambition_app.so[+2d205b7c]
0.69%     Async Compute T  /apex/com.android.runtime/lib64/bionic/libm.so                                                                            atan2f
0.36%     android_main     /data/app/~~GNADuPUr7Wz9i9kvU1rJ9g==/org.erotemic.ambition.sandbox-zx4BEDTFKqEr0pAdjoU81w==/lib/arm64/libambition_app.so  libambition_app.so[+3622a8e4]
0.35%     Compute Task Po  /data/app/~~GNADuPUr7Wz9i9kvU1rJ9g==/org.erotemic.ambition.sandbox-zx4BEDTFKqEr0pAdjoU81w==/lib/arm64/libambition_app.so  libambition_app.so[+2d205b7c]
0.34%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.32%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.29%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!bb16f0bc38febaa4b4ae4f72493245!4783c89!
0.28%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!940f66b05f2a8911c494435445d321!4783c89!
0.28%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::allocate(unsigned long, s
0.27%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48892b8a4]
0.26%     android_main     /data/app/~~GNADuPUr7Wz9i9kvU1rJ9g==/org.erotemic.ambition.sandbox-zx4BEDTFKqEr0pAdjoU81w==/lib/arm64/libambition_app.so  libambition_app.so[+32ebf7b0]
0.26%     Compute Task Po  /data/app/~~GNADuPUr7Wz9i9kvU1rJ9g==/org.erotemic.ambition.sandbox-zx4BEDTFKqEr0pAdjoU81w==/lib/arm64/libambition_app.so  libambition_app.so[+36283c40]
0.26%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!260addb415166054346467f72a0679!4783c89!
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]  171.409s-176.418s frames=218 p50=21.5ms p95=37.0ms p99=48.3ms max=127.9ms archetypes=2465 (+0) entities=3020
[frame-census]  176.418s-181.435s frames=157 p50=24.9ms p95=55.2ms p99=177.1ms max=444.0ms archetypes=2466 (+1) entities=5368
[frame-census]  181.435s-186.458s frames=62 p50=39.0ms p95=400.7ms p99=410.3ms max=437.3ms archetypes=2466 (+0) entities=14602
[frame-census]  186.458s-191.473s frames=134 p50=37.7ms p95=60.0ms p99=70.6ms max=101.0ms archetypes=2466 (+0) entities=14697
[frame-census]  191.473s-196.504s frames=131 p50=36.4ms p95=64.8ms p99=85.6ms max=87.7ms archetypes=2466 (+0) entities=14723
[frame-census]  196.504s-201.574s frames=136 p50=32.2ms p95=54.4ms p99=145.0ms max=165.9ms archetypes=2466 (+0) entities=5352
[frame-census]  201.574s-206.600s frames=212 p50=21.6ms p95=35.5ms p99=58.5ms max=148.0ms archetypes=2466 (+0) entities=5348
[frame-census]  206.600s-211.603s frames=236 p50=21.3ms p95=32.0ms p99=36.4ms max=40.2ms archetypes=2466 (+0) entities=5348
[frame-census]  211.603s-216.612s frames=202 p50=23.7ms p95=40.2ms p99=45.7ms max=52.4ms archetypes=2466 (+0) entities=5402
[frame-census]  216.612s-221.634s frames=218 p50=22.0ms p95=36.9ms p99=61.1ms max=92.0ms archetypes=2466 (+0) entities=2354
[frame-census]  221.634s-226.641s frames=248 p50=18.8ms p95=32.6ms p99=41.7ms max=77.1ms archetypes=2466 (+0) entities=3075
[frame-census]  226.641s-231.648s frames=256 p50=18.8ms p95=30.1ms p99=41.3ms max=64.9ms archetypes=2466 (+0) entities=2793
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#             count  event_name         # count / runtime
  174794.444608(ms)  cpu-clock          #            
          1,438,260  context-switches   # 8.178 K/sec
            236,822  page-faults        # 1.347 K/sec
Total test time: 90.098537 seconds.
```

## gfxinfo excerpt
```text
Total frames rendered: 0
Janky frames: 0 (0.00%)
Janky frames (legacy): 0 (0.00%)
50th percentile: 4950ms
90th percentile: 4950ms
95th percentile: 4950ms
99th percentile: 4950ms
Number Missed Vsync: 0
HISTOGRAM: 5ms=0 6ms=0 7ms=0 8ms=0 9ms=0 10ms=0 11ms=0 12ms=0 13ms=0 14ms=0 15ms=0 16ms=0 17ms=0 18ms=0 19ms=0 20ms=0 21ms=0 22ms=0 23ms=0 24ms=0 25ms=0 26ms=0 27ms=0 28ms=0 29ms=0 30ms=0 31ms=0 32ms=0 34ms=0 36ms=0 38ms=0 40ms=0 42ms=0 44ms=0 46ms=0 48ms=0 53ms=0 57ms=0 61ms=0 65ms=0 69ms=0 73ms=0 77ms=0 81ms=0 85ms=0 89ms=0 93ms=0 97ms=0 101ms=0 105ms=0 109ms=0 113ms=0 117ms=0 121ms=0 125ms=0 129ms=0 133ms=0 150ms=0 200ms=0 250ms=0 300ms=0 350ms=0 400ms=0 450ms=0 500ms=0 550ms=0 600ms=0 650ms=0 700ms=0 750ms=0 800ms=0 850ms=0 900ms=0 950ms=0 1000ms=0 1050ms=0 1100ms=0 1150ms=0 1200ms=0 1250ms=0 1300ms=0 1350ms=0 1400ms=0 1450ms=0 1500ms=0 1550ms=0 1600ms=0 1650ms=0 1700ms=0 1750ms=0 1800ms=0 1850ms=0 1900ms=0 1950ms=0 2000ms=0 2050ms=0 2100ms=0 2150ms=0 2200ms=0 2250ms=0 2300ms=0 2350ms=0 2400ms=0 2450ms=0 2500ms=0 2550ms=0 2600ms=0 2650ms=0 2700ms=0 2750ms=0 2800ms=0 2850ms=0 2900ms=0 2950ms=0 3000ms=0 3050ms=0 3100ms=0 3150ms=0 3200ms=0 3250ms=0 3300ms=0 3350ms=0 3400ms=0 3450ms=0 3500ms=0 3550ms=0 3600ms=0 3650ms=0 3700ms=0 3750ms=0 3800ms=0 3850ms=0 3900ms=0 3950ms=0 4000ms=0 4050ms=0 4100ms=0 4150ms=0 4200ms=0 4250ms=0 4300ms=0 4350ms=0 4400ms=0 4450ms=0 4500ms=0 4550ms=0 4600ms=0 4650ms=0 4700ms=0 4750ms=0 4800ms=0 4850ms=0 4900ms=0 4950ms=0
GPU HISTOGRAM: 1ms=0 2ms=0 3ms=0 4ms=0 5ms=0 6ms=0 7ms=0 8ms=0 9ms=0 10ms=0 11ms=0 12ms=0 13ms=0 14ms=0 15ms=0 16ms=0 17ms=0 18ms=0 19ms=0 20ms=0 21ms=0 22ms=0 23ms=0 24ms=0 25ms=0 4950ms=0
```

## Symbol candidates
```text
# libambition_app.so candidates
/home/joncrall/code/ambition/target/aarch64-linux-android/android-profile/deps/libambition_app.so
/home/joncrall/code/ambition/target/aarch64-linux-android/android-profile/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/merged_jni_libs/debug/mergeDebugJniLibFolders/out/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/merged_native_libs/debug/mergeDebugNativeLibs/out/lib/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/stripped_native_libs/debug/stripDebugDebugSymbols/out/lib/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/src/main/jniLibs/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/release/deps/libambition_app.so
```
