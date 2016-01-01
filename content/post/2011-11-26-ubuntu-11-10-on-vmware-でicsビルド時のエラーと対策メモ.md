---
title: Ubuntu 11.10 on VMware でICSビルド時のエラーと対策メモ
author: kwmt
layout: post
date: 2011-11-25
url: /index.php/2011/11/26/ubuntu-11-10-on-vmware-でicsビルド時のエラーと対策メモ/
categories:
  - Android
tags:
  - Android

---
新しくVMwareをインストールして、<a href="http://www.ubuntulinux.jp/products/JA-Localized/download" target="_blank">Ubuntu11.10 日本語Remix CDイメージをダウンロード</a>して、<a href="http://source.android.com/source/initializing.html" target="_blank">これ</a>を見ながらICSのビルド環境を構築して、makeしたときにエラーが出たのでエラーの内容と対策をメモります。 

## 最初に出たエラー {.section}

エラー内容
:   host C++: obbtool <= frameworks/base/tools/obbtool/Main.cpp :0:0: error: "\_FORTIFY\_SOURCE" redefined [-Werror] :0:0: note: this is the location of the previous definition cc1plus: all warnings being treated as errors make: \*** [out/host/linux-x86/obj/EXECUTABLES/obbtool_intermediates/Main.o] Error 1 

原因
:   ブランチandroid-4.0.1_r1をダウンロードしたから。
  
    詳しくはこちら→
  
    <a href="http://code.google.com/p/android/issues/detail?id=20795" target="_blank">http://code.google.com/p/android/issues/detail?id=20795</a> 

対策
:   masterブランチをダウンロードした。
  
    たしかに、core/combo/HOST_linux-x86.mkが
  
    HOST\_GLOBAL\_CFLAGS += -U\_FORTIFY\_SOURCE -D\_FORTIFY\_SOURCE=0
  
    のように修正されてた。これだとうまくいくらしい。 

参考
:   <a href="http://e-xiao.blogspot.com/2011/10/resolve-build-errors-for-android-234.html" target="_blank">http://e-xiao.blogspot.com/2011/10/resolve-build-errors-for-android-234.html</a>

## 次に出たエラー {.section}

エラー内容
:   host Executable: adb (out/host/linux-x86/obj/EXECUTABLES/adb_intermediates/adb)
  
    host Executable: atree (out/host/linux-x86/obj/EXECUTABLES/atree_intermediates/atree)
  
    host Executable: bb2sym (out/host/linux-x86/obj/EXECUTABLES/bb2sym_intermediates/bb2sym)
  
    host Executable: bb\_dump (out/host/linux-x86/obj/EXECUTABLES/bb\_dump\_intermediates/bb\_dump)
  
    /usr/bin/ld: cannot find -lncurses
  
    collect2: ld returned 1 exit status
  
    make: \*** [out/host/linux-x86/obj/EXECUTABLES/adb_intermediates/adb] Error 1
  
    make: \*** Waiting for unfinished jobs&#8230;.
  
    true
  
    true
  
    true 

対策
:   libncurses5-devをインストールした。
  
    $ sudo apt-get install libncurses5-dev
  
    はじめに
  
    <a href="http://source.android.com/source/initializing.html" target="_blank">http://source.android.com/source/initializing.html</a>
  
    の「Installing required packages」のタイミングで、入れ忘れてただけ。 

## 最後に出たエラー {.section}

エラー内容
:   Install: out/host/linux-x86/bin/llvm-link
  
    out/host/linux-x86/obj/STATIC\_LIBRARIES/libLLVMCodeGen\_intermediates/libLLVMCodeGen.a: file not recognized: File truncated
  
    collect2: ld returned 1 exit status
  
    make: \*** [out/host/linux-x86/obj/EXECUTABLES/llvm-rs-cc_intermediates/llvm-rs-cc] Error 1
  
    make: \*** Waiting for unfinished jobs&#8230;.
  
    out/host/linux-x86/obj/STATIC\_LIBRARIES/libLLVMCodeGen\_intermediates/libLLVMCodeGen.a: file not recognized: File truncated
  
    collect2: ld returned 1 exit status
  
    make: \*** [out/host/linux-x86/obj/EXECUTABLES/clang_intermediates/clang] Error 1
  
    elapsed seconds: 175
  
    wrote generated Main\_*.java files to out/host/linux-x86/obj/EXECUTABLES/vm-tests\_intermediates/main_files 

対策
:   はじめからする。
  
    具体的には、
  
    Mydroid>rm -Rf *
  
    いったん全削除して、
  
    repo init -u http://android.googlesource.com/platform/manifest
  
    して、
  
    repo sync
  
    して、
  
    make -j4
  
    。 

参考
:   <a href="https://groups.google.com/group/android-building/browse_thread/thread/cae10abd771f2415?hl=en#" target="_blank">https://groups.google.com/group/android-building/browse_thread/thread/cae10abd771f2415?hl=en#</a>
  
    
  
    Nov 22 4:45 amのRANDYRKELLYさんの投稿に対しての、Nov 22,5:05 am のConley Owensさんの投稿