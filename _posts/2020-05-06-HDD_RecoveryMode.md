---
title: "응급 복구와 디스크"
last_modified_at: 2020-05-11T16:20:02-05:00
categories:
  - LINUX
tags:
  - LINUX
  - 응급 복구
  - 하드디스크
toc: true 
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true 
author_profile: true 
read_time: false 
---
---
### 응급 복구
---
**init 정보**  
Single mode는 root로 바로 시작하는 것인데 물리적으로 접근이 되어있어야 가능하다.  
2번도 네트워크가 안되는 상태이기 때문에 로컬 장비까지 접근해야 한다.  
3번이 가장 많이 사용하는 모드(기본)  
4번은 사용하지 X  
5번 x window는 데스크톱에서 부팅할 때 사용한다.  
{: .notice--info}
```
#/etc/inittab
# Default runlevel. The runlevels used by RHS are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
#
```
**복구 방법**  
VM 부팅 시 빠른 시간내 아무 키를 눌러서 다음과 같은 화면으로 넘어온다.  
여기서 위아래 키를 눌러서 운영체제 선택 가능.  
E 키를 누르면 부트로더 설정화면으로 갈 수있다.  
{: .notice}
**표시 정보**  
Root (물리디스크, 파티션)  
커널정보 (커널 이미지)  
리눅스가 부팅 할 때 필요한 초기화 파일 (기본적으로 필요한 프로그램들)  
{: .notice}
커널 이미지 부분을 선택하고 e 키를 눌러서 edit 모드로 간다.  
Quiet 뒤에 모드 명을 칠 것이다. >single 또는 1 을 입력  
수정된 정보를 확인한 후 이대로 b를 눌러서 부팅한다.  
오리지널 쉘 모양으로 부팅이 된다.  
PS1 = “   “ 으로 프롬프트 모양을 바꿔줄 수 있다.  
{: .notice}
**프롬프트에 표시할 정보**  
누가(유저)  
어디에(호스트명)  
위치(경로)  
{: .notice}  
유저@호스트명  PS1=”[\u@\h]”  
유저@호스트명(full name)을 표시하기 위해서는 PS1=”[\u@\H]”  
하지만 아래와 같이 full name을 사용하지 않고 보통 소문자로 표현한다. 
호스트명.도메인명  
www.kjy.co.ki  
ftp.kjy.co.ki  
mail.kjy.co.ki  
ns.kjy.co.ki  
db.kjy.co.ki  
{: .notice}
  
**경로 표시하기**  
유저@호스트명  PS1=”[\u@\h \w] ” 를 입력해주면 생긴다.  
상대 경로는 대문자 W를 사용한다.  
{: .notice}
  
**프롬프트의 표시**  
PS1=”[\u@\h \w]\$ ” 이렇게만 입력하면 $만 표시된다.  
PS1=”[\u@\h \w]\\$ ” 이렇게 입력해줘야 제대로 입력  
\$ -> $ / \\$ -> \$ / ‘\$’ -> \$  로 해석.  
홀 따옴표로 row string 전달한다.  
{: .notice}

Mount 명령어는 리눅스 시스템에 연결된 hd 상태를 보여주는 명령어 이다.
원래는 mount 명령어로 확인 시, ro (read only)상태 즉, 복구 모드로 mount가 되어 있어야 한다.
따라서 패스워드 변경이 안된다. 이럴 땐 mount -o remount,rw / 를 입력하여 rw로 변경을 해주고 나서 passwd 변경이 가능해진다.
#reboot 6 를 입력하여 재부팅 후 다시 로그인을 해보자.
로그인 잘되는 것을 확인 할 수 있다.
{: .notice}

---
### HDD 작업 순서
---
**Linux 환경**  
1. HDD를 M/B에 연결한다. 
2. 파티션을 생성한다. fdisk  
3. 파일시스템을 생성한다. mkfs  
4. 마운트한다. mount  
※Linux FileSystem: ext1, ext2, ext3, ext4, xfs
{: .notice}
**Windows 환경**  
1. HDD를 M/B에 연결한다.  
2. 파티션을 생성한다. 디스크매니저, diskpart, diskmgmt.msc  
3. 포맷한다. format  
4. 드라이브 문자를 할당한다.  
{: .notice}
---
### mount 사용법
---
mount  -o  옵션  -t  파일시스템  장치명  마운트포인트(경로)  
옵션: rw, ro, setuid, nosetuid, user, nouser, exec, noexec  
defaults(rw,setuid,nouser,exec,기본값)  
옵션을 생략한 경우 defaults가 동작한다.  
{: .notice}
파일시스템: ext2, ext3, ext4, xfs, fat, ntfs, iso9660  
auto(기본값)  
파일시스템을 생략한 경우 auto로 동작한다.  
{: .notice}
/etc/fstab : 자동 마운트 설정파일(부팅시 자동으로 마운트할 장치들을 등록)  
{: .notice} 
mount  -o  defaults  -t  ext3  /dev/sdb1  /mydata  
```
장치명         마운트포인트   파일시스템   옵션      덤프 순서
레이블명
UUID명
/dev/sdb1      /mydata        ext3         deafults     1 2
LABEL=/mydata  /mydata        ext3         deafults     1 2
UUID=""990ad1a7-.."  /mydata  ext3         deafults     1 2
```
장치명: 장치의 연결된 순서가 바뀌면 장치명도 바뀌는 문제점이 있다.  
레이블명: 레이블명은 중복되는 문제점이 있다.  
UUID명: 네트워크상에 유일한 이름으로 부여되기 때문에 어디서  
어느 컴퓨터와 연결되어도 같은 이름은 사용할 수 있다.  
장치명, 레이블명, UUID를 확인할 수 있는 명령어는 'blkid' 이다.  
{: .notice} 
덤프는 부팅시 fsck 후 마운트를 진행하는 옵션으로 ext3 이상에서 주로 사용한다.  
순서는 fsck 진행하는 장치만 설정하고, / 에 마운트되는 장치만 순서를 1로 설정한다. 나머지 모두 2로 설정한다.  
{: .notice} 
---
#### 자세한 내용은 다음 포스트에..
---
```console
[root@ns1 ~]# mount | grep sdc

[root@ns1 ~]# df -h /dev/sdc1

[root@ns1 ~]# blkid

[root@ns1 ~]# mount -o remount /mydata2
```



























