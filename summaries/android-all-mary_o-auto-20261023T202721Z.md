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
- perf.data bytes: 3408208

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 7137. Samples lost: 0.

## Top simpleperf report lines
```text
5.30%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.92%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10fc3ef0]
1.75%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10fc3ee8]
1.58%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10fc3fe4]
1.37%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10f766ec]
1.33%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.04%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10f767cc]
0.90%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.83%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10fb753c]
0.70%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10fb7904]
0.67%     android_main     /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+b5de9a8]
0.63%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.59%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10fb75cc]
0.49%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.45%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.39%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10fb7964]
0.36%     Async Compute T  /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+10fb79ac]
0.34%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.29%     android_main     /data/app/~~yCPSauZ4Ckk13peX0mlnFg==/org.erotemic.ambition.sandbox-uz5aWgm1vl_ylRf9XPlNLQ==/lib/arm64/libambition_app.so  libambition_app.so[+13ffd7a0]
0.27%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4878eb050]
0.25%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   35.097s-40.119s frames=211 p50=22.5ms p95=40.9ms p99=49.8ms max=51.2ms archetypes=2085 (+2) entities=3800
[frame-census]   40.119s-45.130s frames=205 p50=22.4ms p95=50.2ms p99=57.6ms max=68.9ms archetypes=2085 (+0) entities=3760
[frame-census]   45.130s-50.133s frames=241 p50=19.9ms p95=33.6ms p99=36.4ms max=48.3ms archetypes=2088 (+3) entities=3771
[frame-census]   50.133s-55.159s frames=263 p50=18.6ms p95=30.2ms p99=36.1ms max=41.1ms archetypes=2089 (+1) entities=3788
[frame-census]   55.159s-60.161s frames=217 p50=21.2ms p95=39.2ms p99=43.7ms max=45.2ms archetypes=2089 (+0) entities=3778
[frame-census]   60.161s-65.185s frames=236 p50=21.2ms p95=31.4ms p99=41.0ms max=41.6ms archetypes=2089 (+0) entities=3792
[frame-census]   65.185s-70.186s frames=248 p50=20.3ms p95=30.8ms p99=37.5ms max=43.8ms archetypes=2089 (+0) entities=3808
[frame-census]   70.186s-75.191s frames=248 p50=19.9ms p95=28.1ms p99=43.8ms max=78.8ms archetypes=2105 (+16) entities=2593
[frame-census]   75.191s-80.197s frames=277 p50=17.8ms p95=27.5ms p99=31.8ms max=33.2ms archetypes=2105 (+0) entities=2669
[frame-census]   80.197s-85.199s frames=276 p50=17.8ms p95=26.1ms p99=33.7ms max=37.6ms archetypes=2128 (+23) entities=2674
[frame-census]   85.199s-90.209s frames=276 p50=17.8ms p95=27.2ms p99=31.8ms max=33.3ms archetypes=2129 (+1) entities=2695
[frame-census]   90.209s-95.211s frames=270 p50=18.1ms p95=27.9ms p99=32.1ms max=37.8ms archetypes=2130 (+1) entities=2695
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  63028.884101(ms)  cpu-clock          #             
           645,023  context-switches   # 10.155 K/sec
            56,075  page-faults        # 882.902 /sec
Total test time: 30.006760 seconds.
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
/home/joncrall/code/ambition/target/aarch64-linux-android/release/deps/libambition_app.so
/home/joncrall/code/ambition/target/aarch64-linux-android/release/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/merged_jni_libs/debug/mergeDebugJniLibFolders/out/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/merged_native_libs/debug/mergeDebugNativeLibs/out/lib/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/build/intermediates/stripped_native_libs/debug/stripDebugDebugSymbols/out/lib/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/android/ambition_platformer2d_actor_monolith_android/app/src/main/jniLibs/arm64-v8a/libambition_app.so
/home/joncrall/code/ambition/target/release/deps/libambition_app.so
```
