# Домашнее задание к занятию "`Ansible.Часть 2`" - `Дунаев Дмитрий`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

**Выполните действия, приложите файлы с плейбуками и вывод выполнения.**

Напишите три плейбука. При написании рекомендуем использовать текстовый редактор с подсветкой синтаксиса YAML.

Плейбуки должны: 

1. Скачать какой-либо архив, создать папку для распаковки и распаковать скаченный архив. Например, можете использовать [официальный сайт](https://kafka.apache.org/downloads) и зеркало Apache Kafka. При этом можно скачать как исходный код, так и бинарные файлы, запакованные в архив — в нашем задании не принципиально.
2. Установить пакет tuned из стандартного репозитория вашей ОС. Запустить его, как демон — конфигурационный файл systemd появится автоматически при установке. Добавить tuned в автозагрузку.
3. Изменить приветствие системы (motd) при входе на любое другое. Пожалуйста, в этом задании используйте переменную для задания приветствия. Переменную можно задавать любым удобным способом.

---
### Решение 1
1. Плейбук 1
``` 
 ---
- hosts: vmsnew
  become: yes
  tasks:
  - name: mkdir if not exists
    ansible.builtin.file:
      path: /home/dusk/tmpdir
      state: directory
  - name: Unarchive a file that needs to be downloaded (added in 2.0)
    ansible.builtin.unarchive:
      src: https://mirror.yandex.ru/libreoffice/libreoffice/stable/7.5.9/deb/x86_64/LibreOffice_7.5.9_Linux_x86-64_deb_langpack_ru.tar.gz
      dest: /home/dusk/tmpdir
      remote_src: yes
```
2. Плейбук 2
```
---
- hosts: vmsnew
  become: yes
  tasks:
  - name: install service
    ansible.builtin.apt:
      name: tuned
      state: latest
      update_cache: yes
  - name: Enable service
    ansible.builtin.systemd:
      name: tuned
      state: started
      enabled: true
```
3. Плейбук 3
```
---
- hosts: vmsnew
  become: yes
  tasks:
  - name: Change motd
    vars:
      motd: Hello, Netology!
    ansible.builtin.lineinfile:
      path: /etc/update-motd.d/10-uname
      regexp: '^(#)?uname -snrvm'
      line: "echo {{ motd }}"
```

Скриншоты:

![Выполнение плейбука 1](https://github.com/duskdemon/sys-29-hw-7.01-2/blob/main/img/7-02-02-ansible-pb1.png)
![Выполнение плейбука 2](https://github.com/duskdemon/sys-29-hw-7.01-2/blob/main/img/7-02-02-ansible-pb2.png)
![Выполнение плейбука 3](https://github.com/duskdemon/sys-29-hw-7.01-2/blob/main/img/7-02-02-ansible-pb3.png)

---

### Задание 2

**Выполните действия, приложите файлы с модифицированным плейбуком и вывод выполнения.** 

Модифицируйте плейбук из пункта 3, задания 1. В качестве приветствия он должен установить IP-адрес и hostname управляемого хоста, пожелание хорошего дня системному администратору. 

---
### Решение 2
Плейбук 4
```
 ---
- hosts: vmsnew
  become: yes
  tasks:
  - name: Change motd to new one
    vars:
      motd: Hello, Netology!
      motto: Have a good day, everything will be allright!
    ansible.builtin.lineinfile:
      path: /etc/update-motd.d/10-uname
      regexp: '{{ motd }}'
      line: "echo IP:{{ ansible_default_ipv4.address }} Host:{{ ansible_hostname }} Message:{{ motto }}"
```

Скриншоты:

![Выполнение плейбука 4](https://github.com/duskdemon/sys-29-hw-7.01-2/blob/main/img/7-02-02-ansible-pb4.png)
![Отображение приветствия в ОС](https://github.com/duskdemon/sys-29-hw-7.01-2/blob/main/img/7-02-02-ansible-pb4_c.png)
---

### Задание 3

**Выполните действия, приложите архив с ролью и вывод выполнения.**

Ознакомьтесь со статьёй [«Ansible - это вам не bash»](https://habr.com/ru/post/494738/), сделайте соответствующие выводы и не используйте модули **shell** или **command** при выполнении задания.

Создайте плейбук, который будет включать в себя одну, созданную вами роль. Роль должна:

1. Установить веб-сервер Apache на управляемые хосты.
2. Сконфигурировать файл index.html c выводом характеристик каждого компьютера как веб-страницу по умолчанию для Apache. Необходимо включить CPU, RAM, величину первого HDD, IP-адрес.
Используйте [Ansible facts](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_vars_facts.html) и [jinja2-template](https://linuxways.net/centos/how-to-use-the-jinja2-template-in-ansible/). Необходимо реализовать handler: перезапуск Apache только в случае изменения файла конфигурации Apache.
4. Открыть порт 80, если необходимо, запустить сервер и добавить его в автозагрузку.
5. Сделать проверку доступности веб-сайта (ответ 200, модуль uri).

В качестве решения:
- предоставьте плейбук, использующий роль;
- разместите архив созданной роли у себя на Google диске и приложите ссылку на роль в своём решении;
- предоставьте скриншоты выполнения плейбука;
- предоставьте скриншот браузера, отображающего сконфигурированный index.html в качестве сайта.
---
### Решение 3
Плейбук 5
```
---
- name: role-dd-072
  hosts: vmsnew
  become: yes
  roles:
    - role-dd-072
```

[Роль в репозитории](https://github.com/duskdemon/sys-29-hw-7.01-2/tree/main/roles/role-dd-072)

Скриншоты:

![Выполнение плейбука 5](https://github.com/duskdemon/sys-29-hw-7.01-2/blob/main/img/7-02-02-ansible-pb5.png)
![Отображение страницы в браузере](https://github.com/duskdemon/sys-29-hw-7.01-2/blob/main/img/7-02-02-ansible-pb5_web.png)