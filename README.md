# PHP binaries & PHP build scripts for PocketMine-MP
[![Build status](https://github.com/pmmp/php-build-scripts/actions/workflows/main.yml/badge.svg)](https://github.com/pmmp/php-build-scripts/actions/workflows/main.yml)

## Prebuilt binaries
### Actively updated "latest" URLs
- [PM5 default](https://github.com/pmmp/PHP-Binaries/releases/tag/pm5-latest)
- [PM5 PHP 8.2](https://github.com/pmmp/PHP-Binaries/releases/tag/pm5-php-8.2-latest) (default for PM 5.10+)
- [PM5 PHP 8.3](https://github.com/pmmp/PHP-Binaries/releases/tag/pm5-php-8.3-latest) (not officially supported, but works in PM 5.9+)
- [PM5 PHP 8.4](https://github.com/pmmp/PHP-Binaries/releases/tag/pm5-php-8.4-latest) (not officially supported, but works in PM 5.33+)

### Legacy binaries, no longer updated
- [PM4 PHP 8.0](https://github.com/pmmp/PHP-Binaries/releases/tag/pm4-php-8.0-latest) (default for PM 4.0+)
- [PM4 PHP 8.1](https://github.com/pmmp/PHP-Binaries/releases/tag/pm4-php-8.1-latest) (default for PM 4.21+)
- [PM4 PHP 8.2](https://github.com/pmmp/PHP-Binaries/releases/tag/pm4-php-8.2-latest) (never officially supported, but works in PM 4.12+)
- [PM5 PHP 8.0](https://github.com/pmmp/PHP-Binaries/releases/tag/pm5-php-8.0-latest) (default for PM 5.0 alpha)
- [PM5 PHP 8.1](https://github.com/pmmp/PHP-Binaries/releases/tag/pm5-php-8.1-latest) (default for PM 5.0+)

## compile.sh

Bash script used to compile PHP on MacOS and Linux platforms. Make sure you have ``make autoconf automake libtool m4 wget getconf gzip bzip2 bison g++ git cmake pkg-config re2c ca-certificates``.

### Recommendations
- If you're going to use the compiled binary only on the machine you're build it on, remove the `-t` option for best performance - this will allow the script to optimize for the current machine rather than a generic one.
- [`ext-gd2`](https://www.php.net/manual/en/book.image.php) is NOT included unless the `-g` flag is provided, as PocketMine-MP doesn't need it. However, if your plugins need it, don't forget to enable it using `-g`.
- The `-c` and `-l` options can be used to specify cache folders to speed up recompiling if you're recompiling many times (e.g. to improve the script).

### Common pitfalls
- Avoid using the script in directory trees containing spaces. Some libraries don't like trying to be built in directory trees containing spaces, e.g. `/home/user/my folder/pocketmine-mp/` might experience problems.
- Avoid directory trees containing special (non-English) symbols. For example, `Développement` might cause issues.

### Additional notes
#### Mac OSX (native compile)
- Most dependencies can be installed using Homebrew
- You will additionally need `glibtool` (GNU libtool, xcode libtool won't work)

#### Android 64-bit (cross-compile)
- Only aarch64 targets are supported for Android cross-compile.
- The `aarch64-linux-musl` toolchain is required. You can compile and install it using https://github.com/pmmp/musl-cross-make (PMMP fork includes musl-libc patches for DNS resolver config path and increasing stack size limit for LevelDB)

| Script flags | Description                                                                                                 |
|--------------|-------------------------------------------------------------------------------------------------------------|
| -c           | Uses the folder specified for caching downloaded tarballs, zipballs etc.                                    |
| -d           | Compiles with debugging symbols and disables optimizations (slow, but useful for debugging segfaults)       |
| -D           | Compiles with separated debugging symbols, but leaves optimizations enabled (used for distributed binaries) |
| -g           | Will compile GD2                                                                                            |
| -F           | Will compile FFI                                                                                            |
| -j           | Set make threads to #                                                                                       |
| -l           | Uses the folder specified for caching compilation artifacts (useful for rapid rebuild and testing)          |
| -n           | Don't remove sources after completing compilation                                                           |
| -s           | Will compile everything statically                                                                          |
| -t           | Set target                                                                                                  |
| -v           | Enable Valgrind support in PHP                                                                              |
| -x           | Specifies we are doing cross-compile                                                                        |
| -P           | Set target to the specified major PocketMine-MP version specified (currently only `5` is supported)         |
| -z           | PHP version to build, e.g `8.2` (optional, if not specified, will be auto-selected by PM version)           |

### Example:

| Target          | Arguments                         |
|-----------------|-----------------------------------|
| linux64         | ``-t linux64 -j4 -P5``            |
| linux64, PM4    | ``-t linux64 -j4 -P4``            |
| mac64           | ``-t mac-x86-64 -j4 -P5``         |
| android-aarch64 | ``-t android-aarch64 -x -j4 -P5`` |

## windows-compile-vs.ps1

Uses Visual Studio toolsets to compile PHP binaries on Windows.
You need to install `git`, `cmake` and Visual Studio 2019 to use this script.

This script doesn't accept parameters, but the following environment variables are influential:

| Variable | Description                                                                                                         |
| -------- |---------------------------------------------------------------------------------------------------------------------|
| `PHP_DEBUG_BUILD`  | Disables optimisations and builds PHP with detailed debugging information (useful for debugging segfaults)|
| `SOURCES_PATH`     | Where to put the downloaded sources for compilation                                                       |
| `PM_VERSION_MAJOR` | Major version of PocketMine-MP to build for (currently only `5` is supported)                             |
| `PHP_VERSION_BASE` | PHP version to build, e.g `8.2` (optional, if not specified, will be auto-selected by PM version)         |
| `PHP_JIT_SUPPORT`  | Whether to compile with OPcache JIT, set to `1` for yes                                                   |

## For developers: Version info sources
### Libraries

| Link to package | Needed for | Notes |
|:----------------|:-----------|:------|
| [zlib](https://github.com/madler/zlib/tags) | Compression | |
| [gmp](https://gmplib.org/) | Big integer math for Bedrock packet encryption | Hosted at [DependencyMirror](https://github.com/pmmp/DependencyMirror/releases) to avoid service outages |
| [curl](https://github.com/curl/curl/releases) | Web requests | |
| [libyaml](https://github.com/yaml/libyaml/releases) | Parsing YAML config files | |
| [leveldb](https://github.com/pmmp/leveldb/commits/mojang-compatible/) | Bedrock world support | Custom version based on google/leveldb with minimum required changes to support MCPE worlds |
| [libxml](https://gitlab.gnome.org/GNOME/libxml2/-/releases) | XML parsing support for UPnP | Hosted at [DependencyMirror](https://github.com/pmmp/DependencyMirror/releases) to avoid service outages |
| [libpng](https://sourceforge.net/projects/libpng/files/libpng16/) | php-gd, plugin use only | Hosted at [DependencyMirror](https://github.com/pmmp/DependencyMirror/releases) to avoid service outages |
| [libjpeg](https://ijg.org/) | php-gd, plugin use only | Hosted at [DependencyMirror](https://github.com/pmmp/DependencyMirror/releases) to avoid service outages |
| [openssl](https://github.com/openssl/openssl/releases) | Bedrock packet encryption, secure web requests | |
| [libzip](https://github.com/nih-at/libzip/releases) | Resource packs | |
| [sqlite3](https://sqlite.org/download.html) | Plugin use only | Hosted at [DependencyMirror](https://github.com/pmmp/DependencyMirror/releases) to avoid service outages |
| [libdeflate](https://github.com/ebiggers/libdeflate/blob/master/NEWS.md) | Faster alternative to zlib for network use | |
| [pthreads4w](https://sourceforge.net/projects/pthreads4w/files/) | Needed by ext-pmmpthread on Windows | Hosted at [DependencyMirror](https://github.com/pmmp/DependencyMirror/releases) to avoid service outages |

### PHP & extensions

| Link to package | Needed for | Notes |
|:----------------|:-----------|:------|
| [PHP](https://www.php.net/releases/?json&version=8.2) | Everything | Replace 8.2 in the URL with your chosen version |
| [pmmpthread](https://github.com/pmmp/ext-pmmpthread/releases) | PHP threading | |
| [yaml](https://github.com/php/pecl-file_formats-yaml/tags) | YAML config parsing | Yes, the mix of - and _ is intentional. Don't ask me. |
| [leveldb](https://github.com/pmmp/php-leveldb/commits/pmmp-mojang-compatible/) | Bedrock world support | Custom version to provide `LEVELDB_ZLIB_RAW_COMPRESSION` support |
| [chunkutils2](https://github.com/pmmp/ext-chunkutils2/releases) | `PalettedBlockArray` and other low-level stuff | |
| [xdebug](https://github.com/xdebug/xdebug/releases) | Debugging | Not needed for production |
| [igbinary](https://github.com/igbinary/igbinary/releases) | Faster serialization, mostly for moving stuff between threads | Non-essential, could be ditched if necessary |
| [crypto](https://github.com/bukka/php-crypto/tags) | Bedrock packet encryption | |
| [recursionguard](https://github.com/pmmp/ext-recursionguard/releases) | Debugging | Not needed for production |
| [libdeflate](https://github.com/pmmp/ext-libdeflate/releases) | Faster network compression | Non-essential but provides significant performance advantage over zlib |
| [morton](https://github.com/pmmp/ext-morton) | Packing X/Z and X/Y/Z coordinates into ints in a format suitable for PHP array keys | Needed for performance |
| [xxhash](https://github.com/pmmp/ext-xxhash/releases) | Not currently used | Could be replaced by `hash()` in recent versions of PHP but this extension has much better performance |
| [arraydebug](https://github.com/pmmp/ext-arraydebug/tags) | Debugging array hash collisions | |
| [encoding](https://github.com/pmmp/ext-encoding/releases) | Not currently used | Experimental, intended to replace `BinaryUtils` but never finished |

### Misc

| Link to package | Needed for | Notes |
|:----------------|:-----------|:------|
| [php-sdk-binary-tools](https://github.com/php/php-sdk-binary-tools/releases) | Building PHP on Windows | |
