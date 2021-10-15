# Titan_m_aes_cbc_nopad_bug
Bug in AES CBC No padding mode retrieves 300 bytes of data from the secure element
 
Tested on a Pixel 4XL device

Found a critical vulnerability in the AES-CBC-No padding mode where an "Empty plain text sent to the Titan Strongbox Encryption/Decryption function returns a 380 bytes of data". When I raised this issue with the Android Team, I was told it was a critical vulnerability. Later after a week of assessment I was told it was infeasible to fix. When I asked for the details on the returned 300 bytes of data the android team said it was a random data.

Now the same scenario on an TEE environment works correctly(returns 0 length decrypted text) with the above mentioned steps.


Steps to reproduce:
1) Run the strongbox example app which encrypts/decrypts an empty plain text(PT)
2) The app prints the Plain text, IV used and Cipher text.
3) When an empty PT is supplied to the AES/CBC/NoPadding mode encrypt function, the system.out shows empty value which is expected and libnos_transport shows 0 bytes of PT is supplied, but when the same empty CT is supplied to decrypt function, the titan sends out an decrypted text of size 300 bytes. "Read app 2 reply data (387 bytes)".
4) When trying with an proper PT data the encryption and decryption happens with correct size of data in libnos_transport. 

#### Logcat

```
2021-07-11 14:23:54.622 5121-5121/com.example.strongbox I/System.out: Key generation starting
2021-07-11 14:23:54.624 1195-1195/? I/keystore: del USRPKEY_MyKeyAlias 10288
2021-07-11 14:23:54.625 1195-1195/? I/keystore: del USRCERT_MyKeyAlias 10288
2021-07-11 14:23:54.625 1195-1195/? I/keystore: del CACERT_MyKeyAlias 10288
2021-07-11 14:23:54.625 626-8428/? D/libnos_transport: Calling app 2 with params 0x0007
2021-07-11 14:23:54.626 626-8428/? E/libnos_datagram: can't send spi message: Try again
2021-07-11 14:23:54.642 626-8428/? I/chatty: uid=1064(hsm) Binder:626_2 identical 3 lines
2021-07-11 14:23:54.648 626-8428/? E/libnos_datagram: can't send spi message: Try again
2021-07-11 14:23:54.656 626-8428/? D/libnos_transport: App 2 inspection status=0x00000000 reply_len=0 protocol=1 flags=0x0000
2021-07-11 14:23:54.656 626-8428/? D/libnos_transport: Send app 2 command data (1542 bytes)
2021-07-11 14:23:54.667 626-8428/? D/libnos_transport: Send app 2 go command 0x00020007
2021-07-11 14:23:54.667 626-8428/? D/libnos_transport: Polling app 2
2021-07-11 14:23:54.695 626-8428/? D/libnos_transport: App 2 polled=13 status=0x80000000 reply_len=0 flags=0x0000
2021-07-11 14:23:54.696 626-8428/? D/libnos_transport: App 2 returning 0x0
2021-07-11 14:23:54.697 626-8428/? D/libnos_transport: Calling app 2 with params 0x0000
2021-07-11 14:23:54.700 626-8428/? D/libnos_transport: App 2 inspection status=0x00000000 reply_len=0 protocol=1 flags=0x0000
2021-07-11 14:23:54.700 626-8428/? D/libnos_transport: Send app 2 command data (18 bytes)
2021-07-11 14:23:54.700 626-8428/? D/libnos_transport: Send app 2 go command 0x00020000
2021-07-11 14:23:54.701 626-8428/? D/libnos_transport: Polling app 2
2021-07-11 14:23:54.705 626-8428/? D/libnos_transport: App 2 polled=2 status=0x80000000 reply_len=0 flags=0x0000
2021-07-11 14:23:54.706 626-8428/? D/libnos_transport: App 2 returning 0x0
2021-07-11 14:23:54.707 626-8428/? D/libnos_transport: Calling app 2 with params 0x0001
2021-07-11 14:23:54.709 626-8428/? D/libnos_transport: App 2 inspection status=0x00000000 reply_len=0 protocol=1 flags=0x0000
2021-07-11 14:23:54.709 626-8428/? D/libnos_transport: Send app 2 command data (62 bytes)
2021-07-11 14:23:54.710 626-8428/? D/libnos_transport: Send app 2 go command 0x00020001
2021-07-11 14:23:54.710 626-8428/? D/libnos_transport: Polling app 2
2021-07-11 14:23:54.725 626-8428/? D/libnos_transport: App 2 polled=7 status=0x80000000 reply_len=1662 flags=0x0000
2021-07-11 14:23:54.725 626-8428/? D/libnos_transport: Read app 2 reply data (1662 bytes)
2021-07-11 14:23:54.737 626-8428/? D/libnos_transport: App 2 returning 0x0
2021-07-11 14:23:54.765 5121-5121/com.example.strongbox I/System.out: Key generation completed


2021-07-11 14:23:54.765 5121-5121/com.example.strongbox I/System.out: PlainText:  

2021-07-11 14:23:54.765 5121-5121/com.example.strongbox I/System.out: Encryption Starting
2021-07-11 14:23:54.778 626-8428/? D/libnos_transport: Calling app 2 with params 0x0000
2021-07-11 14:23:54.780 626-8428/? D/libnos_transport: App 2 inspection status=0x00000000 reply_len=0 protocol=1 flags=0x0000
2021-07-11 14:23:54.780 626-8428/? D/libnos_transport: Send app 2 command data (18 bytes)
2021-07-11 14:23:54.780 626-8428/? D/libnos_transport: Send app 2 go command 0x00020000
2021-07-11 14:23:54.781 626-8428/? D/libnos_transport: Polling app 2
2021-07-11 14:23:54.785 626-8428/? D/libnos_transport: App 2 polled=2 status=0x80000000 reply_len=0 flags=0x0000
2021-07-11 14:23:54.785 626-8428/? D/libnos_transport: App 2 returning 0x0
2021-07-11 14:23:54.786 626-8428/? D/libnos_transport: Calling app 2 with params 0x000a
2021-07-11 14:23:54.788 626-8428/? D/libnos_transport: App 2 inspection status=0x00000000 reply_len=0 protocol=1 flags=0x0000
2021-07-11 14:23:54.788 626-8428/? D/libnos_transport: Send app 2 command data (1568 bytes)
2021-07-11 14:23:54.799 626-8428/? D/libnos_transport: Send app 2 go command 0x0002000a
2021-07-11 14:23:54.799 626-8428/? D/libnos_transport: Polling app 2
2021-07-11 14:23:54.810 626-8428/? D/libnos_transport: App 2 polled=5 status=0x80000000 reply_len=50 flags=0x0000
2021-07-11 14:23:54.810 626-8428/? D/libnos_transport: Read app 2 reply data (50 bytes)
2021-07-11 14:23:54.811 626-8428/? D/libnos_transport: App 2 returning 0x0
2021-07-11 14:23:54.814 626-8428/? D/libnos_transport: Calling app 2 with params 0x000c
2021-07-11 14:23:54.816 626-8428/? D/libnos_transport: App 2 inspection status=0x00000000 reply_len=0 protocol=1 flags=0x0000
2021-07-11 14:23:54.816 626-8428/? D/libnos_transport: Send app 2 command data (20 bytes)
2021-07-11 14:23:54.817 626-8428/? D/libnos_transport: Send app 2 go command 0x0002000c
2021-07-11 14:23:54.818 626-8428/? D/libnos_transport: Polling app 2
2021-07-11 14:23:54.822 626-8428/? D/libnos_transport: App 2 polled=2 status=0x80000000 reply_len=0 flags=0x0000
2021-07-11 14:23:54.822 626-8428/? D/libnos_transport: App 2 returning 0x0
2021-07-11 14:23:54.824 5121-5121/com.example.strongbox I/System.out: IV: [B@f2fad0d
2021-07-11 14:23:54.824 5121-5121/com.example.strongbox I/System.out: Encrypted data:
2021-07-11 14:23:54.824 5121-5121/com.example.strongbox I/System.out: [B@314d4c2
2021-07-11 14:23:54.825 5121-5121/com.example.strongbox I/System.out: []
2021-07-11 14:23:54.825 5121-5121/com.example.strongbox I/System.out: Encryption Completed


2021-07-11 14:23:54.825 5121-5121/com.example.strongbox I/System.out: Decryption Starting
2021-07-11 14:23:54.832 626-8428/? D/libnos_transport: Calling app 2 with params 0x000a
2021-07-11 14:23:54.834 626-8428/? D/libnos_transport: App 2 inspection status=0x00000000 reply_len=0 protocol=1 flags=0x0000
2021-07-11 14:23:54.834 626-8428/? D/libnos_transport: Send app 2 command data (1594 bytes)
2021-07-11 14:23:54.838 32253-32303/? E/nightwatch-target: lmkd signal waiters
2021-07-11 14:23:54.845 626-8428/? D/libnos_transport: Send app 2 go command 0x0002000a
2021-07-11 14:23:54.845 626-8428/? D/libnos_transport: Polling app 2
2021-07-11 14:23:54.856 626-8428/? D/libnos_transport: App 2 polled=5 status=0x80000000 reply_len=15 flags=0x0000
2021-07-11 14:23:54.856 626-8428/? D/libnos_transport: Read app 2 reply data (15 bytes)
2021-07-11 14:23:54.857 626-8428/? D/libnos_transport: App 2 returning 0x0
2021-07-11 14:23:54.860 626-8428/? D/libnos_transport: Calling app 2 with params 0x000c
2021-07-11 14:23:54.862 626-8428/? D/libnos_transport: App 2 inspection status=0x00000000 reply_len=0 protocol=1 flags=0x0000
2021-07-11 14:23:54.862 626-8428/? D/libnos_transport: Send app 2 command data (21 bytes)
2021-07-11 14:23:54.863 626-8428/? D/libnos_transport: Send app 2 go command 0x0002000c
2021-07-11 14:23:54.863 626-8428/? D/libnos_transport: Polling app 2
2021-07-11 14:23:54.867 626-8428/? D/libnos_transport: App 2 polled=2 status=0x80000000 reply_len=387 flags=0x0000
2021-07-11 14:23:54.867 626-8428/? D/libnos_transport: Read app 2 reply data (387 bytes)
2021-07-11 14:23:54.871 626-8428/? D/libnos_transport: App 2 returning 0x0
2021-07-11 14:23:54.892 5121-5121/com.example.strongbox I/System.out: Decrypted data: f>��H5���,!a5���(/V�Q6����۽�'�l�g��b��b�v �aUC,֔��9Cw��\���*��o�aQ)kI����ߔ@w��*��$��d͓�&=
2021-07-11 14:23:54.892 5121-5121/com.example.strongbox I/System.out: ��m��]�EP���eC����H��d��);���Ilo҈ -/�`���3�(��,���8k8R���jX�� P�P�����03�kX��0���?�6�����AE`����o�K}�Ϗk[��߹�1�z�����{���y�T�ֺ��hF�(��0��WԱ�� ��������K������G�\�m��7��� J��=�����@Y�M�R.������/��A���s�;��w�G���n�����*6���иrͨUO��
2021-07-11 14:23:54.892 5121-5121/com.example.strongbox I/System.out: ���Y��ڵ����|(Zm�������@�J���>�2�b�k9�Y]�����ͅb�����W
2021-07-11 14:23:54.892 5121-5121/com.example.strongbox I/System.out: [B@8bde2d3
2021-07-11 14:23:54.896 5121-5121/com.example.strongbox I/System.out: [102, 62, -17, -65, -67, -17, -65, -67, 72, 53, -17, -65, -67, 15, -17, -65, -67, 44, 33, 97, 53, -17, -65, -67, -17, -65, -67, -17, -65, -67, 40, 47, 86, -17, -65, -67, 81, 54, -17, -65, -67, -17, -65, -67, -17, -65, -67, 2, -37, -67, 26, 39, -17, -65, -67, 108, -17, -65, -67, 103, -17, -65, -67, -17, -65, -67, 98, -17, -65, -67, -17, -65, -67, 98, -17, -65, -67, 118, 9, -17, -65, -67, 97, 85, 67, 44, -42, -108, 5, -17, -65, -67, 57, 67, 119, 26, 17, 92, 19, -17, -65, -67, -17, -65, -67, 42, -17, -65, -67, -17, -65, -67, 111, -17, -65, -67, 97, 81, 41, 107, 73, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, -33, -108, 64, 119, -17, -65, -67, -17, -65, -67, 42, -17, -65, -67, -17, -65, -67, 36, -17, -65, -67, 22, 100, -51, -109, -17, -65, -67, 38, 61, 10, -17, -65, -67, -17, -65, -67, 109, -17, -65, -67, -17, -65, -67, 93, -17, -65, -67, 69, 80, -17, -65, -67, 20, 24, 101, 67, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, 72, -17, -65, -67, -17, -65, -67, 100, -17, -65, -67, -17, -65, -67, 41, 59, -17, -65, -67, 21, -17, -65, -67, 73, 108, 111, -46, -120, 32, 45, 47, 15, 96, -17, -65, -67, -17, -65, -67, -17, -65, -67, 51, -17, -65, -67, 40, -17, -65, -67, -17, -65, -67, 44, -17, -65, -67, 0, 56, 107, 56, 82, -17, -65, -67, 14, 18, 106, 88, -17, -65, -67, 127, 32, 80, -17, -65, -67, 80, -17, -65, -67, -17, -65, -67, 31, -17, -65, -67, -17, -65, -67, 48, 51, -17, -65, -67, 107, 88, 0, 48, 11, -17, -65, -67, -17, -65, -67, 63, -17, -65, -67, 54, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, 65, 69, 96, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, 111, -17, -65, -67, 75, 125, -17, -65, -67, 12, -49, -113, 107, 91, -17, -65, -67, -17, -65, -67, -33, -71, -17, -65, -67, 49, -17, -65, -67, 122, 18, -17, -65, -67, -17, -65, -67, 21, -17, -65, -67, 123, -17, -65, -67, 25, 7, 121, -17, -65, -67, 84, 25, -42, -70, -17, -65, -67, 16, 104, 70, -17, -65, -67, 40, -17, -65, -67, -17, -65, -67, 48, -17, -65, -67, -17, -65, -67, 87, -44, -79, -17, -65, -67, 29, 32, -17, -65, -67, -17, -65, -67, 22, -17, -65, -67, -17, -65, -67, 127, -17, -65, -67, -17, -65, -67, 75, -17, -65, -67, 1, 15, -17, -65, -67, 17, 27, 71, -17, -65, -67, 92, 3, 109, 1, -17, -65, -67, 55, -17, -65, -67, -17, -65, -67, -17, -65, -67, 32, 74, 20, -17, -65, -67, 61, 15, 6, 5, -17, -65, -67, -17, -65, -67, 64, 89, -17, -65, -67, 77, -17, -65, -67, 82, 46, -17, -65, -67, -17, -65, -67, -17, -65, -67, 0, -17, -65, -67, 47, -17, -65, -67, -17, -65, -67, 65, -17, -65, -67, -17, -65, -67, -17, -65, -67, 115, -17, -65, -67, 59, 22, -17, -65, -67, 119, -17, -65, -67, 71, -17, -65, -67, 29, -17, -65, -67, 110, -17, -65, -67, 30, -17, -65, -67, 22, -17, -65, -67, 42, 54, -17, -65, -67, -17, -65, -67, -17, -65, -67, -48, -72, 114, -51, -88, 85, 79, -17, -65, -67, -17, -65, -67, 10, -17, -65, -67, -17, -65, -67, -17, -65, -67, 89, -17, -65, -67, 18, -38, -75, 7, -17, -65, -67, -17, -65, -67, 24, 124, 40, 90, 109, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, 64, -17, -65, -67, 74, 21, -17, -65, -67, 25, 62, -17, -65, -67, 50, -17, -65, -67, 98, -17, -65, -67, 107, 57, -17, -65, -67, 89, 93, 0, -17, -65, -67, -17, -65, -67, -17, -65, -67, -51, -123, 98, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, -17, -65, -67, 87]
2021-07-11 14:23:54.896 5121-5121/com.example.strongbox I/System.out: Decryption Completed
```
