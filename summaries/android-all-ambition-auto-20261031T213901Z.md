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
- perf.data bytes: 3118557

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 5705. Samples lost: 0.

## Top simpleperf report lines
```text
4.42%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.79%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
1.14%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
1.12%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
1.12%     android_main     /data/app/~~TPbsMRjaLiNb5PotCR8s6g==/org.erotemic.ambition.sandbox-BAgz1Yp8PmMk9bMZRfr84g==/lib/arm64/libambition_app.so  libambition_app.so[+2cfdca08]
0.98%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
0.79%     Async Compute T  /apex/com.android.runtime/lib64/bionic/libm.so                                                                            atan2f
0.74%     android_main     /data/app/~~TPbsMRjaLiNb5PotCR8s6g==/org.erotemic.ambition.sandbox-BAgz1Yp8PmMk9bMZRfr84g==/lib/arm64/libambition_app.so  libambition_app.so[+32cbf214]
0.44%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.40%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.40%     android_main     /data/app/~~TPbsMRjaLiNb5PotCR8s6g==/org.erotemic.ambition.sandbox-BAgz1Yp8PmMk9bMZRfr84g==/lib/arm64/libambition_app.so  libambition_app.so[+36029e78]
0.39%     Compute Task Po  /data/app/~~TPbsMRjaLiNb5PotCR8s6g==/org.erotemic.ambition.sandbox-BAgz1Yp8PmMk9bMZRfr84g==/lib/arm64/libambition_app.so  libambition_app.so[+2cfdca08]
0.30%     Async Compute T  /data/app/~~TPbsMRjaLiNb5PotCR8s6g==/org.erotemic.ambition.sandbox-BAgz1Yp8PmMk9bMZRfr84g==/lib/arm64/libambition_app.so  libambition_app.so[+32fea748]
0.30%     android_main     /data/app/~~TPbsMRjaLiNb5PotCR8s6g==/org.erotemic.ambition.sandbox-BAgz1Yp8PmMk9bMZRfr84g==/lib/arm64/libambition_app.so  libambition_app.so[+32e70760]
0.28%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::deallocate(void*, scudo::
0.28%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!940f66b05f2a8911c494435445d321!4783c89!
0.28%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!bb16f0bc38febaa4b4ae4f72493245!4783c89!
0.26%     Compute Task Po  /data/app/~~TPbsMRjaLiNb5PotCR8s6g==/org.erotemic.ambition.sandbox-BAgz1Yp8PmMk9bMZRfr84g==/lib/arm64/libambition_app.so  libambition_app.so[+360831d4]
0.26%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            pthread_getspecific
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   31.809s-36.833s frames=135 p50=36.1ms p95=61.7ms p99=77.6ms max=92.9ms archetypes=2228 (+0) entities=14720
[frame-census]   36.833s-41.846s frames=140 p50=33.3ms p95=60.4ms p99=81.1ms max=95.2ms archetypes=2228 (+0) entities=14720
[frame-census]   41.846s-46.858s frames=176 p50=28.0ms p95=54.8ms p99=70.1ms max=117.4ms archetypes=2241 (+13) entities=5346
[frame-census]   46.858s-51.870s frames=235 p50=21.1ms p95=33.4ms p99=37.9ms max=45.8ms archetypes=2241 (+0) entities=5346
[frame-census]   51.870s-56.883s frames=228 p50=21.9ms p95=34.7ms p99=41.3ms max=43.3ms archetypes=2241 (+0) entities=5346
[frame-census]   56.883s-61.901s frames=190 p50=24.8ms p95=41.7ms p99=59.1ms max=76.5ms archetypes=2250 (+9) entities=5411
[frame-census]   61.901s-66.924s frames=197 p50=22.4ms p95=55.8ms p99=74.0ms max=90.5ms archetypes=2277 (+27) entities=2583
[frame-census]   66.924s-71.946s frames=242 p50=19.4ms p95=33.5ms p99=46.0ms max=52.8ms archetypes=2308 (+31) entities=2596
[frame-census]   71.946s-76.961s frames=250 p50=19.9ms p95=29.8ms p99=35.1ms max=55.4ms archetypes=2309 (+1) entities=2576
[frame-census]   76.961s-81.966s frames=252 p50=19.1ms p95=28.9ms p99=32.8ms max=88.1ms archetypes=2329 (+20) entities=3078
[frame-census]   81.966s-86.976s frames=262 p50=18.7ms p95=28.1ms p99=32.0ms max=37.2ms archetypes=2353 (+24) entities=3100
[frame-census]   86.976s-91.979s frames=259 p50=19.2ms p95=29.4ms p99=34.9ms max=55.3ms archetypes=2353 (+0) entities=3100
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  59893.612923(ms)  cpu-clock          #             
           603,702  context-switches   # 10.004 K/sec
            79,050  page-faults        # 1.310 K/sec 
Total test time: 30.002923 seconds.
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
