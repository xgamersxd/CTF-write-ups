Corny Kernel
50 points
misc, systems, beginner
Use our corny little driver to mess with the Linux kernel at runtime!
$ socat file:$(tty),raw,echo=0 tcp:corny-kernel.chal.uiuc.tf:1337
author: Nitya
Files
pwnymodule.c

Открываем файл pwnymodule.c и видим, короткий код модуля для ядра Linux. Из кода ясно, что модуль выводит флаг по частям. Первая часть выводится при инициализации модуля функцией pwny_init(), вторая часть при выгрузке функцией pwny_exit().

Подключаемся к серверу и смотрим содержимое текущей директории. В ней лежит файл pwnymodule.ko.gz 
	$ socat file:$(tty),raw,echo=0 tcp:corny-kernel.chal.uiuc.tf:1337
	$ ls
Гугл говорит, что это сжатый файл модуля ядра. Далее ищем или вспоминаем команды для загрузки, выгрузки модулей:
	$ insmod pwnymodule.ko.gz 
	[ 8.102400] pwnymodule: uiuctf{m4ster_
Модуль загружен, первая часть фалага есть. Далее выгружаем модуль:
	$ rmmod pwnymodule.ko.gz 
И в выводе ничего. Возвращаемся в файл pwnymodule.c  и видим, что в функциях загрузки и выгрузки используются разные способы логирования (pr_alert и pr_info соответственно).
Чтобы получить вторую часть флага, необходимо изменить уровень логирования следующим образом:
	$ cat /proc/sys/kernel/printk
	4 4 1 7
	$ echo 8 > /proc/sys/kernel/printk
Далее загружаем модуль и выгружаем еще один раз:
	$ insmod pwnymodule.ko.gz 
	$ rmmod pwnymodule.ko.gz 
	[ 557.170080] pwnymodule: k3rNE1_haCk3r}
Получаем вторую часть флага. Итог:
	uiuctf{m4ster_k3rNE1_haCk3r}

