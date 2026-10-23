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
- perf.data bytes: 3276862

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 6809. Samples lost: 0.

## Top simpleperf report lines
```text
4.95%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
2.06%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d34f98]
1.84%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d3508c]
1.32%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d34f90]
1.20%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.15%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10ce7794]
1.01%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10ce7874]
0.93%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d285e4]
0.81%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.75%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.57%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d28674]
0.54%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d289ac]
0.51%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.48%     android_main     /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+b3983d4]
0.46%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d28a0c]
0.35%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d28a54]
0.31%     android_main     /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+13d6e2fc]
0.28%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d28604]
0.28%     android_main     /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+13d6e300]
0.26%     Async Compute T  /data/app/~~vwgIL09sGdsXfBMeNmWC0Q==/org.erotemic.ambition.sandbox-uEfrNwcmyoZ0IPQHfc1MdQ==/lib/arm64/libambition_app.so  libambition_app.so[+10d28a50]
0.26%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c370]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   35.075s-40.081s frames=181 p50=24.0ms p95=58.2ms p99=67.8ms max=91.9ms archetypes=2075 (+1) entities=3791
[frame-census]   40.081s-45.093s frames=204 p50=23.5ms p95=38.6ms p99=43.9ms max=44.5ms archetypes=2075 (+0) entities=3848
[frame-census]   45.093s-50.110s frames=199 p50=25.1ms p95=38.9ms p99=48.4ms max=55.7ms archetypes=2076 (+1) entities=3797
[frame-census]   50.110s-55.137s frames=177 p50=26.2ms p95=48.5ms p99=53.0ms max=65.0ms archetypes=2076 (+0) entities=3801
[frame-census]   55.137s-60.157s frames=160 p50=33.6ms p95=44.2ms p99=49.7ms max=53.9ms archetypes=2078 (+2) entities=3795
[frame-census]   60.157s-65.179s frames=204 p50=23.7ms p95=37.6ms p99=48.8ms max=80.5ms archetypes=2083 (+5) entities=3774
[frame-census]   65.179s-70.191s frames=227 p50=21.1ms p95=32.8ms p99=40.6ms max=45.2ms archetypes=2083 (+0) entities=3797
[frame-census]   70.191s-75.199s frames=218 p50=23.0ms p95=34.6ms p99=40.9ms max=52.8ms archetypes=2083 (+0) entities=3791
[frame-census]   75.199s-80.217s frames=203 p50=24.8ms p95=35.6ms p99=38.4ms max=41.8ms archetypes=2083 (+0) entities=3817
[frame-census]   80.217s-85.218s frames=213 p50=23.8ms p95=33.8ms p99=37.8ms max=43.4ms archetypes=2083 (+0) entities=3802
[frame-census]   85.218s-90.240s frames=239 p50=20.2ms p95=33.2ms p99=40.6ms max=42.1ms archetypes=2083 (+0) entities=3822
[frame-census]   90.240s-95.245s frames=218 p50=23.6ms p95=33.7ms p99=38.3ms max=45.6ms archetypes=2083 (+0) entities=3822
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  57119.910603(ms)  cpu-clock          #             
           512,519  context-switches   # 8.910 K/sec 
            44,335  page-faults        # 770.670 /sec
Total test time: 30.028554 seconds.
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
