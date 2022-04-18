---
title: "Welcome_Blank UH"
date: 2022-03-01 00:00:00 +0900
layout: article
tag: 
- UserHabit
categories: 
- Development
- PlainText
published: true
---

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