# libgsasl 1.8.0 on loongarch64

## 构建（老 autotools）

    patch -p1 < doris-thirdparty/patches/libgsasl-1.8.0.patch
    cp ~/opt/tools/usr/share/automake-1.18/config.guess .   # 老 config.guess 不认 loongarch64
    cp ~/opt/tools/usr/share/automake-1.18/config.sub .
    ./configure --prefix=$P --enable-static --disable-shared \
        --without-gssapi --without-libidn CFLAGS="-std=gnu17 -fPIC -O2"
    make -j4 && make install

产物 `installed/lib/libgsasl.a`。

## 踩坑

- configure 会警告 `unrecognized options: --without-gssapi, --without-libidn`、`Libntlm >= 0.3.5 not found`，
  都不影响静态库产出（GSS 实现探测为 yes）。
- GCC 16 默认 C23 编不动这份老 K&R 风格代码 → `CFLAGS="-std=gnu17 ..."`。
- 必须换 `config.guess` / `config.sub`，否则 `cannot guess build type`。

## 验证

已链接进 `doris_be`，0 undefined symbol。
