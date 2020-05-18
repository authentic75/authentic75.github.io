---
title: "LINUX: Permission(권한)"
last_modified_at: 2020-05-13T16:20:02-05:00
categories:
  - Security
tags:
  - LINUX
  - Permission
  - chmod
  - chgrp
toc: true 
toc_label: "Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
---
### Linux 권한(퍼미션)
---
* r(Read,읽기) File : 파일의 내용을 볼 수 있다.
	* cat, head, tail, vi
	* Directory: 디렉토리의 목록을 볼 수 있다.
	* ls
* w(Write,쓰기) File : 파일의 내용을 수정할 수 있다.
	* vi, cat
	* Directory: 디렉토리안에 파일/디렉토리를 생성,제거,수정할 수 있다.
	* cat, cp, mv, rm, mkdir, rmdir
* x(eXecute,실행) File : 파일을 실행할 수 있다.
	* 파일명
	* Directory: 디렉토리안에 들어갈 수 있다.
	* cd
{: .notice--info} 
디렉토리에서 x가 없는 r은 없습니다. 들어갈 수 없는 디렉토리의 내용을 정상적으로 출력되지 않는다.  
디렉토리에 x만 있으면, 내용을 감추고 하위 디렉토리에 들어갈 수 있다.
{: .notice--info}
```console
[root@ns1 ~]# groups
root bin daemon sys adm disk wheel
[root@ns1 ~]# groupadd -g 400 admin
[root@ns1 ~]# gpasswd admin

[root@ns1 ~]# useradd user1
[root@ns1 ~]# group user1
[root@ns1 ~]# gpasswd -a root admin
[root@ns1 ~]# groups
root bin daemon sys adm disk wheel 
```
창을 다시 껐다 켜야 적용된다
{: .notice}
```console
[root@ns1 ~]# groups
admin root bin daemon sys adm disk wheel 
```
```console
[root@ns1 ~]# cat > file1
[root@ns1 ~]# cat > file2
[root@ns1 ~]# chmod 640 file2
[root@ns1 ~]# ls -lh
-rw-r--r-- 1 root admin 4 4월 26 16:38 file1
-rw-r----- 1 root admin 4 4월 26 16:38 file2
```
* root에서 파일 생성시 644
* root에서 디렉토리 생성시 755
* Umask가 0022 이므로 디렉토리(777-022), 파일(666-022) 
{: .notice}
* user1을 생성하여 파일에 접근
	* cat file1 가능
	* cat file2 거부
* user1에 newgrp admin 해서 그룹에 가입 후
	* cat file2 가능
	* vi file2 가능 (읽기는 가능, 수정은 불가능)
{: .notice}
```console
[root@ns1 test]# mkdir dir1 dir2
[root@ns1 test]# touch dir1/file11 dir1/file12
[root@ns1 test]# touch dir2/file21 dir2/file22
[root@ns1 test]# chmod 755 dir1
[root@ns1 test]# chmod 750 dir2
[root@ns1 test]# ls -lh
drw-r-xr-x 1 root admin 4 4월 26 16:38 dir1
drw-r-x--- 1 root admin 4 4월 26 16:38 dir2
```
* user1을 생성하여 파일에 접근
	* ls dir1 가능
	* ls dir2 거부
* user1에 newgrp admin 해서 그룹에 가입 후
	* ls dir2 가능
{: .notice}