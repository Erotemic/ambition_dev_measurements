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
- perf.data bytes: 5076704

## Interesting record stderr
- simpleperf I cmd_record.cpp:891] Samples recorded: 12987. Samples lost: 0.

## Top simpleperf report lines
```text
5.01%     Compute Task Po  [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
3.54%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d9b5b0]
2.52%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d9b4e0]
2.09%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d95dd0]
2.00%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d9b54c]
1.48%     android_main     [kernel.kallsyms]                                                                                                         [kernel.kallsyms][+ffffffa48796c2e4]
1.32%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d95bd4]
1.09%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d95ae0]
1.06%     android_main     /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+c79bc8c]
0.85%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memmove_aarch64_simd
0.48%     android_main     /vendor/lib64/hw/vulkan.adreno.so                                                                                         !!!0000!d96d9ec5e29c21949f3f32f251c2b5!4783c89!
0.39%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d95ad8]
0.39%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            __memcmp_aarch64
0.35%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d80fdc]
0.35%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d58168]
0.35%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d95f68]
0.33%     android_main     /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+101c4994]
0.32%     android_main     /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+14dce92c]
0.30%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d95c30]
0.29%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d95ad0]
0.28%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d581b4]
0.28%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d871d0]
0.28%     Async Compute T  /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+11d9b4ec]
0.28%     android_main     /apex/com.android.runtime/lib64/bionic/libc.so                                                                            pthread_getspecific
0.27%     android_main     /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+14dce928]
0.25%     android_main     /data/app/~~mFHN25FEZKRVzHrJfu_PAQ==/org.erotemic.ambition.sandbox-KpYfEanR5QdC2jJBr4Ed_w==/lib/arm64/libambition_app.so  libambition_app.so[+14dce924]
```

## Frame times (`[frame-census]`, the last 12 windows of 5 s)
```text
[frame-census]  130.406s-135.424s frames=198 p50=25.0ms p95=37.1ms p99=42.4ms max=43.6ms archetypes=2100 (+0) entities=2682
[frame-census]  135.424s-140.448s frames=176 p50=28.2ms p95=43.3ms p99=47.8ms max=52.9ms archetypes=2100 (+0) entities=2684
[frame-census]  140.448s-145.477s frames=170 p50=28.8ms p95=44.4ms p99=50.0ms max=65.7ms archetypes=2100 (+0) entities=2682
[frame-census]  145.477s-150.513s frames=176 p50=28.6ms p95=42.6ms p99=51.8ms max=57.0ms archetypes=2100 (+0) entities=2682
[frame-census]  150.513s-155.528s frames=163 p50=30.7ms p95=45.7ms p99=49.6ms max=56.5ms archetypes=2100 (+0) entities=2682
[frame-census]  155.528s-160.545s frames=157 p50=31.0ms p95=50.0ms p99=52.3ms max=59.6ms archetypes=2100 (+0) entities=2682
[frame-census]  160.545s-165.548s frames=174 p50=28.5ms p95=40.9ms p99=46.4ms max=55.8ms archetypes=2100 (+0) entities=2684
[frame-census]  165.548s-170.552s frames=160 p50=31.7ms p95=43.3ms p99=50.3ms max=55.3ms archetypes=2100 (+0) entities=2682
[frame-census]  170.552s-175.553s frames=177 p50=28.9ms p95=40.7ms p99=43.9ms max=46.0ms archetypes=2100 (+0) entities=2684
[frame-census]  175.553s-180.554s frames=163 p50=30.1ms p95=47.4ms p99=52.4ms max=55.5ms archetypes=2100 (+0) entities=2682
[frame-census]  180.554s-185.556s frames=171 p50=27.6ms p95=44.7ms p99=50.8ms max=105.4ms archetypes=2100 (+0) entities=2684
[frame-census]  185.556s-190.558s frames=179 p50=27.5ms p95=39.4ms p99=46.6ms max=53.9ms archetypes=2100 (+0) entities=2682
```

- hub games: all

## simpleperf stat excerpt
```text
Performance counter statistics:
#             count  event_name         # count / runtime
  101388.823495(ms)  cpu-clock          #             
            964,340  context-switches   # 9.438 K/sec 
             63,135  page-faults        # 617.914 /sec
Total test time: 60.006905 seconds.
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
