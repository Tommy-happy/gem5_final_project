## Q1 - GEM5 + NVMAIN BUILD-UP (40%)

## Q2 - Enable L3 last level cache in GEM5 + NVMAIN (15%) 
- 在 gem5/config/common/Caches.py 新增L3Cache
    - ![image](https://hackmd.io/_uploads/SyUOqsTHC.png)
- 在 gem5/config/common/CacheConfig.py 
    - 第三個 if 中加上 l3_cache
        - ![image](https://hackmd.io/_uploads/SJU9lnTHC.png)

    - 新增一個 if 判斷式，判斷如果 option 中啟用 L3 cache 的狀況
        - ![image](https://hackmd.io/_uploads/HJhioo6rA.png)
- 在 gem5/config/common/Options.py 中新增一條 option 
    - ![image](https://hackmd.io/_uploads/S1DPRjpHA.png)

- 在 gem5/src/mem/XBar.py 中新增L3XBar這個class (從L2XBar複製過來)
    - ![image](https://hackmd.io/_uploads/BJfzZnar0.png)

- 在 gem5/src/cpu/BaseCPU.py 中
    - 引入 l3 cache
        - ![image](https://hackmd.io/_uploads/H11lynarA.png)
    - 新增一個函數 (把l2的函數二都改成三即可)
    - ![image](https://hackmd.io/_uploads/rJsP12aBR.png)

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
    - ![image](https://hackmd.io/_uploads/BkdQMuarA.png)

### full-way command
- command
    ```!
    ./build/X86/gem5.opt -d ../q3_out/full_way/ configs/example/se.py -c tests/test-progs/hello/bin/x86/linux/benchmark/quicksort --cpu-type=TimingSimpleCPU --caches --l2cache --l3cache --l3_assoc=1 --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=512kB --mem-type=NVMainMemory --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config > ../q3_out/full_way/log
    ```
    - 適當調整大小讓l3 cache 的 miss rate 有變化
- miss rate
    - ![image](https://hackmd.io/_uploads/Bk9Hf_aHA.png)


## Q4 - Modify last level cache policy based on frequency based replacement policy (15%)

- 這裡用2-way asscoiative
- 在 gem5/config/common/Caches.py
    - 新增一項規則：replacement_policy = Param.BaseReplacementPolicy(LFURP(),"Replacement policy")
    - ![image](https://hackmd.io/_uploads/BkiysuarR.png)

- 原本的replacement policy：![image](https://hackmd.io/_uploads/BydEvOTSR.png)

- 後來的replacement policy：![image](https://hackmd.io/_uploads/rkTLU_prC.png)

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
        - ![image](https://hackmd.io/_uploads/rJgTVo6BR.png)



- write through
    - gem5/config/common/Caches.py中，將cache1、2、3中加入writeback_clean = True
        - ![image](https://hackmd.io/_uploads/r14a65TBA.png)
        - ![image](https://hackmd.io/_uploads/BJzBbsaBA.png)
        - ![image](https://hackmd.io/_uploads/ByoSZiTr0.png)


    - command：

    ```!
    ./build/X86/gem5.opt -d ../q5_out/wirte_back/ configs/example/se.py -c ./tests/test-progs/hello/bin/x86/linux/benchmark/multiply --cpu-type=TimingSimpleCPU --caches --l1d_assoc=4 --l1i_assoc=4 --l2cache --l2_assoc=4 --l3cache --l3_assoc=4 --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=512kB --mem-type=NVMainMemory --nvmain-config=../NVmain/Config/PCM_ISSCC_2012_4GB.config > ../q5_out/log
    ```

    - log：
        - ![image](https://hackmd.io/_uploads/ByqlWiaBA.png)




