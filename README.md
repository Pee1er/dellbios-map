Title: Dell Flash BIOS upgrade driver dellbios.sys physical memory map

Vendor: Dell Inc.
Product: Flash BIOS upgrade driver
Component: dellbios.sys
SHA-256: b3a191ccd1df19cdf17fe6637d48266ac84c4310b013ad6973d8cb336b06ff69
MD5: 1a5a95d6bedbe29e5acf5eb6a727c634
SHA-1: c1777fcb7005b707f8c86b2370f3278a8ccd729f
Size: 7168 bytes
PE timestamp: 2005-08-02 20:38:51 UTC
FileVersion: none
ProductVersion: none
OriginalFilename: none
Embedded string: Dell Inc. Flash BIOS upgrade driver, Copyright (c) Dell Inc. - Release Version 2.0
That string is text in the file. It is not a VERSIONINFO FileVersion.
Authenticode status: NotSigned
Signer: none
Certificate thumbprint: none
Device: \\.\Dellbios
PDB: C:\BIOS\BIOS_DRV\new\objfre_wnet_AMD64\amd64\DBDrvr64.pdb
Test image: locally test-signed copy of this file, SHA-256 8d5f6d17e2ac83acd6279c6246875fa805e359076e11acc21e09f779f981fbf6, 8912 bytes. The test certificate is not a Dell signature.

CVE:

Description:
dellbios.sys in Dell Inc. Flash BIOS upgrade driver, which has no FileVersion (SHA-256 b3a191ccd1df19cdf17fe6637d48266ac84c4310b013ad6973d8cb336b06ff69, 7168 bytes, PE timestamp 2005-08-02 20:38:51 UTC), zero-extends a caller-supplied 32-bit physical address and maps a caller-supplied length with MmMapIoSpace on IOCTL 0x9B0C2410 (METHOD_BUFFERED), copies the caller's bytes into that mapping, and then unmaps it, without validating the range or translating a bus address, which allows a local user to read and write arbitrary physical memory within that 32-bit address and to gain local privilege escalation via \\.\Dellbios. On 2026-11-02, on an isolated Windows 10 Enterprise LTSC 2021 virtual machine (10.0.19044.7807), an administrator loaded a test-signed copy of this unsigned file after service start of the original returned Windows error 577, and local account aida-low, which is not a member of Administrators, opened \\.\Dellbios and sent IOCTL 0x9B0C2410 for physical address 0xbaae7000 with length one byte, wrote 0x5A and read 0x5A back through IOCTL 0x9B0C240C, then wrote 0xA5 and read 0xA5 back the same way, while an administrator process that did not send either IOCTL held that physical page and read the first byte as 0x5A and then 0xA5; the one-byte write is a reproduction of the caller-supplied length, IOCTL 0x9B0C240C confirms that write and is not a separate vulnerability, and the test did not show a crash. The device security descriptor was O:BAG:SYD:(A;;0x1201bf;;;WD)(A;;FA;;;SY)(A;;FA;;;BA)(A;;0x1200a9;;;RC)S:AI(ML;;NW;;;LW), and Everyone (WD) has access mask 0x1201bf, which includes FILE_READ_DATA and FILE_WRITE_DATA.

Suggested fix: on IOCTL 0x9B0C2410 and IOCTL 0x9B0C240C, copy only to or from a physical address chosen inside the driver, and limit access to \\.\Dellbios to Administrators and SYSTEM.
