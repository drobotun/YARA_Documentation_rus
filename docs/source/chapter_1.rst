.. _label_chapter_1:

Начало работы
=============

YARA - это мультиплатформенная программа, работающая в операционных системах Windows, Linux и Mac OS X. Вы можете найти последнюю версию YARA по `адресу <https://github.com/VirusTotal/yara/releases>`_.

Компиляция и установка YARA
"""""""""""""""""""""""""""

Загрузите исходный архив и подготовьтесь к его компиляции:

.. code-block:: bash

	tar -zxf yara-3.8.1.tar.gz
	cd yara-3.8.1
	./bootstrap.sh

Убедитесь, что в вашей системе установлены ``automake``, ``libtool``, ``make`` и ``gcc``. Пользователи Ubuntu и Debian могут использовать:

.. code-block:: bash

	sudo apt-get install automake libtool make gcc

Если вы планируете изменить исходный код YARA, вам также могут понадобиться ``flex`` и ``bison`` для генерации лексеров и парсеров:

.. code-block:: bash

	sudo apt-get install flex bison

Скомпилируйте и установите YARA стандартным способом:

.. code-block:: bash

	./configure
	make
	sudo make install

Запустите проверку, чтобы убедиться, что все нормально:

.. code-block:: bash

	make check

Некоторые функции YARA зависят от библиотеки OpenSSL. Эти функции будут включены, только если в системе установлена библиотека OpenSSL. Если данная библиотека отсутствует, YARA будет работать нормально, но вы не сможете использовать отключенные функции.

Сценарий ``configure`` автоматически определит, установлен OpenSSL или нет. Если вы хотите применить зависимые функции OpenSSL, вы должны передать ``--with-crypto`` скрипту ``configure``. Пользователи Ubuntu и Debian могут использовать ``sudo apt-get install libssl-dev`` для установки библиотеки OpenSSL.

По умолчанию в YARA не компилируются следующие модули:

- cuckoo
- magic
- dotnet

Если вы планируете использовать эти модули, вы должны передать соответствующие аргументы ``--enable-<module name>``  в скрипт ``configure``.

Например:

.. code-block:: bash

	./configure --enable-cuckoo
	./configure --enable-magic
	./configure --enable-dotnet
	./configure --enable-cuckoo --enable-magic --enable-dotnet

Модули обычно зависят от внешних библиотек и, в зависимости от модулей, которые вы решите установить, вам могут понадобиться следующие библиотеки:

- **cuckoo:** Зависит от библиотеки `Jansson <http://www.digip.org/jansson/>`_ для разбора JSON-строк. Некоторые версии Ubuntu и Debian уже включают пакет с именем ``libjansson-dev``, однако если ``sudo apt-get install libjansson-dev`` у вас не работает, то можно загрузить исходный код из `этого репозитория <https://github.com/akheron/jansson>`_.
- **magic:** Зависит от библиотеки ``libmagic``, используемой стандартным программным `файлом <https://en.wikipedia.org/wiki/File_(command)>`_ Unix. Операционные системы Ubuntu, Debian и CentOS обычно включают в себя пакет ``libmagic-dev``. Исходный код можно найти `здесь <ftp://ftp.astron.com/pub/file/>`_.

Установка в Windows
'''''''''''''''''''

Скомпилированные двоичные файлы для Windows в 32 и 64-разрядных вариантах можно найти по `ссылке <https://www.dropbox.com/sh/umip8ndplytwzj1/AADdLRsrpJL1CM1vPVAxc5JZa?dl=0>`_. Просто скачайте нужную версию, распакуйте архив и сохраните файлы ``yara.exe`` и ``yarac.exe`` в нужном месте на диске.

Для установки расширения ``yara-python`` загрузите и выполните установщик, соответствующий используемой версии Python.


Установка в Mac OS X с Homebrew
'''''''''''''''''''''''''''''''

Чтобы установить YARA с помощью `Homebrew <https://brew.sh/>`_, просто введите ``brew install yara``.

Установка yara-python
'''''''''''''''''''''

Если вы планируете использовать YARA из ваших скриптов Python, вам необходимо установить расширение ``yara-python``. Это расширение можно найти обратившись к https://github.com/VirusTotal/yara-python.

Запуск YARA первый раз
""""""""""""""""""""""

Теперь, когда вы установили YARA, вы можете написать очень простое правило и использовать инструмент командной строки для сканирования некоторых файлов:

.. code-block:: bash

	echo "rule dummy { condition: true }" > my_first_rule
	yara my_first_rule my_first_rule

Пусть вас не удивляет повторяющиеся ``my_first_rule`` в аргументах YARA, в данном случае для сканирования передается тот же файл, что и файл с правилом. Вы можете передать любой файл, который хотите проверить (второй аргумент).

Если все пойдет хорошо, вы должны получить следующий результат:

.. code-block:: bash

	dummy my_first_rule

Это означает, что файл ``my_first_rule`` соответствует правилу с именем ``dummy``.

Если вы получаете ошибку такого вида:

.. code-block:: bash

	yara: error while loading shared libraries: libyara.so.2: cannot open shared object file: No such file or directory

Это означает, что загрузчик не находит библиотеку ``libyara``, которая находится в ``/usr/local/lib``. В некоторых вариантах Linux загрузчик по умолчанию не ищет библиотеки по этому пути, поэтому мы должны указать ему сделать это, добавив ``/usr/local/lib`` в файл конфигурации загрузчика ``/etc/ld.so.conf``:

.. code-block:: bash

	sudo sh -c 'echo "/usr/local/lib" >> /etc/ld.so.conf'
	sudo ldconfig
