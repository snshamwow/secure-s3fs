##Secure-S3FS Installation Instructions:

## 1. Installing S3FS-Secure:

First, download S3FS-Secure from https://github.com/tylerlafrance/secure-s3fs/ either through browser or through command:  
git clone https://github.com/tylerlafrance/secure-s3fs.git

Ensure your system satisfies build and runtime dependencies for:
* fuse >= 2.8.4
* automake
* gcc-c++
* make
* libcurl
* libxml2
* openssl
* mime.types (the package providing depends on the OS)
    * s3fs tries to detect `/etc/mime.types` as default regardless of the OS
    * Else s3fs tries to detect `/etc/apache2/mime.types` if OS is macOS
    * s3fs exits with an error if these files are not exist
    * Alternatively, you can set mime.types file path with `mime` option without detecting these default files
* pkg-config (or your OS equivalent)

This can be done with the following command:  
sudo apt-get install build-essential libcurl4-openssl-dev libxml2-dev mime-support

Open s3fs-secure/src/fdcache.cpp to edit the source code.
To set the encryption key location, edit line 80 to the proper file location.
Similarly, edit line 81 to change the location of the temporary storage file.
Last, to change your backup key, edit line 130's "gj5416" to your own backup key and change line 128 to the number of characters in your backup key.

Next, navigate to your s3fs-secure folder and execute the following terminal commands:  
./autogen.sh  
./configure  
make  
sudo make install  

## 2. Connecting s3fs to Amazon AWS Account:
In terminal, execute the following commands:
echo <access-key-id>:<secret-access-key> > /etc/passwd-s3fs
chmod 600 /etc/passwd-s3fs
(Replace <access-key-id> and <secret-access-key> with the actual IAM user keys)

## 3. Create local mounting point
sudo mkdir /path/to/mountpoint

## 4. Mounting to local file system
login with root user:
sudo su
Add an entry to fstab to mount the bucket:
echo s3fs#<s3-bucket> /mnt/<test-bucket> fuse _netdev,rw,nosuid,nodev,allow_other,nonempty 0 0 >> /etc/fstab
(Replace the leading <s3-bucket> with your AWS s3 bucket name and the /mnt/<test-bucket> with the mount point which you have created)

Remount all mount points, which now include our new s3 bucket:
mount -a

## 5. Verify that S3 Bucket is mounted to linux file system
df -h
Through this command output, you can see s3fs under Filesystem, mounted on your specified mountpoint.

#Standalone RC4 Installation Instructions

## 1. Compile rc4.c
To compile rc4.c, you need to include the directory of the libcrypto library. One such compilation example:
gcc -I/path/to/openssl/include/openssl -lcrypto -o rc4 rc4.c

## 2. Execute rc4.c
To execute:
./exe [-e/-d] [-salt/-nosalt] [key] [input] [output]
