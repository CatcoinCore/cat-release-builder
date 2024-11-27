# cat-release-builder

cat-release-builder is an updated gitian builder script for building Catcoin Core 0.21.x. 
It includes additional features and bugfixes not present in the original gitian-builder script.

We recommend building with docker mode, please see [the Docker installation website](https://docs.docker.com/engine/install/)
for appropriate Docker Desktop for your computer. When building, please make sure to have Docker Desktop open on your system.

### Setup

Replace USERNAME & version number. The version number should be the same as the tag, but without the `v` prefix.

```bash
$ git clone -b catcoin-changes https://github.com/CatcoinCore/cat-release-builder.git
$ cd cat-release-builder
$ ./build-release.py --docker --setup USERNAME 2.0.0-beta15 --disable-apt-cacher
```

### Usage

There are three different build modes, `--build`, `--sign` or `--buildsign`.

Building for specific target OS can be specified by parameter `-o` where `m` is macOS, `w` is Windows and `l` is Linux.

`-j` to specific number of build threads (there is a known issue where too many threads may cause build failures).

`-m` to specify amount of memory to allocate in MB.

**Example**
```
./build-release.py --docker --build kr105 2.0.0-beta15 -j 30 -m 10000 --no-commit -o w
```

**Additional help?**
```
./build-release.py --help
```


## Adding a New GPG Key for Gitian Building

## Generate a New Key

1. Generate a new GPG key:
```bash
gpg --full-generate-key
```

2. When prompted:

- Choose option 10 ((10) ECC (sign only))
- Accept the default elliptic curve
- Important: You must set a password, or some scripts will break
- Enter your USERNAME and email address

3. Export your public key:

```bash
gpg --export --output USERNAME-key.gpg USERNAME
```

4. Add your key to the repository:
- Copy the USERNAME-key.gpg file to the gitian-keys folder
- Commit and push your changes

5. Optional but recommended - Backup your private key:
```bash
gpg --export-secret-keys --armor USERNAME > USERNAME-key_private.asc
```
Store this backup with your password securely and offline.

Note: Replace USERNAME with your actual nickname throughout these commands.
