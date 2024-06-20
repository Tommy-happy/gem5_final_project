## Q1 - GEM5 + NVMAIN BUILD-UP (40%)

## Q2 - Enable L3 last level cache in GEM5 + NVMAIN (15%) 
- 在 gem5/config/common/Caches.py 新增L3Cache
    - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/d182f0fb-f8ac-4226-811a-52563bfad8e9)

- 在 gem5/config/common/CacheConfig.py 
    - 第三個 if 中加上 l3_cache
        - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/5f133887-7b8a-4eb7-982c-4cdca59b17d8)

    - 新增一個 if 判斷式，判斷如果 option 中啟用 L3 cache 的狀況
        - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/f75b6a14-a2c1-4742-b1f9-98cb9d492a8e)

- 在 gem5/config/common/Options.py 中新增一條 option 
    - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/2b5a5214-db55-41d7-b2d2-0426c92a4818)

- 在 gem5/src/mem/XBar.py 中新增L3XBar這個class (從L2XBar複製過來)
    - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/9819ed37-118d-4d8d-a9d9-078286ba35a2)

- 在 gem5/src/cpu/BaseCPU.py 中
    - 引入 l3 cache
        - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/2574e795-e092-4dd6-9e8e-fdbcc456b1e7)

    - 新增一個函數 (把l2的函數二都改成三即可)
    - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/71d200a2-cd10-49f0-839a-06a784c428fa)

- 重新編譯
    ```!
    scons EXTRAS=../NVmain build/X86/gem5.opt
    ```
- 重新編譯後執行時 option 加上 --l3cache 即可調用 l3 cache
    - command:
    ```!
    ./build/X86/gem5.opt -d ../q2_out configs/example/se.py -c ./tests/test-progs/hello/bin/x86/linux/benchmark/quicksort --cpu-type=TimingSimpleCPU --caches --l2cache --l3cache --mem-type=NVMainMemory --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config > ../q2_out/log
    ```


## Q3 - Config last level cache to 2-way and full-way associative cache and test performance (15%)

### 2-way
- command
    ```!
    ./build/X86/gem5.opt -d ../q3_out/2_way/ configs/example/se.py -c ./tests/test-progs/hello/bin/x86/linux/benchmark/quicksort --cpu-type=TimingSimpleCPU --caches --l2cache --l3cache --l3_assoc=2 --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=512kB --mem-type=NVMainMemory --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config > ../q3_out/2_way/log
    ```
    - 適當調整大小讓l3 cache 的 miss rate 有變化
- miss rate
    - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/8207c2f7-66c9-4934-8dae-fa61562ef530)

### full-way command
- command
    ```!
    ./build/X86/gem5.opt -d ../q3_out/full_way/ configs/example/se.py -c tests/test-progs/hello/bin/x86/linux/benchmark/quicksort --cpu-type=TimingSimpleCPU --caches --l2cache --l3cache --l3_assoc=1 --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=512kB --mem-type=NVMainMemory --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config > ../q3_out/full_way/log
    ```
    - 適當調整大小讓l3 cache 的 miss rate 有變化
- miss rate
    - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/13322908-cdf6-48e9-984b-895b50e14cc8)

## Q4 - Modify last level cache policy based on frequency based replacement policy (15%)

- 這裡用2-way asscoiative
- 在 gem5/config/common/Caches.py
    - 新增一項規則：replacement_policy = Param.BaseReplacementPolicy(LFURP(),"Replacement policy")
    - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/d0989960-61bb-4b66-a110-2a4d5298bad4)

- 原本的replacement policy：![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/bba409a7-442d-43bf-9ace-6056021396d2)

- 後來的replacement policy：![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/7aace83f-3521-4f34-8004-4d1759de23b9)

- command-original policy
    ```!
    ./build/X86/gem5.opt -d ../q4_out/lru/ configs/example/se.py -c ./tests/test-progs/hello/bin/x86/linux/benchmark/quicksort --cpu-type=TimingSimpleCPU --caches --l2cache --l3cache --l3_assoc=2 --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=512kB --mem-type=NVMainMemory --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config > ../q4_out/lru/log
    ```

- command-frequency based policy
    ```!
    ./build/X86/gem5.opt -d ../q4_out/lfu/ configs/example/se.py -c ./tests/test-progs/hello/bin/x86/linux/benchmark/quicksort --cpu-type=TimingSimpleCPU --caches --l2cache --l3cache --l3_assoc=2 --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=512kB --mem-type=NVMainMemory --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config > ../q4_out/lfu/log
    ```

## Q5 - Test the performance of write back and write through policy based on 4-way associative cache with isscc_pcm(15%) 

- write back
    - command：
    ```!
    ./build/X86/gem5.opt -d ../q5_out/wirte_back/ configs/example/se.py -c ./tests/test-progs/hello/bin/x86/linux/benchmark/multiply --cpu-type=TimingSimpleCPU --caches --l1d_assoc=4 --l1i_assoc=4 --l2cache --l2_assoc=4 --l3cache --l3_assoc=4 --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=512kB --mem-type=NVMainMemory --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config > ../q5_out/log
    ```
    - log：
        - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/34430e85-dd55-4fe5-b853-0ddd3119a6da)

- write through
    - gem5/config/common/Caches.py中，將cache1、2、3中加入writeback_clean = True
        - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/24c8e0c3-dffa-45a9-a7e6-d7c79abb7dff)
        - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/944321e2-7e91-4c5e-b608-11da3cf17d77)
        - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/05b97ca6-af3f-4be4-9e59-c95a1d91d911)

    - command：

    ```!
    ./build/X86/gem5.opt -d ../q5_out/wirte_back/ configs/example/se.py -c ./tests/test-progs/hello/bin/x86/linux/benchmark/multiply --cpu-type=TimingSimpleCPU --caches --l1d_assoc=4 --l1i_assoc=4 --l2cache --l2_assoc=4 --l3cache --l3_assoc=4 --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=512kB --mem-type=NVMainMemory --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config > ../q5_out/log
    ```

    - log：
        - ![image](https://github.com/Tommy-happy/gem5_final_project/assets/112320408/1904d5b0-657f-4df5-929c-bf2285c3da3a)





