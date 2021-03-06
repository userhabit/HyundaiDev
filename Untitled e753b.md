# Untitled

Created: April 18, 2022 1:51 PM

## mongodb 설치

### mongodb-org-5.0.5-1.el8.x86_64.rpm 설치

아래처럼 실행하면 된다. `rpm -Uvh` 를 `dnf install` 대신 사용해도 된다.

참고: [https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#install-the-mongodb-packages](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#install-the-mongodb-packages)

```
dnf install -y mongodb-org-server-5.0.5-1.el8.x86_64.rpm \\
&& dnf install -y mongodb-org-mongos-5.0.5-1.el8.x86_64.rpm \\
&& dnf install -y mongodb-org-database-tools-extra-5.0.5-1.el8.x86_64.rpm \\
&& dnf install -y mongodb-org-shell-5.0.5-1.el8.x86_64.rpm

dnf install -y mongodb-org-database-5.0.5-1.el8.x86_64.rpm \\
&& dnf install -y mongodb-mongosh-1.1.7.el8.x86_64.rpm

dnf install -y mongodb-database-tools-100.5.1.x86_64.rpm \\
&& dnf install -y mongodb-org-tools-5.0.5-1.el8.x86_64.rpm \\
&& dnf install -y mongodb-org-5.0.5-1.el8.x86_64.rpm

```

### 그외 package 들

그외 package 들은 이미 설치가 되어 있다. `rpm -qa` 로 확인만 해보면 된다.

```
rpm -qa | grep 'chkconfig-\\|cyrus-sasl-\\|openssl-\\|platform-python-\\|python3'

```

```
chkconfig-1.19.1-1.el8.x86_64.rpm
cyrus-sasl-2.1.27-5.el8.x86_64.rpm
cyrus-sasl-gssapi-2.1.27-5.el8.x86_64.rpm
cyrus-sasl-plain-2.1.27-5.el8.x86_64.rpm
openssl-1.1.1k-5.el8_5.x86_64.rpm
openssl-libs-1.1.1k-5.el8_5.x86_64.rpm
openssl-pkcs11-0.4.10-2.el8.x86_64.rpm
platform-python-pip-9.0.3-20.el8.noarch.rpm
python36-3.6.8-38.module_el8.5.0+895+a459eca8.x86_64.rpm
python3-pip-9.0.3-20.el8.noarch.rpm
python3-setuptools-39.2.0-6.el8.noarch.rpm

```

### openssl

openssl 은 이미 `1.1.1k-4` 버전이 설치되어 있었다. 큰 이슈가 없을듯 하여, 그냥 뒀다. 만약 update 를 하려면 깔려있는 openssl 을 삭제한후 다시 설치해야 할 듯 하다. `rpm -Uvh` 로는 계속 dependency 가 교차로 걸려서 update 가 안되었다.

아래 command 로 시도해보지는 않았지만, 아래 command 로도 가능할 듯 하다.

```
dnf update --disablerepo=* --allowerasing openssl-1.1.1k-5.el8_5.x86_64.rpm

```

### mongodb 설정

### 절차

1. `/etc/mongod.conf` 수정
2. `/etc/hosts` 에 domain 추가
3. 몽고db 실행: `systemctl start mongod`
4. `/var/log/mongodb/mongod.log` 에 대한 other 의 읽기권한 추가: `chmod o+r /var/log/mongodb/mongod.log`
5. admin 계정 생성
6. auth 활성화 후 재실행
7. user 생성(polaris)

### mongod.conf 수정

```
# 설정에서 사용할 directory 생성

mkdir -p /mceadm/apps/mongo/data
mkdir -p /mceadm/apps/mongo/log
chmod 755 /mceadm/apps
chmod 755 /mceadm/apps/mongo
chown mceadm:hmc /mceadm/apps/mongo
chown mongod:mongod /mceadm/apps/mongo/data
chown mongod:mongod /mceadm/apps/mongo/log

```

`/etc/mongod.conf` 에 다음 사항을 추가하자

"rs0" 대신에 아무런 이름을 써도 상관없다. 이 이름을 `rs.initiate` 를 할 때 사용하게 된다.

```
replication:
   replSetName: "rs0"
net:
   bindIp: localhost,<hostname(s)|ip address(es)>

```

```
# mongod.conf in "uhs1.userhabit.io"

# Where and how to store data.
storage:
  dbPath: /mceadm/apps/mongo/data
  journal:
    enabled: true

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /mceadm/apps/mongo/log/mongod.log

# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1,uhs1.userhabit.io

# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo

# auth - 계정을 먼저 만들고 활성화 해야 한다.
# security:
#  authorization: enabled
#  keyFile: /var/lib/mongo/replica_set_key

replication:
   replSetName: "rs0"

```

### /etc/hosts file 에 domain 추가

이 domain 은 추후에 polaris 서버에서도 사용하게 된다.

```
echo -e "10.227.171.96  uhs1.userhabit.io\\n10.227.171.97  uhs1.userhabit.io\\n10.227.171.98  uhs1.userhabit.io\\n" >> /etc/hosts

```

### `systemctl start mongod`

service(systemctl) 로 실행을 시키려고 root 계정으로 실행했다. root 로 실행해도 `systemctl start mongod` 를 하면 fork 를 해서 mongod 로 실행하게 된다.

- [How to run MongoDB as a non root user in Linux? - Database Administrators Stack Exchange](https://dba.stackexchange.com/questions/132544/how-to-run-mongodb-as-a-non-root-user-in-linux)

systemctl 을 이용하려는 이유는 현대차증권 이 매주 '금요일' 인가 reboot 을 한다고 한다. 그래서 자동으로 실행이 되도록 application 들을 설정해 놔야 한다고 한다.

### 기타

rpm 설치를 하면 다음 2가지는 자동으로 생성된다.

```
-rw-r--r-- 1 root root /usr/lib/systemd/system/mongod.service
-rw-r--r-- 1 root root /etc/mongod.conf

```

`/etc/mongod.conf` 를 보면, 기본 path 로 다음 2가지를 쓰고 있는 것을 알 수 있다.

- dbPath : /var/lib/mongo
- log : /var/log/mongod/mongod.log

```
drwxr-xr-x 2 mongod mongod /var/log/mongod
drwxrw-rw- 1 mongod mongod /var/lib/mongo
srwx------ 1 mongod mongod /tmp/mongodb-27017.sock

```

참고로, `systemctl start mongod` 를 수행하면, `mongod` 계정으로 실행하게 된다.

- mongod version 확인방법: `/usr/bin/mongod --version`

### replicaSet 설정

위의 설정으로 3개의 mongod 를 전부 실행한 후

한 곳에 가서 `mongo` 를 실행한다. `rs.initiate()` 로 replicaSet 설정을 잡아주면 된다. 확인은 `rs.status()`로 하면 된다.

priority 를 따로 안줘서 아무나 primary node 가 된다. 띄우는 순서는 상관없다. 재실행 하면 primary 가 바뀔 수 있다. 다음 테스트들은 잘 됐다.

- 전체를 내리고 다시 start : ok
- 하나를 내린후 상태 : ok

```
$ mongo

...

> rs.initiate( {
       _id : "rs0",
       members: [
          { _id: 0, host: "uhs1.userhabit.io:27017" },
          { _id: 1, host: "uhs2.userhabit.io:27017" },
          { _id: 2, host: "uhs3.userhabit.io:27017" }
       ]
    })
...

> rs.status()

```

### mongodb collection initialization

폴라리스 서버를 처음 실행하면 collection  들을 자동으로 만든다.

그래서 일단 replcaSet 의 primary 가 있는 서버에서 polaris 를 실행했다. ([참고](https://github.com/userhabit/uh-issues/issues/598#issuecomment-1016304501))

primary node 에서 collection  들을 확인하면, 생성이 되어 있다. secondary node 도 확인했는데, 동기화가 잘 되었다.

```
mongo
...
rs0:PRIMARY> use userhabit;
rs0:PRIMARY> show collections;
...
rs0:SECONDARY> rs.secondaryOk();
rs0:SECONDARY> show collections;

```

### rs.secondaryOk()

Secondary node 에서 query 를 할 때는 `rs.secondaryOk()` 를 미리 해줘야 한다. 이 명령은 session 당 한번만 하면 된다.

- [https://stackoverflow.com/questions/8990158/mongodb-replicates-and-error-err-not-master-and-slaveok-false-code#8990428](https://stackoverflow.com/questions/8990158/mongodb-replicates-and-error-err-not-master-and-slaveok-false-code#8990428)