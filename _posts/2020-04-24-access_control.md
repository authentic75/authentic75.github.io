---
title: "LINUX: 권한(소유권)과 링크"
last_modified_at: 2020-04-24T16:20:02-05:00
categories:
  - Security
tags:
  - LINUX
  - Permission
  - chmod
  - chgrp
  - symbolic
  - hard
  - link
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
---
### groups, newgrp
---
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
---
### 파일 권한
---
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
---
### 디렉토리 권한
---
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
* 다른 예로 파일에 대한 rwx 권한이 전부 없지만 담고 있는 디렉토리에 대한 w 권한이 있을 경우 파일 삭제가 가능하다.  
* 또한 디렉토리에 대한 w 권한이 있어야 touch가 가능하다.   
* 파일 삭제는 파일 권한이 아니라 디렉토리 권한과 관련이 있다.    
* 디렉토리의 read 권한이 없으면 ls 명령어를 사용할 수 없다.  
* 디렉토리의 execute 권한이 있으면 cd를 사용하여 이동이 가능하다.   
{: .notice--warning}
추가로, 디렉토리에 대한 권한이 r-- 일 경우 ls로 디렉토리를 조회해도 정상적으로 출력이 되지 않는다.  
r와 x가 반드시 같이 설정되어있어야함.  
{: .notice--info}

---
### 실습
---
`mkdir -p 옵션을 사용하면 거쳐가는 path 한번에 생성됨`
```console
[root@ns1 test]# mkdir -p dir1/dir2/dir3
```

```console
[root@ns1 test]# ls
dir1
[root@ns1 test]# chmod 755 dir1
[root@ns1 test]# chmod 750 dir1/dir2/
```

```console
[user1@ns1 test]$ cd dir1
[user1@ns1 dir1]$ pwd
/test/dir1
[user1@ns1 dir1]$ cd dir2
-bash: cd: dir2: 허가 거부됨
```

```console
[root@ns1 test]# chmod 777 dir1/dir2/
dir2/
```

```console
[user1@ns1 dir1]$ cd dir2
[user1@ns1 dir2]$ ls
ls: .: 허가 거부됨
```

```console
[root@ns1 test]# chmod 777 dir1/dir2/dir3/
[root@ns1 test]#
[root@ns1 test]# chmod o+x dir1/dir2
[root@ns1 test]#
```

```console
[user1@ns1 dir1]$
[user1@ns1 dir2]$ cd dir3
[user1@ns1 dir3]$
```
---
### Umask
---
User1은 0002 root 0022가 default 값
{: .notice--warning}
```console
[user1@ns1 test]$ umask
0002
[user1@ns1 test]$ umask -S
u=rwx, g=rwx, o=rx
[user1@ns1 test]$ mkdir dir1
[user1@ns1 test]$ ls -lh
drwxrwxr-x 2 user1 user1 4 4월 26 17:38 dir1
[user1@ns1 test]$ umask 057
[user1@ns1 test]$ umask -S
u=rwx, g=w, o=
[user1@ns1 test]$
```
---
### 파일 소유자 변경
---
```console
[root@ns1 test]# chown user1 sample.txt	//chown 명령어로 파일의 소유자 변경 가능
[root@ns1 test]# chgrp user2 sample.txt	//chgrp 명령어로 파일 소유 그룹 변경가능 
```

`파일 및 디렉토리 생성`

```console
[root@ns1 test]# mkdir dir1
[root@ns1 test]# mkdir dir1/dir10
[root@ns1 test]#
[root@ns1 test]# touch dir1/file10
[root@ns1 test]# touch dir1/
dir10/  file10
[root@ns1 test]# touch dir1/dir10/file100
[root@ns1 test]#
[root@ns1 test]# ls -lh
합계 4.0K
drwxr-xr-x 3 root root 4.0K  4월 21 13:28 dir1
[root@ns1 test]# ls -lh dir1
합계 4.0K
drwxr-xr-x 2 root root 4.0K  4월 21 13:28 dir10
-rw-r--r-- 1 root root    0  4월 21 13:28 file10
```

`chown으로 하위 디렉토리나 파일이 같이 바뀌진 않는다`
```console
[root@ns1 test]# chown user1 dir1
[root@ns1 test]# ls -lh
합계 4.0K
drwxr-xr-x 3 user1 root 4.0K  4월 21 13:28 dir1
[root@ns1 test]# ls -lh dir1
합계 4.0K
drwxr-xr-x 2 root root 4.0K  4월 21 13:28 dir10
-rw-r--r-- 1 root root    0  4월 21 13:28 file10
[root@ns1 test]#
```

`Recursive 옵션으로 하위 까지 변경`
```console
[root@ns1 test]# chown -R user1 dir1
[root@ns1 test]# ls -lh
합계 4.0K
drwxr-xr-x 3 user1 root 4.0K  4월 21 13:28 dir1
[root@ns1 test]# ls -lh dir1
합계 4.0K
drwxr-xr-x 2 user1 root 4.0K  4월 21 13:28 dir10
-rw-r--r-- 1 user1 root    0  4월 21 13:28 file10
[root@ns1 test]#
*chgrp그룹도 마찬가지
[root@ns1 test]# chgrp user2 dir1
[root@ns1 test]# ls -lh
합계 4.0K
drwxr-xr-x 3 user1 user2 4.0K  4월 21 13:28 dir1
[root@ns1 test]#
```

`-R 옵션을 사용하면 모두 바뀐다`
```console
[root@ns1 test]# ls -lh
합계 4.0K
drwxr-xr-x 3 user1 user2 4.0K  4월 21 13:28 dir1
[root@ns1 test]# ls -lh dir1
합계 4.0K
drwxr-xr-x 2 user1 user2 4.0K  4월 21 13:28 dir10
-rw-r--r-- 1 user1 user2    0  4월 21 13:28 file10
[root@ns1 test]#
```

`Chmod도 마찬가지다`
```console
[root@ns1 test]# chmod 777 dir1
[root@ns1 test]# ls -lh
합계 4.0K
drwxrwxrwx 3 user1 user2 4.0K  4월 21 13:28 dir1
[root@ns1 test]# ls -lh dir1
합계 4.0K
drwxr-xr-x 2 user1 user2 4.0K  4월 21 13:28 dir10
-rw-r--r-- 1 user1 user2    0  4월 21 13:28 file10
[root@ns1 test]#
[root@ns1 test]# chmod -R 777 dir1
[root@ns1 test]# ls -lh dir1
합계 4.0K
drwxrwxrwx 2 user1 user2 4.0K  4월 21 13:28 dir10
-rwxrwxrwx 1 user1 user2    0  4월 21 13:28 file10
[root@ns1 test]# ls -lh
합계 4.0K
drwxrwxrwx 3 user1 user2 4.0K  4월 21 13:28 dir1
[root@ns1 test]#
```

`다양한 표현`
```console
[root@ns1 test]# chown root dir1
[root@ns1 test]# !ls
ls -lh
합계 4.0K
drwxrwxrwx 3 root user2 4.0K  4월 21 13:28 dir1
[root@ns1 test]# chgrp root dir1
[root@ns1 test]# !ls
ls -lh
합계 4.0K
drwxrwxrwx 3 root root 4.0K  4월 21 13:28 dir1
[root@ns1 test]# chown user1:user1 dir1 //소유자와 소유그룹을 동시에 바꿈
[root@ns1 test]# !!
chown user1:user1 dir1
[root@ns1 test]# ls -lh
합계 4.0K
drwxrwxrwx 3 user1 user1 4.0K  4월 21 13:28 dir1
[root@ns1 test]#
[root@ns1 test]# chown root.user2 dir1 //소유자와 소유그룹 동시에 바꿈
[root@ns1 test]#
[root@ns1 test]# !ls
ls -lh
합계 4.0K
drwxrwxrwx 3 root user2 4.0K  4월 21 13:28 dir1
```
```console
[root@ns1 test]# chown user1 dir1 ; chgrp user1 dir1 //두명령을 한줄에 실행
[root@ns1 test]#
[root@ns1 test]# ls -lh
합계 4.0K
drwxrwxrwx 3 user1 user1 4.0K  4월 21 13:28 dir1
[root@ns1 test]#
```

---
### 특수 권한
---
* 특수 권한 : SetUID SetGID Stickybit
* SetUID: u+s	--S --- ---
	* File: 파일을 실행할 경우 실행한 사용자의 권한이 아닌 실행파일의 
	* 소유자 권한으로 파일 접근 
* SetGID: g+s	--- --S ---
	* File: 파일을 실행할 경우 실행한 사용자의 권한이 아닌 실행파일의 
	* 소유 그룹 권한으로 파일 접근 	
	* Directory: 해당 디렉토리에 파일이나 디렉토리가 만들어진 경우 
	사용자의 그룹이 아닌 원래 해당 디렉토리의 소유 그룹으로 자동으로 수정 되어 진다.
* Stickybit: o+t	--- --- --T	
	* Directory: 아무나 쓰기 가능한 디렉토리에 타인 소유의 파일을 제거 할 수 있는 권한을 없앤다. 
	따라서 파일 생성자(소유자) 만이 파일이나 디렉토리를 제거할 수 있다. 
	( 실제 Sticky bit 가 들어가잇는 디렉토리는 temp 가 있다 다운로드 후 임시로 압축 풀고 지우고 등등)
{: .notice}

---
#### shadow 파일
---
```console
[root@ns1 test]# tail -n 2 /etc/shadow
user2:!!:18373:0:99999:7::: >> 암호 비활성화 됨
user1:$1$Hq669u7g$6ttJGwQvj/kxdGgIwD29.1:18373:0:99999:7:::
```
`락과 언락 옵션`
```console
[root@ns1 test]# passwd -l user1
Locking password for user user1.
passwd: Success
[root@ns1 test]# tail -n 2 /etc/shadow
user2:!!:18373:0:99999:7:::
user1:!!$1$Hq669u7g$6ttJGwQvj/kxdGgIwD29.1:18373:0:99999:7:::
[root@ns1 test]# passwd -u user1
Unlocking password for user user1.
passwd: Success.
[root@ns1 test]# tail -n 2 /etc/shadow
user2:!!:18373:0:99999:7:::
user1:$1$Hq669u7g$6ttJGwQvj/kxdGgIwD29.1:18373:0:99999:7:::
```
```console
[root@ns1 test]# passwd user2
Changing password for user user2.
New UNIX password:
BAD PASSWORD: it is too short
Retype new UNIX password:
passwd: all authentication tokens updated successfully.
[root@ns1 test]# tail -n 2 /etc/shadow
user2:$1$bWJorwZw$AJsr6HRb8RL2foEYH7bkv.:18373:0:99999:7:::
user1:$1$Hq669u7g$6ttJGwQvj/kxdGgIwD29.1:18373:0:99999:7:::
[root@ns1 test]#
```

`Shadow에서 암호를 확인할 수 있다. 이 파일은 root 조차 읽기만 가능`
```console
[root@ns1 test]# ls -lh /etc/shadow
-r-------- 1 root root 1.3K  4월 21 13:45 /etc/shadow
[root@ns1 test]#
```
하지만 특정 조건 시에 수정 가능하다.  
그것이 바로 passwd 명령어.
{: .notice}
passwd 를 사용하여 암호수정하면 shadow 에서 md5 해쉬가 바뀐것을 볼수 있다  
이러한 것을 setUID 이라고 부른다.
{: .notice}
```console
[root@ns1 test]# cp /bin/cat ./cat1
[root@ns1 test]# cp /bin/cat ./cat2
[root@ns1 test]#
[root@ns1 test]# chmod u+s cat2
[root@ns1 test]#
[root@ns1 test]# ls -lh
합계 48K
-rwxr-xr-x 1 root root 23K  4월 21 13:50 cat1
-rwsr-xr-x 1 root root 23K  4월 21 13:51 cat2 (빨간색)
```

---
####  권한변경
---

```console
[root@ns1 test]# cat > file1
abc
[root@ns1 test]# cat > file2
xyz
[root@ns1 test]# chmod 644 file1
[root@ns1 test]# chmod 600 file2
[root@ns1 test]#
[root@ns1 test]# ls -lh
합계 56K
-rwxr-xr-x 1 root root 23K  4월 21 13:50 cat1
-rwsr-xr-x 1 root root 23K  4월 21 13:51 cat2
-rw-r--r-- 1 root root   4  4월 21 13:51 file1
-rw------- 1 root root   4  4월 21 13:51 file2
```

위 상황에서 일반 계정으로 cat1을 실행가능, cat1으로 file1 read 가능 file2는 불가능   
그러나 cat2를 사용할 시에는 root의 권한을 잠깐 빌려와서 사용하게 된다.
{: .notice}

```console
[root@ns1 test]# ls -lh
합계 56K
-rwxr-xr-x 1 root root 23K  4월 21 14:27 cat1
-rwsr-xr-x 1 root root 23K  4월 21 14:27 cat2
-rw-r--r-- 1 root root   4  4월 21 14:27 file1
-rw-r----- 1 root root   4  4월 21 14:28 file2
```
* user1은 other에 속한다 cat을 통해서 file1 읽기 가능 file2 읽기 불가능
* cat1을 통해서는? other로 접근 file1 읽기가능 file2 읽기 불가능
* cat2를 통해서는? other 의 권한으로 접근 하지만 set uid 의 영향으로 소유자 권한을 가지고 file1에 소유자의 권한으로 접근 file2도 소유자 권한으로 접근
* 위의 같은 경우에는 root 권한으로 실행  
{: .notice}

* cat 명령과 vi 명령어는 set UID가 들어가면 큰일난다.
* 어느 파일이든 읽고 쓸수있게된다.
{: .notice}

---
#### setUID
---

```console
[root@ns1 test]# rm -rf *
[root@ns1 test]# which echo
/bin/echo
[root@ns1 test]# cp /bin/echo ./echo1
[root@ns1 test]# cp /bin/vi ./vi1
[root@ns1 test]# chmod u+s echo1
[root@ns1 test]# chmod u+s vi1
[root@ns1 test]# ls -lh
합계 608K
-rwsr-xr-x 1 root root  20K  4월 21 14:33 echo1
-rwsr-xr-x 1 root root 581K  4월 21 14:34 vi1
```
`vi1 로 passwd 파일을 수정할 수 있게됨`
```console
[user1@ns1 test]$ ./vi1 /etc/passwd
[user1@ns1 test]$ tail -n 3 /etc/passwd
user2:x:500:500::/home/user2:/bin/bash
user1:x:501:501::/home/user1:/bin/bash
test1:x:0:0::/root:/bin/bash
```
`암호를 제거해줌`
```console
[user1@ns1 test]$ ./vi1 /etc/shadow
```
`root에서 확인`
```console
[root@ns1 test]# tail -n 3 /etc/shadow
user2:$1$bWJorwZw$AJsr6HRb8RL2foEYH7bkv.:18373:0:99999:7:::
user1:$1$Hq669u7g$6ttJGwQvj/kxdGgIwD29.1:18373:0:99999:7:::
test1::18373:0:99999:7:::
```
접속 가능해짐 (그렇지만 root 로 들어가짐. 백도어 계정이 만들어져서 시스템에 권한 행사가 가능해짐.  보안 차원에서 Set uid 가 있는 파일을 잘 보고 걸러내야함)
{: .notice}
```console
[user1@ns1 test]$ su - test1
[root@ns1 ~]# [user1@ns1 test]$ ./vi1 /etc/shadow
```
* root가 파일 생성시 항상 644로 생성
* x자리에만 s 치환이 가능하다
* 하지만 x가 원래 있던 파일인지 아닌지 알수 없어서 x 권한이 없으면 대문자 S로 바뀌어서 보이게 된다.
{: .notice}

---
#### setGID 파일
---

```console
[root@ns1 test]# ls -lh
합계 0
-rw-r--r-- 1 root root 0  4월 21 14:42 file1
[root@ns1 test]# chmod u+s file1
[root@ns1 test]# ls -lh
합계 0
-rwSr--r-- 1 root root 0  4월 21 14:42 file1
[root@ns1 test]# chmod u+x file1
[root@ns1 test]# ls -lh
합계 0
-rwsr--r-- 1 root root 0  4월 21 14:42 file1
[root@ns1 test]# chmod u-x file1
[root@ns1 test]# ls -lh
합계 0
-rwSr--r-- 1 root root 0  4월 21 14:42 file1
```
setGID 는 그룹의 x 자리에 s가 들어가고 그룹의 권한으로 실행되게 된다.
{: .notice}
```console
[root@ns1 test]# ls -lh
합계 0
-rwSr--r-- 1 root root 0  4월 21 14:42 file1
[root@ns1 test]# chmod g=rwx file1
[root@ns1 test]# ls -lh
합계 0
-rwSrwxr-- 1 root root 0  4월 21 14:42 file1
[root@ns1 test]# chmod g=rws file1
[root@ns1 test]# ls -lh
합계 0
-rwSrwSr-- 1 root root 0  4월 21 14:42 file1
[root@ns1 test]#
```
```console
[root@ns1 test]# mkdir dir1
[root@ns1 test]# chown user1:user1 dir1
[root@ns1 test]# ls -lh
합계 4.0K
drwxr-xr-x 2 user1 user1 4.0K  4월 21 14:47 dir1

[user1@ns1 test]$ whoami
user1
[user1@ns1 test]$ groups
user1
[user1@ns1 test]$ cd /test/dir1
[user1@ns1 dir1]$ pwd
/test/dir1
[user1@ns1 dir1]$ touch file1
[user1@ns1 dir1]$ mkdir dir1
```

```console
login as: user2
user2@192.168.0.114's password:
[user2@ns1 ~]$ whoami
user2
[user2@ns1 ~]$ groups
user2
[user2@ns1 ~]$ cd /test/dir1
[user2@ns1 dir1]$ pwd
/test/dir1
[user2@ns1 dir1]$ touch file2
touch: cannot touch `file2': 허가 거부됨
[user2@ns1 dir1]$ mkdir dir2
mkdir: `dir2' 디렉토리를 만들 수 없습니다: 허가 거부됨
```

```console
[user2@ns1 dir1]$ mkdir dir1 dir2
[user2@ns1 dir1]$ touch dir2/file20
[user2@ns1 dir1]$ ls -lh
합계 8.0K
drwxrwxr-x 2 user2 user2 4.0K  4월 21 15:01 dir1
drwxrwxr-x 2 user2 user2 4.0K  4월 21 15:01 dir2
```
```console
[user1@ns1 dir1]$ ls -lh
합계 8.0K
drwxrwxr-x 2 user2 user2 4.0K  4월 21 15:01 dir1
drwxrwxr-x 2 user2 user2 4.0K  4월 21 15:01 dir2
[user1@ns1 dir1]$ rmdir dir1
```
rmdir dir2 를 하려면 아래에 있는 file20 을 지워야 하는데 file20은 누구의 권한이 필요한가? 부모인 dir2 를 지울 수 있는 권한이 필요하다 그것은 user2
{: .notice}

---
#### sitcky bit
---

```console
[root@ns1 test]# mkdir dir1 dir2
[root@ns1 test]# chmod 777 dir*
[root@ns1 test]# ls -lh
합계 8.0K
drwxrwxrwx 2 root root 4.0K  4월 21 15:46 dir1
drwxrwxrwx 2 root root 4.0K  4월 21 15:46 dir2
```
둘다 루트 소유 아무나 수정 가능 -> sticky 비트를 줘볼것이다.
{: .notice}
```console
[root@ns1 test]#
[root@ns1 test]# chmod o+t dir2
[root@ns1 test]# ls -lh
합계 8.0K
drwxrwxrwx 2 root root 4.0K  4월 21 15:46 dir1
drwxrwxrwt 2 root root 4.0K  4월 21 15:46 dir2
[root@ns1 test]#
```
user1 user2 둘다 dir1 에 들어온다
{: .notice}
```console
[user1@ns1 test]$ cd dir1
[user1@ns1 dir1]$ ls -lhd
drwxrwxrwx 2 root root 4.0K  4월 21 15:46 .
[user1@ns1 dir1]$ touch file1
[user1@ns1 dir1]$ cat > file1
abc
xyz
[user1@ns1 dir1]$ cat > file2
123
456
[user1@ns1 dir1]$ ls -lh
합계 4.0K
-rw-rw-r-- 1 user1 user1 8  4월 21 15:50 file2
[user1@ns1 dir1]$
```
user가 file2 를 작성하는동안 user 2 가 file1 을 지워버림.  
이런 상황을 대비하기 위해 사용하는것이 sticky bit다   
dir2 에 들어와 보자  
{: .notice}
```console
[user1@ns1 dir1]$ cd ../dir2
[user1@ns1 dir2]$ pwd
/test/dir2
[user1@ns1 dir2]$
[user1@ns1 dir2]$ ls -lhd
drwxrwxrwt 2 root root 4.0K  4월 21 15:46 .
[user1@ns1 dir2]$ cat > file1
abc
xyz
[user1@ns1 dir2]$ cat> file2
123
456
```
```console
[user2@ns1 dir1]$ cd ../dir2
[user2@ns1 dir2]$ pwd
/test/dir2
[user2@ns1 dir2]$ ls -ldh
drwxrwxrwt 2 root root 4.0K  4월 21 15:46 .
[user2@ns1 dir2]$
[user2@ns1 dir2]$ touch file5
[user2@ns1 dir2]$ touch file6
[user2@ns1 dir2]$ rm 5
[user2@ns1 dir2]$ ls
file  file1  file2  file5  file6
[user2@ns1 dir2]$ rm file1
rm: remove write-protected 일반 파일 `file1'? y
rm: cannot remove `file1': 명령이 허용되지 않음
```
오로지 작성자만이 파일을 지울 수 있다.
{: .notice}
---
### 문제
---
whoami  > user2  
groups  > user2 admin wheel  
umask 033 		  				
touch file1	  					
mkdir dir1  
{: .notice}
ls -lh
(1) 2 (3) (4) 4K 날짜 dir1  
(2) 1 (3) (4) 0 날짜 file1  
chmod (5) dir 1  
chmod (6) file 1
{: .notice}   
ls -lh    
drwxrws--- 2 ( 3 ) ( 4 ) 4k 날짜 dir1     
-rwsr-xr-x 1 ( 3 ) ( 4 ) 0 날짜 file 1    
chmod 1777 dir1    
chmod u=rwx,go-w file1 
{: .notice}  
ls -lh  
(7) 2 (3) (4) 4k 날짜 dir1  
(8) 1 (3) (4) 0 날짜 file1  
{: .notice}

* 3번 답은 user2 (whoami 를 보고 알 수 있다)
* 4번 또한 groups 를 보고 user2 임을 알 수 있다
* umask  --- -wx -wx  디렉토리의 경우 제거 하면 1번  drwx r-- r-- 이 된다
* 파일의 경우 x가 빠져서 2번은  -rw- r-- r-- 가 된다
* 5번은 chmod g+s dir1 또는 chmod 2770 dir1, chmod ug=rwx,o=,g+s , chmod g+wxs,o-r 등
* 6번은 chmod 4755, chmod ugo=rx, u+xs , chmod u+xs, go+x
* 7번은 drwxrwxrwt 8번은 -rwxr-xr-x 
{: .notice--danger}








