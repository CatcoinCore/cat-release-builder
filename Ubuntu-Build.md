# Building Catcoin release on Ubuntu

## Prerequisites
### Hardware Requirements
Before beginning, ensure you have a Virtual Machine with the following specifications:
- 16 CPU cores
- 16 GB RAM
- 50 GB Disk space

### Software Requirements
You'll need Ubuntu Server 24.04, which can be downloaded as:
- Image file: `ubuntu-24.04.1-live-server-amd64.iso`
- Verify the SHA256 checksum: `e240e4b801f7bb68c20d1356b60968ad0c33a41d00d828e74ceb3364a0317be9`

## Installation Process

### 1. Docker Installation
First, we'll install Docker and its dependencies. Execute these commands in sequence or just copy/paste the entire block:

```bash
# Update package index and install prerequisites
sudo apt-get update
sudo apt-get install ca-certificates curl

# Set up Docker's GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update package index again and install Docker packages
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 2. Repository Setup
Clone the Catcoin repository:
```bash
git clone -b catcoin-changes https://github.com/CatcoinCore/cat-release-builder.git
cd cat-release-builder
```

### 3. GPG Key Configuration
If you already have a GPG key added to the repository, skip to step 4. Otherwise, follow these steps:

#### 3.1 Generate New GPG Key
```bash
gpg --full-generate-key
```

Important configuration options:
- Select option 10 (ECC - sign only)
- Use default elliptic curve
- Set expiration period (never expire is acceptable)
- **Must set a password** (required for scripts)
- Enter your USERNAME for the Real Name field
- Leave comment field empty
- Confirm with "O" key

#### 3.2 Export and Add Your Key
```bash
# Export public key
gpg --export --output USERNAME-key.gpg USERNAME

# Backup private key (recommended)
gpg --export-secret-keys --armor USERNAME > USERNAME-key_private.asc
```

**Important**: Store your private key backup and password securely offline.

Add your public key to the repository:
1. Move USERNAME-key.gpg to the gitian-keys folder
2. Commit and push your changes (optional)

### 4. Git Configuration
Configure your Git identity (replace with your details):
```bash
git config --global user.email "catoshi@catcoin.cat"
git config --global user.name "catoshi"
```

### 5. Build Setup
Prepare the build environment:
```bash
chmod a+x build-release.py
sudo ./build-release.py --docker --setup catoshi 2.0.0-beta15 --disable-apt-cacher
```
Note: If prompted about cache-ng, select 'no'

### 6. Building Windows Release
Start the build process:
```bash
sudo ./build-release.py --docker --build catoshi 2.0.0-beta15 -o w -j 16 -m 12000 --no-commit
```

Parameters explained:
- `-j 16`: Utilizes 16 CPU cores
- `-m 12000`: Allocates 12GB memory
- `-o w`: Targets Windows build
- `--no-commit`: Prevents automatic committing of results

### 7. Expected Output
After successful completion, you should see the following files:

```plaintext
File                                               Size            SHA256 Hash
catcoin-win64-debug.zip                           431,210,015     40237f73ce83f436d961f788a413819b5b699890cbcbc9e3d07e487231c3fb53
catcoin-win64-setup-unsigned.exe                   19,981,062     f05064ba0a24f257f979198a0bb99e1c80ee3a725b2bbbaa5a79ab0834b8af14
catcoin-win64.zip                                  34,851,817     b0aede3713b3ff4de695ba16a63254f4dcfcd077623b4404ff0e2feb41368674
catcoin-win-unsigned.tar.gz                        19,636,664     1c3c3d910da3793dff23a30c0fe833b36b5f66d100661b9df7c0a08cf3b2dad3
```
