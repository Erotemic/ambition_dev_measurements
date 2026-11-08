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
- perf.data bytes: 6945313

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 17243. Samples lost: 0.

## Top simpleperf report lines
```text
4.40%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.08%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.06%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.96%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.90%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.84%     android_main     /data/app/~~LusbaFUFnFrPDoUuUtEEgA==/org.erotemic.ambition.sandbox-5ui91bSBaIXRiUKBW8EiaQ==/lib/arm64/libambition_app.so  libambition_app.so[+2d20883c]
0.61%     Async Compute T  /apex/com.android.runtime/lib64/bionic/libm.so                                                                            atan2f
0.47%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.45%     Compute Task Po  /data/app/~~LusbaFUFnFrPDoUuUtEEgA==/org.erotemic.ambition.sandbox-5ui91bSBaIXRiUKBW8EiaQ==/lib/arm64/libambition_app.so  libambition_app.so[+2d20883c]
0.34%     android_main     /data/app/~~LusbaFUFnFrPDoUuUtEEgA==/org.erotemic.ambition.sandbox-5ui91bSBaIXRiUKBW8EiaQ==/lib/arm64/libambition_app.so  libambition_app.so[+36234bf4]
0.28%     android_main     /data/app/~~LusbaFUFnFrPDoUuUtEEgA==/org.erotemic.ambition.sandbox-5ui91bSBaIXRiUKBW8EiaQ==/lib/arm64/libambition_app.so  libambition_app.so[+32ec954c]
0.27%     Compute Task Po  /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.26%     Compute Task Po  /data/app/~~LusbaFUFnFrPDoUuUtEEgA==/org.erotemic.ambition.sandbox-5ui91bSBaIXRiUKBW8EiaQ==/lib/arm64/libambition_app.so  libambition_app.so[+3628df50]
0.26%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48892b8a4]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]  169.229s-174.244s frames=245 p50=19.8ms p95=33.1ms p99=42.5ms max=70.8ms archetypes=2460 (+0) entities=2995
[frame-census]  174.244s-179.278s frames=195 p50=20.2ms p95=39.4ms p99=135.6ms max=449.4ms archetypes=2460 (+0) entities=5356
[frame-census]  179.278s-184.455s frames=104 p50=28.3ms p95=305.4ms p99=390.0ms max=511.8ms archetypes=2460 (+0) entities=14592
[frame-census]  184.455s-189.488s frames=135 p50=31.9ms p95=59.8ms p99=133.7ms max=137.9ms archetypes=2460 (+0) entities=14658
[frame-census]  189.488s-194.494s frames=144 p50=31.6ms p95=58.6ms p99=71.2ms max=85.7ms archetypes=2460 (+0) entities=14722
[frame-census]  194.494s-199.502s frames=140 p50=34.8ms p95=58.5ms p99=67.1ms max=84.3ms archetypes=2460 (+0) entities=14722
[frame-census]  199.502s-204.503s frames=167 p50=26.6ms p95=58.0ms p99=122.3ms max=150.3ms archetypes=2460 (+0) entities=5348
[frame-census]  204.503s-209.511s frames=226 p50=21.7ms p95=34.4ms p99=37.2ms max=46.4ms archetypes=2460 (+0) entities=5364
[frame-census]  209.511s-214.527s frames=195 p50=24.8ms p95=41.6ms p99=47.2ms max=77.4ms archetypes=2460 (+0) entities=2601
[frame-census]  214.527s-219.533s frames=234 p50=20.5ms p95=34.2ms p99=48.2ms max=72.6ms archetypes=2460 (+0) entities=2991
[frame-census]  219.533s-224.534s frames=249 p50=19.2ms p95=31.7ms p99=45.6ms max=87.1ms archetypes=2460 (+0) entities=3085
[frame-census]  224.534s-229.536s frames=244 p50=19.9ms p95=33.1ms p99=35.7ms max=67.3ms archetypes=2460 (+0) entities=2828
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#             count  event_name         # count / runtime
  175736.946400(ms)  cpu-clock          #            
          1,468,744  context-switches   # 8.304 K/sec
            239,220  page-faults        # 1.352 K/sec
Total test time: 90.023037 seconds.
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
