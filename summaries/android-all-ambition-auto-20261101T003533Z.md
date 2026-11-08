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
- perf.data bytes: 8252214

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 20792. Samples lost: 0.

## Top simpleperf report lines
```text
4.45%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.23%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
1.10%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.08%     android_main     /data/app/~~tk9tCRM_B4PkCe2vfxyuMA==/org.erotemic.ambition.sandbox-3GPH6fg9yzK1SNb9RyNqcA==/lib/arm64/libambition_app.so  libambition_app.so[+2d205b3c]
1.08%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.88%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.82%     Async Compute T  /apex/com.android.runtime/lib64/bionic/libm.so                                                                            atan2f
0.52%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa487895bc8]
0.39%     android_main     /data/app/~~tk9tCRM_B4PkCe2vfxyuMA==/org.erotemic.ambition.sandbox-3GPH6fg9yzK1SNb9RyNqcA==/lib/arm64/libambition_app.so  libambition_app.so[+3622a664]
0.38%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            memset
0.37%     Compute Task Po  /data/app/~~tk9tCRM_B4PkCe2vfxyuMA==/org.erotemic.ambition.sandbox-3GPH6fg9yzK1SNb9RyNqcA==/lib/arm64/libambition_app.so  libambition_app.so[+2d205b3c]
0.31%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48892b8a4]
0.29%     Compute Task Po  /data/app/~~tk9tCRM_B4PkCe2vfxyuMA==/org.erotemic.ambition.sandbox-3GPH6fg9yzK1SNb9RyNqcA==/lib/arm64/libambition_app.so  libambition_app.so[+362839c0]
0.27%     android_main     /data/app/~~tk9tCRM_B4PkCe2vfxyuMA==/org.erotemic.ambition.sandbox-3GPH6fg9yzK1SNb9RyNqcA==/lib/arm64/libambition_app.so  libambition_app.so[+3622a508]
0.26%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.25%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]  272.291s-277.327s frames=146 p50=25.0ms p95=65.0ms p99=199.4ms max=563.1ms archetypes=2465 (+0) entities=5363
[frame-census]  277.327s-282.397s frames=60 p50=32.4ms p95=458.5ms p99=476.8ms max=547.9ms archetypes=2465 (+0) entities=14597
[frame-census]  282.397s-287.399s frames=102 p50=43.4ms p95=88.7ms p99=125.1ms max=142.4ms archetypes=2465 (+0) entities=14682
[frame-census]  287.399s-292.410s frames=119 p50=41.6ms p95=64.5ms p99=73.7ms max=74.9ms archetypes=2465 (+0) entities=14722
[frame-census]  292.410s-297.416s frames=116 p50=41.6ms p95=72.8ms p99=77.3ms max=89.7ms archetypes=2465 (+0) entities=14722
[frame-census]  297.416s-302.419s frames=175 p50=23.0ms p95=60.8ms p99=151.6ms max=183.2ms archetypes=2465 (+0) entities=5348
[frame-census]  302.419s-307.436s frames=231 p50=21.2ms p95=32.1ms p99=35.9ms max=55.3ms archetypes=2465 (+0) entities=5348
[frame-census]  307.436s-312.447s frames=210 p50=23.1ms p95=39.1ms p99=46.3ms max=49.5ms archetypes=2465 (+0) entities=5402
[frame-census]  312.447s-317.452s frames=211 p50=22.3ms p95=40.6ms p99=46.6ms max=88.9ms archetypes=2465 (+0) entities=2591
[frame-census]  317.452s-322.462s frames=240 p50=19.7ms p95=31.6ms p99=52.9ms max=74.2ms archetypes=2465 (+0) entities=3094
[frame-census]  322.462s-327.475s frames=249 p50=18.9ms p95=31.9ms p99=49.5ms max=64.0ms archetypes=2465 (+0) entities=2812
[frame-census]  327.475s-332.492s frames=244 p50=19.7ms p95=32.7ms p99=42.6ms max=71.9ms archetypes=2465 (+0) entities=2426
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#             count  event_name         # count / runtime
  228447.783126(ms)  cpu-clock          #            
          1,844,062  context-switches   # 8.021 K/sec
            292,139  page-faults        # 1.271 K/sec
Total test time: 120.048363 seconds.
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
