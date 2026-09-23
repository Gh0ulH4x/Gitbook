# Level 26 → Level 27

## Description
```Description
## Level Goal

Good job getting a shell! Now hurry and grab the password for bandit27!

## Commands you may need to solve this level
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit26
Password: s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ
```

## Let's Explore 
We cant get bandit26 `SSH` so just continue the previous trail and got this 
```bash
bandit26@bandit:~$ ls
bandit27-do  text.txt
bandit26@bandit:~$ cat bandit27-do 
ELFp4�54 
         (444``���DD�� $$�����DDP�tdL LL,,Q�tdR�td//lib/ld-linux.so.2GNU5�S�ms/QZs��&_�提GNU  �K��-S '_IO_stdin_usedexit__libc_start_mainexecvprintf>���H��.6GLIBC_2.0GLIBC_2.34__gmon_start__4ii
     S������/��������t�Ѓ[��5��%��%h������%������h������%
                                                        h▒�����1�^�����PTR���t/jjQV������P�������$���f�f�f�f�f�f�f��f�f�f�f�f�f�f���$�f�f�f�f�f�f��▒=▒t$���t����h▒�Ѓ���.��&��.��&��&�▒-▒���������t ���tU����Ph▒���Ít&Í�&���=u����h����▒�Í�&Í�&��늍L$����q�U��Q���ȃ8 �@��P���������
                                                    j������P�9��ph=���������M�ɍa��S��������.�[�Run a command as another user.
  Example: %s id
env/usr/bin/env(�����$���Dd���p:����zR|
                                     ����2zR|
                                            ���� 0H���F
                                                       J
                                                        tx?▒;*2$"(T����aD
                                                                         IuAu|N
     A�C
        �P4
�▒���o�|�  �
        �
b
�▒���a�N[ jy���▒���CC: (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0 ��
   crt1.o__abi_tag__wrap_maincrtstuff.cderegister_tm_clones__do_global_dtors_auxcompleted.0__do_global_dtors_aux_fini_array_entryframe_dummy__frame_dummy_init_array_entrybandit27.c__FRAME_END___DYNAMIC__GNU_EH_FRAME_HDR_GLOBAL_OFFSET_TABLE___libc_start_main@GLIBC_2.34__x86.get_pc_thunk.bxprintf@GLIBC_2.0_edata_fini__data_start__gmon_start__exit@GLIBC_2.0__dso_handle_IO_stdin_usedexecv@GLIBC_2.0_end_dl_relocate_static_pie_fp_hw__bss_start__TMC_END___init.symtab.strtab.shstrtab.interp.note.gnu.build-id.note.ABI-tag.gnu.hash.dynsym.dynstr.gnu.version.gnu.version_r.rel.dyn.rel.plt.init.text.fini.rodata.eh_frame_hdr.eh_frame.init_array.fini_array.dynamic.got.got.plt.data.bss.comment�#��$� D���o�� N

                                    �
                                    pV||b^���o�k���o��0z      � B$$� �  P�ppw���� J�LL ,�xx ��/�/���/���/�▒▒0�0▒0+D0p     2��4
```

here is our hint this file contains something suspecious `Run a command as another user.Example: %s id`
```bash
bandit26@bandit:~$ %s id
bash: fg: %s: no such job
bandit26@bandit:~$ id
uid=11026(bandit26) gid=11026(bandit26) groups=11026(bandit26)
bandit26@bandit:~$ ./bandit27-do id
uid=11026(bandit26) gid=11026(bandit26) euid=11027(bandit27) groups=11026(bandit26)
bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27 
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
bandit26@bandit:~$ 
```
Got the Password 
## Password
```Password
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
```
