.. include:: <s5defs.txt>

Tulip - Python 3 + Loop de Eventos + I/O Assíncrono
===================================================

:Authors: Felipe Cruz
:Date:    $Date: 2013-08-20 23:00:00 +0000 (Tue, 20 Ago 2013) $

Quem?
-----

* Trabalho em Rede (loogica.com)
* Comunidade (loogica.net)
* Pythonista em tempo integral há 4 anos.
* (quase) 100% do trabalho atual é livre.
* http://github.com/felipecruz

Agenda
------

* Polling & Loop de Eventos
* I/O Assíncrono e Não bloqueante
* Python 3 - Coroutines & yield from
* Tulip
* Exemplo

Polling
-------

* Perguntar pro sistema qual status de um file descriptor.
* Módulo: select
* Select(POSIX), Poll, Epoll, Kqueue ...

Polling - Etapas
----------------

1 - Criar objeto poller
2 - (Opcional) Registar um file descriptor
3 - Execução do poll()

Polling - Etapas
----------------

.. class:: medium
.. sourcecode:: python

    import select
    # 1
    kq = select.kqueue()
    # 3
    event = select.kevent(server, select.KQ_FILTER_READ,
                                  select.KQ_EV_ADD, 0)
    events = kq.control([event], 0, 0)

Polling
-------

* Responsabilidade nossa de continuar invocando.
* Responsabilidade nossa de adicionar/remover eventos.
* Iterar eventos e tratar cada item.

Loop de Eventos
---------------

* Polling + Loop + Callbacks
* Executar X quando Y
* Executar Z em N msecs
* Executar

AbstractEventLoop
BaseEventLoop
BaseSelectorEventLoop
SelectorEventLoop

Loop de Eventos
---------------

* Execução entregue ao loop de eventos.
* Callbacks são executados quando determiados eventos acontecem.

Loop Eventos - Tulip
--------------------

* Implementação de um Loop de eventos
* run_forever()
* stop()
* call_later(), call_soon()
* add_reader(), add_writer()





Polling - Etapas
----------------

.. class:: medium
.. sourcecode:: python

    import socket
    import select
    server = socket.socket(socket.AF_INET,
                           socket.SOCK_STREAM, 0)
    server.bind(('127.0.0.1', 8888)); server.listen(10)
    kq = select.kqueue()
    kq.control([select.kevent(server, select.KQ_FILTER_READ, select.KQ_EV_ADD, 0)], 0, 0)
    []
