# Level 32 → Level 33

## Description
```Description
Level Goal
After all this git stuff, it’s time for another escape. Good luck!

Commands you may need to solve this level
sh, man
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit30
Password: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
```
## Lets Explore
```bash
WELCOME TO THE UPPERCASE SHELL
>> $0
$ ls
uppershell
$ cat uppershell
ELF4�64 
        (444``��� /�����DDP�td, ,,,,Q�tdR�td//lib/ld-linux.so.2GNU0cH▒{#ע[dı�X▒���GNU
           �( 
              �K��gUa4F&^-� !UM@▒_IO_stdin_usedfgetsstdinputsexitfflushsystem__libc_start_mainprintftouppersetreuidgeteuidlibc.so.6GLIBC_2.0GLIBC_2.����z@�n_start__fii
▒�       �
$�
 S�����/��������t�Ѓ[��5��%��%h������%������h������%
                                                   h▒������%h ������%h(������%▒h0������%h8�p����% h@�`����%$hH�P���1�^�����PTR���/jjQV��  ���P�8�����$���f�f�f�f�f�f�f��f�f�f�f�f�f�f���$�f�f�f�f�f�f��0=0t$���t����h0�Ѓ���.��&��.��&��&�0-0���������t ���tU����Ph0���Ít&Í�&���=Du����h����D�Í�&Í�&��늍L$����q�U��SQ�� �ȋ@������e��E�1��Z������S����SP���������
                                                       �I�������
                                                                h'���������
 j��������@��Ph�������P���������u
��
  j����ǅ�����9������������������
                                P������������������������������������������u���
     ������P��������G���S��#������,�[�WELCOME TO THE UPPERCASE SHELL>> (���������D����p�����zR|
                   X���2zR|
                          l��� 0h���F
                                     J
                                      tx?▒;*2$"$T
D��
 IuBuxu|��f
�▒���o���  �
�
8J0V��_0f�l~(��� ��,���@H▒CC: (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0 ��
    crt1.o__abi_tag__wrap_maincrtstuff.cderegister_tm_clones__do_global_dtors_auxcompleted.0__do_global_dtors_aux_fini_array_entryframe_dummy__frame_dummy_init_array_entryupper.c__FRAME_END___DYNAMIC__GNU_EH_FRAME_HDR_GLOBAL_OFFSET_TABLE___libc_start_main@GLIBC_2.34__x86.get_pc_thunk.bxprintf@GLIBC_2.0fflush@GLIBC_2.0fgets@GLIBC_2.0_edata_finigeteuid@GLIBC_2.0__data_startputs@GLIBC_2.0system@GLIBC_2.0__gmon_start__exit@GLIBC_2.0__dso_handle_IO_stdin_usedsetreuid@GLIBC_2.0stdin@GLIBC_2.0_end_dl_relocate_static_pie_fp_hw__bss_starttoupper@GLIBC_2.0__TMC_END___init.symtab.strtab.shstrtab.interp.note.gnu.build-id.note.ABI-tag.gnu.hash.dynsym.dynstr.gnu.version.gnu.version_r.rel.dyn.rel.plt.init.text.fini.rodata.eh_frame_hdr.eh_frame.init_array.fini_array.dynamic.got.got.plt.data.bss.comment�#��$� D���o��$N
                                                                        �V���^���o��k���o��0z     �       B��� �  ���#���� +�,, ,�XX ��/�/���/���/4�((@0 �000+\0�   3\�5$ 
```

Got this and Got Also Idea to How to exit from it using `$0` and then we run `whoami` &this done

```bash
$ ls
uppershell
$ whoami
bandit33
$ cat /etc/bandit_pass/bandit33
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
```

## Password
```Password
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
```
