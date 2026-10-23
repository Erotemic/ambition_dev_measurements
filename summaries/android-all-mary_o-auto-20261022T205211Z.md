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
- perf.data bytes: 3415512

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 7801. Samples lost: 0.

## Top simpleperf report lines
```text
3.23%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.59%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27148]
1.51%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1abe8]
1.17%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1ac0c]
0.95%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1a82c]
0.94%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27150]
0.87%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
0.77%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1ab64]
0.71%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10cd9a20]
0.69%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10cd994c]
0.68%     Compute Task Po  /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.67%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27244]
0.65%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.59%     Async Compute T  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27150]
0.58%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10cd9a2c]
0.56%     Async Compute T  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10cd994c]
0.55%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27158]
0.50%     Async Compute T  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27244]
0.46%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.46%     android_main     /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1ac0c]
0.45%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1abf0]
0.41%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10cd9a14]
0.41%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+b394d94]
0.40%     Async Compute T  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27148]
0.40%     android_main     /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27148]
0.38%     Async Compute T  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10cd9a2c]
0.38%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1ac10]
0.35%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10cd99f0]
0.35%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27140]
0.35%     android_main     /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+b394d94]
0.33%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10cd9a44]
0.33%     android_main     /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1abe8]
0.32%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.31%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1a7a0]
0.31%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1abc4]
0.28%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1ab5c]
0.28%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1ac18]
0.28%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27144]
0.28%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d27248]
0.26%     Async Compute T  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1a82c]
0.26%     Compute Task Po  /data/app/~~rREwl72adhcBH5JA7V-4TA==/org.erotemic.ambition.sandbox-05x8hfjUT4VvN0AtEHfcyw==/lib/arm64/libambition_app.so  libambition_app.so[+10d1a79c]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   35.059s-40.082s frames=140 p50=25.8ms p95=39.6ms p99=160.6ms max=1171.9ms archetypes=2074 (+1) entities=3771
[frame-census]   40.082s-45.263s frames=109 p50=23.8ms p95=41.1ms p99=1218.1ms max=1300.5ms archetypes=2074 (+0) entities=3777
[frame-census]   45.263s-50.275s frames=150 p50=22.8ms p95=49.5ms p99=138.0ms max=1277.6ms archetypes=2074 (+0) entities=3762
[frame-census]   50.275s-55.278s frames=231 p50=21.6ms p95=33.2ms p99=38.3ms max=44.2ms archetypes=2075 (+1) entities=3797
[frame-census]   55.278s-60.336s frames=203 p50=24.2ms p95=34.5ms p99=40.2ms max=101.1ms archetypes=2075 (+0) entities=3881
[frame-census]   60.336s-65.347s frames=211 p50=23.6ms p95=35.2ms p99=40.6ms max=55.7ms archetypes=2075 (+0) entities=3832
[frame-census]   65.347s-70.362s frames=212 p50=22.3ms p95=37.5ms p99=53.3ms max=58.0ms archetypes=2075 (+0) entities=3832
[frame-census]   70.362s-75.381s frames=173 p50=22.4ms p95=35.9ms p99=58.4ms max=887.8ms archetypes=2089 (+14) entities=2686
[frame-census]   75.381s-80.403s frames=222 p50=22.0ms p95=34.0ms p99=39.4ms max=41.3ms archetypes=2089 (+0) entities=2692
[frame-census]   80.403s-85.404s frames=233 p50=21.1ms p95=32.8ms p99=38.1ms max=38.5ms archetypes=2089 (+0) entities=2697
[frame-census]   85.404s-90.415s frames=226 p50=21.7ms p95=31.2ms p99=38.6ms max=49.3ms archetypes=2089 (+0) entities=2749
[frame-census]   90.415s-95.420s frames=216 p50=23.2ms p95=33.0ms p99=36.3ms max=38.1ms archetypes=2090 (+1) entities=2732
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  58777.089459(ms)  cpu-clock          #             
           530,072  context-switches   # 8.955 K/sec 
            45,451  page-faults        # 767.888 /sec
Total test time: 30.000793 seconds.
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
