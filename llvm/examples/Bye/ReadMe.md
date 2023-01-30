[opt invoke old/new pass manager](https://llvm.org/docs/NewPassManager.html#invoking-opt)

```shell
# old pass manager
opt -enable-new-pm=0 -load ../../cmake-build-debug/Bye/Bye.so -goodbye -wave-goodbye -time-passes < hello.ll > /dev/null
# new pass manager
opt -load-pass-plugin=../../cmake-build-debug/Bye/Bye.so --wave-goodbye -p=goodbye < hello.bc > /dev/null
```
