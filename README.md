# qmetacc
Meta Class Compiler, a modified version of Qt's Meta-Object Compiler (moc)



cmake .. -DCMAKE_PREFIX_PATH=D:\testcode\installtest

cmake --build .

## How to test qmetacc.exe: ##

```sh
qmetacc.exe -o main_moc.cpp main.cpp
```

or
```sh
qmetacc.exe -h
```