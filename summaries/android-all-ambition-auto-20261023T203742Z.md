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
- perf.data bytes: 2819078

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 4929. Samples lost: 0.

## Top simpleperf report lines
```text
5.34%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.24%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
1.10%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
0.95%     android_main     /data/app/~~k8XX7mLlfrYus6RgNRXeTw==/org.erotemic.ambition.sandbox-kBhwjBFMER96gnmt7VuL9A==/lib/arm64/libambition_app.so  libambition_app.so[+b5de9a8]
0.77%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.69%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.57%     Compute Task Po  /data/app/~~k8XX7mLlfrYus6RgNRXeTw==/org.erotemic.ambition.sandbox-kBhwjBFMER96gnmt7VuL9A==/lib/arm64/libambition_app.so  libambition_app.so[+b5de9a8]
0.51%     android_main     /data/app/~~k8XX7mLlfrYus6RgNRXeTw==/org.erotemic.ambition.sandbox-kBhwjBFMER96gnmt7VuL9A==/lib/arm64/libambition_app.so  libambition_app.so[+13ffd7a0]
0.43%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.37%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4878eb050]
0.37%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796e324]
0.28%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            scudo::Allocator<scudo::AndroidConfig, &(scudo_malloc_postinit)>::allocate(unsigned long, s
0.28%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa4879435f0]
0.26%     Compute Task Po  /data/app/~~k8XX7mLlfrYus6RgNRXeTw==/org.erotemic.ambition.sandbox-kBhwjBFMER96gnmt7VuL9A==/lib/arm64/libambition_app.so  libambition_app.so[+14081598]
0.26%     android_main     /data/app/~~k8XX7mLlfrYus6RgNRXeTw==/org.erotemic.ambition.sandbox-kBhwjBFMER96gnmt7VuL9A==/lib/arm64/libambition_app.so  libambition_app.so[+10dcbf0c]
0.26%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!097ece140a689b3ba2b5661cb74dce!4783c89!
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]   35.188s-40.214s frames=144 p50=33.0ms p95=60.4ms p99=77.8ms max=83.5ms archetypes=2182 (+9) entities=4689
[frame-census]   40.214s-45.231s frames=114 p50=45.0ms p95=61.7ms p99=66.0ms max=66.7ms archetypes=2182 (+0) entities=4691
[frame-census]   45.231s-50.262s frames=115 p50=44.9ms p95=61.8ms p99=63.5ms max=63.7ms archetypes=2182 (+0) entities=4699
[frame-census]   50.262s-55.272s frames=203 p50=21.2ms p95=53.1ms p99=74.7ms max=93.1ms archetypes=2204 (+22) entities=2947
[frame-census]   55.272s-60.279s frames=260 p50=19.0ms p95=29.9ms p99=34.8ms max=57.0ms archetypes=2233 (+29) entities=2897
[frame-census]   60.279s-65.293s frames=232 p50=20.8ms p95=35.8ms p99=39.4ms max=53.8ms archetypes=2248 (+15) entities=3027
[frame-census]   65.293s-70.296s frames=270 p50=18.2ms p95=28.2ms p99=32.8ms max=42.6ms archetypes=2248 (+0) entities=3039
[frame-census]   70.296s-75.335s frames=250 p50=19.8ms p95=34.0ms p99=44.9ms max=63.6ms archetypes=2314 (+66) entities=2391
[frame-census]   75.335s-80.357s frames=274 p50=17.5ms p95=28.7ms p99=35.7ms max=38.4ms archetypes=2319 (+5) entities=2402
[frame-census]   80.357s-85.370s frames=261 p50=18.6ms p95=30.2ms p99=31.9ms max=54.4ms archetypes=2323 (+4) entities=2788
[frame-census]   85.370s-90.371s frames=265 p50=18.1ms p95=31.9ms p99=35.5ms max=60.2ms archetypes=2323 (+0) entities=3047
[frame-census]   90.371s-95.389s frames=267 p50=18.7ms p95=29.1ms p99=33.7ms max=55.6ms archetypes=2324 (+1) entities=2908
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#            count  event_name         # count / runtime
  59602.235872(ms)  cpu-clock          #             
           618,343  context-switches   # 10.298 K/sec
            70,655  page-faults        # 1.177 K/sec 
Total test time: 30.000602 seconds.
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
