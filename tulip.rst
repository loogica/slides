.. include:: <s5defs.txt>

Tulip - Py3, Loop de Eventos + I/O Assíncrono
=============================================

:Authors: Felipe Cruz
:Date:    $Date: 2013-08-20 23:00:00 +0000 (Tue, 20 Ago 2013) $

Quem?
-----

.. class:: big

* Trabalho em Rede (loogica.com)
* Comunidade (loogica.net)
* (quase) 100% do trabalho atual é livre.
* http://github.com/felipecruz
* @felipejcruz

Agenda
------

.. class:: big

* Polling & Loop de Eventos
* I/O Assíncrono e Não bloqueante
* Coroutines, Futures/Tasks
* I/O Assíncrono sem callbacks - yield from

Polling
-------

* Nós, explicitamente, pedimos pra saber da disponibilidade
  de eventos.

Polling
-------

* "readyness" Model
* Perguntar pro sistema qual status de um file descriptor.
* Módulo: select
* Python 3.4 - Selectors
* Select(POSIX), Poll, Epoll, Kqueue ...

Polling - Etapas
----------------

* Criar objeto poller
* (Opcional) Registar um file descriptor
* Execução do poll()

Polling
-------

* Responsabilidade nossa de continuar invocando.
* Responsabilidade nossa de adicionar/remover eventos.
* Iterar eventos e tratar cada item.


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
    events = select.kevent(server, 
                           select.KQ_FILTER_READ,
                           select.KQ_EV_ADD, 0)
    events = kq.control([events], 0, 0)
    for event in events:
        pass


Loop de Eventos
---------------

* Tem um objeto Poller
* Polling + Loop + Callbacks(*)
* Executar X quando Y
* Executar Z em N msecs
* Executar W num pool de threads

Loop de Eventos
---------------

* Execução entregue ao loop de eventos.
* Callbacks são executados quando determiados eventos acontecem.

Loop Eventos - Tulip
--------------------

* Implementação de um Loop de eventos
* run_forever(), run_until_complete()
* stop()
* call_later(), call_soon()
* add_reader(), add_writer()


I/O Assíncrono & Não Bloqueante
-------------------------------

* I/O Assíncrono: quero ser notificado quando uma tarefa
  terminar - Ex: Pyaio, Windows IOCP
* I/O Não bloqueante: Uma operação *nunca* bloqueia. Se ela
  não puder ser executada na hora que pedimos, 
  retorna um erro (-1) e errno == EAGAIN
* Não são a mesma coisa mas são usados juntos muitas vezes.

I/O Assíncrono
--------------

* Nesse caso, chamamos de "completeness model".

.. sourcecode:: python

    import pyaio
    import os

    def aio_callback(buf, rcode, errno):
        print(buf)

    fd = os.open('/tmp/pyaio.txt', os.O_RDONLY)
    pyaio.aio_read(fd, 10, 20, aio_callback)

I/O Não Bloqueante
------------------


.. sourcecode:: python

    try:
        data = sock.recv(n)
    except (BlockingIOError, InterruptedError):
        # devo tentar ler denovo em algum momento

Coroutines
----------

* coro é suspensa até que alguem chame "send()"

.. sourcecode:: python

    def coro(times):
        while True:
            w = (yield)
            print(w)

    g = coro(5)
    next(g)
    g.send(20)

Coroutines
----------

* coro é suspensa até que alguem chame "send()"
* Parte da mágica do Tulip está nesse detalhe.

Futures
-------

* Abstração de uma computação que ainda não foi feita.
* f.set_result()
* f.add_done_callback()
* futures.wait([f1, f2], [timeout, [flags]])


Sem Yield From
--------------

* Temos que definir um callback para tratar o termino do evento

.. sourcecode:: python

    def read_static_file():
        def read_callback(data, rcode, errno):
            data_to_send = data
            send_data(data_to_send)

        rc = pyaio.aio_read(filename, 0, 
                            4096, read_callback)

Yield From
----------

* Yield from + Futures + Loop de eventos = expressividade

.. sourcecode:: python

    def read_static_file():
        data, _, _ = yield from aio_read(filename, 
                                         file_size=4096)
        rc = yield from send(data)


Yield from Minha Função
-----------------------

* A função deve retornar um Future
* Algúem tem que chamar "set_result(resultado)" da future
* Quando isso acontece, o tulip faz um "coro.send(resultado)" e o método
  que foi suspenso é resumido com aquele resultado

Yield From - Resumo
-------------------

* Uma forma de expressar que algo vai ser feito em outro momento
* Permitir que uma função seja "pausada" em determinado estado
* Integrada com uma forma de resumir o que foi pausado quando
  definimos o resultado da future retornada.

Fim
---

.. class:: huge

* http://github.com/felipecruz
* @felipejcruz
* Perguntas?
