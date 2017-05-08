## ManagedOpenSSL.NET for VS 2008 & VS 2015

### Description
A managed [OpenSSL](https://www.openssl.org/) wrapper written in C# targeting the .NET Framework 3.5. It exposes both the [Crypto API](https://www.openssl.org/docs/crypto/crypto.html) and the [SSL API](https://www.openssl.org/docs/ssl/ssl.html).

This is a must for .NET developers that need crypto but don't want to use Microsoft's SSPI. The wrapper is based on version 1.0.2k of `libeay32.dll`and `ssleay32.dll`. Both the 32 Bit and 64 Bit versions of these DLLs are provided as 3PP in the `native` directory and have been taken from:

https://indy.fulgan.com/SSL/openssl-1.0.2k-i386-win32.zip

https://indy.fulgan.com/SSL/openssl-1.0.2k-x64_86-win64.zip

as suggested in the [OpenSSL Wiki](https://wiki.openssl.org/index.php/Binaries).

### Differences to [OpenSSL.net](https://github.com/openssl-net/openssl-net)
The following differences compared to [OpenSSL.net](https://github.com/openssl-net/openssl-net) apply:
 * backported project & solution files for Visual Studio 2008
 * new project & solution files for Visual Studio 2015
 * CLI project has been dropped
 * no unit tests + drop of related packages
 * add missing wrapper for `RSA_verify()` function

### Wrapper Example

The following is a partial example to show the general pattern of wrapping onto the C API.

Take DSA and the following C prototypes:

```
DSA *  DSA_new(void);
void   DSA_free(DSA *dsa);
int    DSA_size(const DSA *dsa);
int    DSA_generate_key(DSA *dsa);
int    DSA_sign(int dummy, const unsigned char *dgst, int len,
                unsigned char *sigret, unsigned int *siglen, DSA *dsa);
int    DSA_verify(int dummy, const unsigned char *dgst, int len,
                const unsigned char *sigbuf, int siglen, DSA *dsa);
```

Which gets wrapped as something akin to:

```
public class DSA : IDisposable
{
    // calls DSA_new()
    public DSA();

    // calls DSA_free() as needed
    ~DSA();

    // calls DSA_free() as needed
    public void Dispose();

    // returns DSA_size()
    public int Size { get; }

    // calls DSA_generate_key()
    public void GenerateKeys();

    // calls DSA_sign()
    public byte[] Sign(byte[] msg);

    // returns DSA_verify()
    public bool Verify(byte[] msg, byte[] sig);
}
```

### Installation

The binary package contains the ManagedOpenSsl.dll pre-compiled with VS 2015 and "Any CPU" platform. Both libeay32.dll and ssleay32.dll are statically linked native C DLLs without external dependencies. Hence you just have to make sure both `libeay32.dll` and `ssleay32.dll` are either located in the current working directory of your application or accessible by the `PATH` environment variable.

In your .NET project, add a reference to the `ManagedOpenSsl.dll` assembly.

If you're having trouble with the native library being loaded under mono, try using MONO_LOG_LEVEL=debug when invoking mono.

### Documentation

Take a look at the low-level C API [documentation](https://www.openssl.org/docs).

### License
This repository contains two software components with dedicated licenses:
 * The underlying, native OpenSSL libraries (only contained as binaries) are distributed under the terms of the [OpenSSL License & SSLeay License](https://www.openssl.org/source/license.html)
 * The ManagedOpenSSL library and related code are released under the original OpenSSL.net license, which is a BSD license. For more details see [LICENSE](LICENSE)
