# Fedora bootc examples

Fork of
https://github.com/redhat-cop/rhel-bootc-examples/tree/main/sealing
except geared towards demoing sealed images using Fedora on AWS with
nested virt.  Original README after the break below.

## Prep

- Launch Fedora 44 Cloud instance on AWS

  - https://fedoraproject.org/cloud/download/#cloud_launch

  - Instance type m8i.large, 30G storage, enable nested virt

- Install git-core and just

  ```
  sudo dnf install -y git-core just
  ```

- Clone this

  ```
  git clone https://github.com/jeckersb/fedora-sealing-demo
  cd fedora-sealing-demo
  ```

- Install dependencies

  ```
  sudo just deps
  ```

## Running

- Generate keys

  ```
  just keygen
  ```

- Verify custom secure boot database key

  ```
  $ openssl x509 -in target/keys/sb-db.crt -text -noout
  Certificate:
      Data:
          Version: 3 (0x2)
          Serial Number:
              53:24:c9:5a:00:a7:54:d9:62:01:ec:6d:95:c3:f3:f2:2f:1c:cc:b7
          Signature Algorithm: sha256WithRSAEncryption
          Issuer: CN=Cloud Native RTP Secure Boot Signature Database
          Validity
              Not Before: Jun 17 18:38:21 2026 GMT
              Not After : Jun 14 18:38:21 2036 GMT
          Subject: CN=Cloud Native RTP Secure Boot Signature Database
          Subject Public Key Info:
              Public Key Algorithm: rsaEncryption
                  Public-Key: (2048 bit)
                  Modulus:
                      00:ba:c7:87:12:f3:9b:05:47:be:6a:37:3a:4d:c8:
                      27:d5:c3:d5:71:10:66:9e:ae:2f:15:b2:19:48:4a:
                      d1:0a:96:58:5e:83:5d:85:03:ea:92:82:c8:00:be:
                      3a:58:5f:0b:87:5f:0e:30:04:fb:69:bb:28:8b:40:
                      b9:d5:cb:af:4d:14:bd:0b:00:3f:0b:47:8a:50:6b:
                      25:a2:e5:8b:8b:b6:a7:b9:0b:fb:ff:e2:be:7b:1a:
                      6e:91:f7:03:44:3c:de:51:7d:d0:b6:fe:39:46:07:
                      78:31:24:34:00:b1:32:41:29:a5:53:81:bf:28:74:
                      c9:43:cb:7d:65:8a:dc:30:93:51:22:f2:9c:63:b5:
                      76:a3:90:9a:10:d3:e1:c1:dc:b5:03:72:a9:d0:f6:
                      f9:33:0b:98:0d:a1:a1:de:be:53:dc:24:f5:c9:13:
                      3c:54:15:29:cf:cb:8f:1f:c6:aa:77:b1:90:24:ca:
                      1a:0c:99:52:19:29:50:8c:a6:9f:50:16:79:ff:8c:
                      b6:d1:af:69:b5:a1:f5:87:a5:a1:cf:6f:79:50:4d:
                      c0:d2:22:a7:5a:dd:01:de:07:61:2d:5d:a2:6f:3e:
                      10:c3:d8:eb:81:4a:a6:be:bc:3d:13:68:00:98:52:
                      b3:5a:56:ef:6e:ae:b3:6a:ab:f2:93:71:78:64:6d:
                      48:25
                  Exponent: 65537 (0x10001)
          X509v3 extensions:
              X509v3 Subject Key Identifier:
                  D9:23:4D:09:05:1C:4A:5C:9D:5F:C0:B0:25:76:02:1A:32:2D:C6:9F
              X509v3 Authority Key Identifier:
                  D9:23:4D:09:05:1C:4A:5C:9D:5F:C0:B0:25:76:02:1A:32:2D:C6:9F
              X509v3 Basic Constraints: critical
                  CA:TRUE
      Signature Algorithm: sha256WithRSAEncryption
      Signature Value:
          69:66:88:29:e2:91:70:74:4d:4d:5d:9f:10:82:49:dc:b7:fa:
          c7:7d:e0:00:84:ad:21:26:1c:46:fa:f1:12:d1:c4:09:a8:58:
          5a:15:ec:bb:d6:20:64:43:0f:46:36:50:70:4c:79:89:ef:9e:
          f6:ef:95:b3:47:e0:77:70:39:c9:82:3d:ec:c6:51:91:d8:b4:
          8d:be:e9:74:d9:58:de:3f:34:75:58:e4:8e:25:e4:ae:0a:7c:
          8e:27:e7:9d:c5:47:d4:94:b4:82:88:4b:3f:00:a4:45:94:e6:
          9c:11:36:63:9e:ac:92:0f:b8:a6:63:ac:89:7a:c5:0a:10:6a:
          8b:f9:82:10:ac:2e:84:66:a2:0c:10:79:3b:94:b2:4b:f9:8c:
          21:d7:0c:e2:97:88:4f:b0:40:72:cc:8c:63:cb:03:10:f0:24:
          aa:b5:53:59:82:fa:43:d9:92:64:16:23:d0:74:b8:e5:ce:70:
          f0:72:cd:ef:16:c4:a6:dc:de:3e:33:68:c1:b0:ba:d4:67:2d:
          24:4d:37:02:13:60:7e:3a:4b:d4:9f:04:bb:0b:96:79:30:06:
          91:d0:3d:6e:62:19:c4:7a:7f:ef:34:9d:4e:c5:2d:f3:c0:d5:
          a5:af:53:1f:a0:e4:cf:01:97:8e:f6:66:45:79:4f:00:6d:bd:
          28:57:b4:a6
  ```

- Build image, deploy and connect via `bcvk`

  ```
  just bcvk-ssh
  ```

## Verify sealing

- Verify secure boot is enabled

  ```
  [root@fedora ~]# mokutil --sb-state
  SecureBoot enabled
  ```

- List secure boot signature database keys

  ```
  [root@fedora ~]# mokutil --db
  [key 1]
  Owner: f4f4e748-8c2d-44a9-bdab-dc2ac0119b70
  SHA1 Fingerprint: e1:83:4c:46:76:c3:b6:f0:9e:e0:02:06:94:7b:97:f5:cb:ae:99:40
  Certificate:
      Data:
          Version: 3 (0x2)
          Serial Number:
              53:24:c9:5a:00:a7:54:d9:62:01:ec:6d:95:c3:f3:f2:2f:1c:cc:b7
          Signature Algorithm: sha256WithRSAEncryption
          Issuer: CN=Cloud Native RTP Secure Boot Signature Database
          Validity
              Not Before: Jun 17 18:38:21 2026 GMT
              Not After : Jun 14 18:38:21 2036 GMT
          Subject: CN=Cloud Native RTP Secure Boot Signature Database
          Subject Public Key Info:
              Public Key Algorithm: rsaEncryption
                  Public-Key: (2048 bit)
                  Modulus:
                      00:ba:c7:87:12:f3:9b:05:47:be:6a:37:3a:4d:c8:
                      27:d5:c3:d5:71:10:66:9e:ae:2f:15:b2:19:48:4a:
                      d1:0a:96:58:5e:83:5d:85:03:ea:92:82:c8:00:be:
                      3a:58:5f:0b:87:5f:0e:30:04:fb:69:bb:28:8b:40:
                      b9:d5:cb:af:4d:14:bd:0b:00:3f:0b:47:8a:50:6b:
                      25:a2:e5:8b:8b:b6:a7:b9:0b:fb:ff:e2:be:7b:1a:
                      6e:91:f7:03:44:3c:de:51:7d:d0:b6:fe:39:46:07:
                      78:31:24:34:00:b1:32:41:29:a5:53:81:bf:28:74:
                      c9:43:cb:7d:65:8a:dc:30:93:51:22:f2:9c:63:b5:
                      76:a3:90:9a:10:d3:e1:c1:dc:b5:03:72:a9:d0:f6:
                      f9:33:0b:98:0d:a1:a1:de:be:53:dc:24:f5:c9:13:
                      3c:54:15:29:cf:cb:8f:1f:c6:aa:77:b1:90:24:ca:
                      1a:0c:99:52:19:29:50:8c:a6:9f:50:16:79:ff:8c:
                      b6:d1:af:69:b5:a1:f5:87:a5:a1:cf:6f:79:50:4d:
                      c0:d2:22:a7:5a:dd:01:de:07:61:2d:5d:a2:6f:3e:
                      10:c3:d8:eb:81:4a:a6:be:bc:3d:13:68:00:98:52:
                      b3:5a:56:ef:6e:ae:b3:6a:ab:f2:93:71:78:64:6d:
                      48:25
                  Exponent: 65537 (0x10001)
          X509v3 extensions:
              X509v3 Subject Key Identifier:
                  D9:23:4D:09:05:1C:4A:5C:9D:5F:C0:B0:25:76:02:1A:32:2D:C6:9F
              X509v3 Authority Key Identifier:
                  D9:23:4D:09:05:1C:4A:5C:9D:5F:C0:B0:25:76:02:1A:32:2D:C6:9F
              X509v3 Basic Constraints: critical
                  CA:TRUE
      Signature Algorithm: sha256WithRSAEncryption
      Signature Value:
          69:66:88:29:e2:91:70:74:4d:4d:5d:9f:10:82:49:dc:b7:fa:
          c7:7d:e0:00:84:ad:21:26:1c:46:fa:f1:12:d1:c4:09:a8:58:
          5a:15:ec:bb:d6:20:64:43:0f:46:36:50:70:4c:79:89:ef:9e:
          f6:ef:95:b3:47:e0:77:70:39:c9:82:3d:ec:c6:51:91:d8:b4:
          8d:be:e9:74:d9:58:de:3f:34:75:58:e4:8e:25:e4:ae:0a:7c:
          8e:27:e7:9d:c5:47:d4:94:b4:82:88:4b:3f:00:a4:45:94:e6:
          9c:11:36:63:9e:ac:92:0f:b8:a6:63:ac:89:7a:c5:0a:10:6a:
          8b:f9:82:10:ac:2e:84:66:a2:0c:10:79:3b:94:b2:4b:f9:8c:
          21:d7:0c:e2:97:88:4f:b0:40:72:cc:8c:63:cb:03:10:f0:24:
          aa:b5:53:59:82:fa:43:d9:92:64:16:23:d0:74:b8:e5:ce:70:
          f0:72:cd:ef:16:c4:a6:dc:de:3e:33:68:c1:b0:ba:d4:67:2d:
          24:4d:37:02:13:60:7e:3a:4b:d4:9f:04:bb:0b:96:79:30:06:
          91:d0:3d:6e:62:19:c4:7a:7f:ef:34:9d:4e:c5:2d:f3:c0:d5:
          a5:af:53:1f:a0:e4:cf:01:97:8e:f6:66:45:79:4f:00:6d:bd:
          28:57:b4:a6
    ```

  (This is the database key generated from `just keygen` above)

- Verify bootloader signature (matches database key above)

  ```
  [root@fedora ~]# sbverify --list /boot/EFI/systemd/systemd-bootx64.efi
  signature 1
  image signature issuers:
   - /CN=Cloud Native RTP Secure Boot Signature Database
  image signature certificates:
   - subject: /CN=Cloud Native RTP Secure Boot Signature Database
     issuer:  /CN=Cloud Native RTP Secure Boot Signature Database
  ```

- Verify bootloader config

  ```
  [root@fedora ~]# cat /boot/loader/entries/bootc_fedora-44-1.conf
  title Fedora Linux 44 (Forty Four) 44
  version 44
  efi /EFI/Linux/bootc/bootc_composefs-29947de8e1958606920bd02605848766bec10e301b237671a764bef62764600a6876bd84a3e92055481895dbcdb335de16941a659b73fd38c7de46e880d64790.efi
  sort-key bootc-fedora-0
  ```

- Verify Unified Kernel Image signature

  ```
  [root@fedora ~]# sbverify --list /boot/EFI/Linux/bootc/bootc_composefs-29947de8e1958606920bd02605848766bec10e301b237671a764bef62764600a6876bd84a3e92055481895dbcdb335de16941a659b73fd38c7de46e880d64790.efi
  signature 1
  image signature issuers:
   - /CN=Cloud Native RTP Secure Boot Signature Database
  image signature certificates:
   - subject: /CN=Cloud Native RTP Secure Boot Signature Database
     issuer:  /CN=Cloud Native RTP Secure Boot Signature Database
  ```

- Verify kernel cmdline from UKI on disk

  ```
  [root@fedora ~]# objdump -s -j .cmdline /boot/EFI/Linux/bootc/bootc_composefs-29947de8e1958606920bd02605848766bec10e301b237671a764bef62764600a6876bd84a3e92055481895dbcdb335de16941a659b73fd38c7de46e880d64790.efi

  /boot/EFI/Linux/bootc/bootc_composefs-29947de8e1958606920bd02605848766bec10e301b237671a764bef62764600a6876bd84a3e92055481895dbcdb335de16941a659b73fd38c7de46e880d64790.efi:     file format pei-x86-64

  Contents of section .cmdline:
   14dfaf000 636f6d70 6f736566 733d3239 39343764  composefs=29947d
   14dfaf010 65386531 39353836 30363932 30626430  e8e1958606920bd0
   14dfaf020 32363035 38343837 36366265 63313065  2605848766bec10e
   14dfaf030 33303162 32333736 37316137 36346265  301b237671a764be
   14dfaf040 66363237 36343630 30613638 37366264  f62764600a6876bd
   14dfaf050 38346133 65393230 35353438 31383935  84a3e92055481895
   14dfaf060 64626364 62333335 64653136 39343161  dbcdb335de16941a
   14dfaf070 36353962 37336664 33386337 64653436  659b73fd38c7de46
   14dfaf080 65383830 64363437 39302072 7720636f  e880d64790 rw co
   14dfaf090 6e736f6c 653d6876 63302c31 31353230  nsole=hvc0,11520
   14dfaf0a0 30207379 7374656d 642e6a6f 75726e61  0 systemd.journa
   14dfaf0b0 6c642e66 6f727761 72645f74 6f5f636f  ld.forward_to_co
   14dfaf0c0 6e736f6c 653d31                      nsole=1
  ```
- Verify kernel cmdline from running kernel (matches above)

  ```
  [root@fedora ~]# cat /proc/cmdline
  composefs=29947de8e1958606920bd02605848766bec10e301b237671a764bef62764600a6876bd84a3e92055481895dbcdb335de16941a659b73fd38c7de46e880d64790 rw console=hvc0,115200 systemd.journald.forward_to_console=1
  ```

- Verify root filesystem

  ```
  [root@fedora ~]# findmnt -J /
  {
     "filesystems": [
        {
           "target": "/",
           "source": "composefs:29947de8e1958606920bd02605848766bec10e301b237671a764bef62764600a6876bd84a3e92055481895dbcdb335de16941a659b73fd38c7de46e880d64790",
           "fstype": "overlay",
           "options": "ro,relatime,seclabel,lowerdir=/proc/self/fd/5::/proc/self/fd/6,redirect_dir=on,metacopy=on,verity=require"
        }
     ]
  }
  ```

## Breaking it, for fun and profit!

- Sanity check that the `date` command works

  ```
  [root@fedora ~]# date
  Tue Jun 16 18:47:41 UTC 2026
  ```

- Remount sysroot rw so we can make "malicious" modifications to the
  underlying objects

  ```
  [root@fedora ~]# mount -o remount,rw /sysroot
  ```

- Find the object in the composefs object store backing the `date`
  command.  (Checking by size is fast and correct enough for demo
  purposes)

  ```
  [root@fedora ~]# stat -c %s /usr/bin/date
  98456
  [root@fedora ~]# find /sysroot/composefs/objects -type f -size 98456c
  /sysroot/composefs/objects/1b/ea65b0112e9e55f725330f2bf80c136518999e2f28aef1d2f26fe0d1b1a07808f5b81e2b7ab34f842c485b467e6c0ab9f86f91dc759efa91ab8547a296e514
  ```

- Make a "malicious" modification to the backing file.  The `date`
  binary contains the string `JUNE`; we will change it to `CNCF`
  instead.

  ```
  [root@fedora ~]# sed -i s/JUNE/CNCF/ /sysroot/composefs/objects/1b/ea65b0112e9e55f725330f2bf80c136518999e2f28aef1d2f26fe0d1b1a07808f5b81e2b7ab34f842c485b467e6c0ab9f86f91dc759efa91ab8547a296e514
  ```

- Drop dentry/inode cache.  For... reasons, overlayfs will not
  necessarily pick up our modification.  This will force the issue.

  ```
  [root@fedora ~]# echo 2 > /proc/sys/vm/drop_caches
  ```

- Try to run the corrupted `date` command again.

  ```
  [root@fedora ~]# date
  -bash: /usr/bin/date: Input/output error
  ```

  This fails because the fsverity digest no longer matches the
  expected value from the sealed image.  Success!

---

# (Original README) Sealed composefs boot

This example builds a RHEL 10.2 Image Mode system that boots with the
[bootc composefs backend](https://bootc.dev/bootc/experimental-composefs.html). A signed Unified Kernel Image (UKI) embeds the composefs digest of the root filesystem. At boot:

1. UEFI Secure Boot verifies the UKI signature against enrolled keys
2. The kernel starts with `composefs=<digest>` in the command line
3. The initramfs mounts the root as a composefs overlay with `verity=require`
4. Every file access is verified against its fs-verity digest

Note that this support is Tech Preview in RHEL 10.2. But we are interested
in feedback.

## Quick start with bcvk

### 1. Generate Secure Boot keys

```sh
just keygen
```

This generates PK, KEK, and db keypairs in `target/keys/` and copies
`db.crt` into `keys/` for committing. PK and KEK are only used for
firmware enrollment (bcvk handles this); db signs the boot artifacts.

It's important to understand that it is *your* Secure Boot keys used to sign the entire boot chain, and that [shim](https://github.com/rhboot/shim) is not used by default.

### 2. Build and boot

```sh
just bcvk-ssh
```

This single command builds the sealed container image and boots a VM with Secure Boot and the composefs backend.

### 3. Manual exploration

The `Justfile` is a relatively straightforward wrapper for `podman build` to generate the container image, and `bcvk` to run it as a local VM.

You can separate build and run steps:

```sh
just build
bcvk libvirt run --detach --ssh-wait --name sealed-demo \
    --secure-boot-keys target/keys \
    localhost/sealed-host:latest
bcvk libvirt ssh sealed-demo
```

Inside the VM:

```sh
mount | grep ' / '
# composefs:<digest> on / type overlay (ro,verity=require)

cat /proc/cmdline
# composefs=<digest> rw ...
```

Clean up:

```sh
bcvk libvirt rm --stop --force sealed-demo
```

## How it works

```
Build time
├── Containerfile
│     ├── Install packages (systemd-boot, sbsigntools, systemd-ukify)
│     ├── Sign systemd-boot with db key (secret: db.key, public: keys/db.crt)
│     ├── Rebuild initramfs with bootc dracut module
│     └── FROM scratch flatten (deterministic composefs digest)
├── bootc container ukify
│     ├── Compute composefs SHA-512 digest from flattened rootfs
│     ├── Embed digest + kargs in UKI command line
│     └── Sign UKI with db key
└── COPY --from=kernel /boot /boot

Boot time (UEFI → systemd-boot → UKI → composefs)
├── UEFI verifies systemd-boot signature against enrolled db cert
├── systemd-boot loads UKI
├── Kernel starts with composefs=<digest> in cmdline
├── initramfs: bootc-root-setup.service
│     ├── Mounts ext4 root partition
│     ├── Mounts EROFS metadata image (composefs image)
│     ├── Sets up overlayfs with verity=require
│     └── Bind-mounts /etc and /var from state
└── switch-root into composefs overlay
```

## Key management

Only one secret: the Secure Boot db private key (`db.key`). Everything
else is public or derived:

| File | Secret? | Where | Purpose |
|---|---|---|---|
| `keys/db.crt` | No | Committed to repo | Public cert; used by `sbsign` and enrolled in firmware |
| `db.key` | **Yes** | CI secret / local `target/keys/` | Signs systemd-boot and UKI |
| `PK.key`, `KEK.key` | Local only | `target/keys/` | Firmware enrollment (bcvk, virt-fw-vars, or cloud API) |

For CI, set one GitHub Actions secret:

| Secret | Description |
|---|---|
| `SECUREBOOT_DB_KEY` | Secure Boot db private key (PEM) |

PR builds use ephemeral keys so no secrets are needed for CI validation.
