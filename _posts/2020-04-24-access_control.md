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
```console
[root@ns1 test]# chmod 666 file1
[root@ns1 test]# chmod 664 file2
[root@ns1 test]# chmod 662 file3
```
* user1을 생성하여 파일에 접근
	* vi file1 가능
	* cat file1 가능
	* vi file2 가능
	* cat file2 가능
	* vi file3 가능
	* cat file3	거부
{: .notice}
```console
[root@ns1 test]# mkdir dir1 dir2 dir3
[root@ns1 test]# touch dir1/file1
[root@ns1 test]# touch dir2/file2
[root@ns1 test]# chmod 777 dir1
[root@ns1 test]# chmod 666 dir1/file1
[root@ns1 test]# chmod 775 dir2
[root@ns1 test]# chmod 666 dir2/file2
[root@ns1 test]# chmod 773 dir3
```
* user1로 dir1에 접근시
	* cd dir1
	* ls -lh
	* touch file11
	* mkdir 11
	* rm file11
	* rm -r dir11
	* rm file1
* user1로 dir2 접근시
	* cd dir2
	* ls -lhd
	* cat >> file2
	* cat file2
	* **rm file2 거부됨** 파일 수정 권한은 있는데 파일을 지울 수는 없다. 왜냐면 파일을 담고 있는 디렉토리에 대한 권한이 없기 때문이다
{: .notice}
다른 예로 파일에 대한 rwx 권한이 전부 없지만 담고 있는 디렉토리에 대한 w 권한이 있을 경우 파일 삭제가 가능하다.  
파일 삭제는 파일 권한이 아니라 디렉토리 권한과 관련이 있다.  
디렉토리의 read 권한이 없으면 ls 명령어를 사용할 수 없다.
디렉토리의 execute 권한이 있으면 cd를 사용하여 이동이 가능하다. 
{: .notice--warning}






















