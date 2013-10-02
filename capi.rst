.. include:: <s5defs.txt>

Python C-API
============

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

* Motivação
* Linhas Gerais
* Funções & Exemplos
* Detalhes Finais
* Alternativas
* Conclusões

Motivação
---------

* Integrar com código C já existente
* Desempenho
* Aprender sobre o Interpretador


Linhas Gerais
-------------

* Declarar Função que inicializa o módulo
* Declarar membros do módulo
* Declara o Módulo
* Declarar classes




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
    for ev in events: pass

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
* Executar W num pool de threads

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
* run_forever(), run_until_complete()
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


Futures
-------

* Abstração de uma computação que ainda não foi feita
* f.set_result()
* f.add_done_callback()
* futures.wait([f], [timeout, [flags]])


Tulip

Futures + Coroutines


.. class:: medium
.. sourcecode:: python

    import tulip
    from tulip.futures import Future
    from tulip.unix_events import SelectorEventLoop
    from concurrent.futures import ThreadPoolExecutor

    loop = SelectorEventLoop()
    f = Future(loop=loop)

    def run_thread_loop():
        import os
        print('XXXXX')
        tasks.wait([f], loop=self)
        print('BBBBB')


    def run_briefly(loop):
        @tulip.coroutine
        def once():
            pass
        t = tulip.Task(once(), loop=loop)
        loop.run_until_complete(t)

    executor = ThreadPoolExecutor(max_workers=2)
    fut = executor.submit(run_thread_loop)

    f.set_result('HA!')
    run_briefly(loop)



    def my_func():
        result = yield from f
        print(result)
        return result

    def fixture():
        yield 'A'
        x = yield from f
        yield 'B', x
        y = yield from f
        yield 'C', y

    g = fixture()
    assert next(g) == 'A'  # yield 'A'.
    assert next(g) == f  # First yield from f.
    f.set_result(42)
    assert next(g) == ('B', 42)  # yield 'B', x.
    # The second "yield from f" does not yield f.
    assert next(g) == ('C', 42)  # yield 'C', y.
