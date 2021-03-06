.. include:: <s5defs.txt>

Python & C
==========

:Authors: Felipe Cruz
:Date:    $Date: 2012-11-06 11:08:00 +0000 (Thu, 12 Nov 2012) $

Quem?
-----

.. container:: handout

    Apresentação Pessoal

.. class:: incremental

* Trabalho há 3/4 anos com Python.
* Já contribuí em Projetos OpenSource com código Python e C

* twitter: @felipejcruz
* github: https://github.com/felipecruz


Quem?
-----

.. container:: center, huge

    .. image:: assets/loogica_logo.svg
        :height: 250px
        :width: 600 px

.. container:: center, huge

    .. image:: assets/dekode_logo.svg
        :height: 250px
        :width: 600 px

Motivação
---------

.. container:: handout

    Explicar motivação

C ainda é uma linguagem muito usada e indiscutivelmente rápida.

Python é uma linguagem é muito usada, flexível e produtiva.

Como unir os 2 mundos?

Opções
------

Suportadas Oficialmente

* Python C Extensions
* Ctypes

Terceiros

* Cython
* Cffi

Outras Opções
-------------

* Pyrex
* (R)Python

Gerador de binding
~~~~~~~~~~~~~~~~~~

* PyAutoC
* SWIG


Python C Extensions - Prático
-----------------------------

* http://docs.python.org/3.3/extending/extending.html

* C usando a API C/Pyhton.
* Exige compilação.
* Debug mais complexo.
* Compatível com PyPy através do Cpyext (não recomendado).


C - Struct de Módulo
--------------------

.. sourcecode:: c

    static struct PyModuleDef moduledef = {
        PyModuleDef_HEAD_INIT,
        "pyaio",                            /* m_name      */
        "Python POSIX aio (aio.h) bindings",/* m_doc       */
        -1,                                 /* m_size      */
        PyaioMethods,                       /* m_methods   */
        NULL,                               /* m_reload    */
        NULL,                               /* m_traverse  */
        NULL,                               /* m_clear     */
        NULL,                               /* m_free      */
    };


\+ C - Definição de funções do módulo
-------------------------------------

.. sourcecode:: c

    static PyMethodDef PyaioMethods[] = {

            { "aio_read", pyaio_read,
            METH_VARARGS, pyaio_read_doc },

            { "aio_write", pyaio_write,
            METH_VARARGS, pyaio_write_doc },

            { NULL, NULL, 0, NULL }
    };



\+ C - Inicialização do módulo
------------------------------

.. sourcecode:: c

    PyObject *
    init_pyaio(void)
    {
        PyObject *m;
        PyObject *__version__;

        __version__ = PyUnicode_FromFormat("%s",
                                           PYAIO_VERSION);

        if (!__version__) {
            return NULL;
        }

        ...


\+ C - Inicialização do módulo
------------------------------

.. class:: huge, center

    Isso é apenas uns 15% do necessário.

C Extensions - Observações
--------------------------

* Permite desenvolver módulos inteiros em C, não somente integrar libs em C.
* Tem muitos detalhes e certa complexidade.
* Bom desempenho.
* Pra testar é necessário compilar e instalar.
* Exige mais tempo e mais cuidado.

Ctypes
------

* Permite apenas chamar código em de bibliotecas escritas em C.
* Python puro.
* API relativamente simples.
* Dependendo do uso, performance pode ser um problema.

Ctypes
------

.. sourcecode:: python

    from ctypes import *
    from ctypes.util import find_library


Ctypes
------

.. sourcecode:: python

    from ctypes import *
    from ctypes.util import find_library

    # achar uma biblioteca
    zmq = CDLL(find_library("zmq"), use_errno=True)

Ctypes
------

Descrição dos métodos:

.. sourcecode:: python

    zmq.zmq_init.restype = c_void_p
    zmq.zmq_init.argtypes = [c_int]


Ctypes
------

Descrição dos métodos:

.. sourcecode:: python

    zmq.zmq_init.restype = c_void_p
    zmq.zmq_init.argtypes = [c_int]

    # chamada do método definido
    ctx = zmq.zmq_init(1)

\+ Ctypes
---------

.. sourcecode:: python

    zmq.zmq_socket.restype = c_int
    zmq.zmq_socket.argtypes = [c_void_p, c_int]

\+ Ctypes
---------

.. sourcecode:: python

    zmq.zmq_socket.restype = c_int
    zmq.zmq_socket.argtypes = [c_void_p, c_int]

    # chamada de zmq_socket
    socket = zmq.zmq_socket(ctx, c_int(1))

Ctypes - Performance
--------------------

* Para entregar uma execução pesada para um código eficiente em C ou
  se foram poucas trocas do contexto Python para o contexto C, performance
  não é problema.
* Se exigir muitas "trocas de contexto", entre Python e C, performance
  pode ser um problema porque essa troca tem um custo.

Ctypes - Observações
--------------------

* Prático para integrar com código que já existe.
* Fácil de causar SEGFAULT no interpretador.

.. sourcecode:: python

    from ctypes import c_char_p; c_char_p(1).value

* Compatível com PyPy (embora não seja a melhor opção).
* Python puro, testes não dependem de compilação/instalação.

Cython - Prático
----------------

* Linguagem que mistura C e Python ou Python "anotado".
* Performance equivalente a extensões nativas(C-Extensions).
* Depende de compilação.
* Debug baseado no GDB (em código C).
* Diversos níveis de uso e complexidade. (o que é bom!)

Cython - Python Puro
--------------------

.. class:: big

.. sourcecode:: python

    x = cython.declare(cython.int)

Cython - Python Puro
--------------------

.. class:: big

.. sourcecode:: python

    @cython.locals(a=cython.double,
                   b=cython.double)
    def foo(a, b):
        ...

Cython - Python Puro
--------------------

.. class:: big

.. sourcecode:: python

    cython.declare(x=cython.int,
                   x_ptr=cython.p_int)
    x_ptr = cython.address(x)

Cython - Python & C
-------------------

.. class:: huge

.. sourcecode:: cython

    def soma(a, b):
        return a + b

Cython - Python & C
-------------------

.. class:: huge

.. sourcecode:: cython

    cdef int soma(int a, int b):
        return a + b

Cython - Compilação
-------------------

.. class:: huge

.. sourcecode:: bash

    $ cython arquivo.pyx

Depois, ou compila "na mão" ou usa setuptools/distribute para
compilar e distribuir.


Cython - Resumo
---------------

* Boa opção para escrever extensões nativas poupando trabalho braçal.
* É possível começar só com python puro.
* Compatibilidade com PyPy em andamento.

CFFI - Prático
--------------

* Para escrever bindings*.
* Baseado na API LuaJIT FFI.
* Compatível com PyPy(trunk) e seu código é especializado pelo JIT.
* ABI & API level.

CFFI
----

.. sourcecode:: python

    declarations =
    '''
        void *zmq_init(int io_threads);
        void *zmq_socket(void* context, int type);
    '''

CFFI
----

.. sourcecode:: python

    from cffi import FFI
    ffi = FFI()

    declarations = '''
    void *zmq_init(int io_threads);
    void *zmq_socket(void* context, int type);
    '''

    headers = '#include <zmq.h>'

    ffi.cdef(declarations)
    zmq = ffi.verify(headers)

\+ CFFI
-------

.. sourcecode:: python

    zmq_ctx = zmq.zmq_init(1)
    zmq_socket = zmq.zmq_socket(zmq_ctx, ZMQ_PAIR)

    c_string = ffi.new('char[]', 'uma string')
    zmq_msg_t = ffi.new('zmq_msg_t*')


CFFI - Resumo
-------------

* Usar declarações em C, de um header file (.h) por exemplo.
* API simples
* Existe compilação mas é transparente. Testar é fácil.
* Ótimo desempenho com PyPy (trunk).


(R)Python
---------

.. class:: tiny

.. sourcecode:: python

    from pypy.translator.tool.cbuild import ExternalCompilationInfo
    from pypy.rpython.lltypesystem import rffi

    eci = ExternalCompilationInfo(includes=['zmq.h'],
                                  libraries=['zmq'])

    zmq_init = rffi.llexternal("zmq_init",
                               [rffi.INT],
                               rffi.VOIDP,
                               compilation_info=eci)

    zmq_socket = rffi.llexternal("zmq_socket",
                                 [rffi.VOIDP, rffi.UINT],
                                 rffi.VOIDP,
                                 compilation_info=eci)

\+ (R)Python
------------

.. class:: tiny

.. sourcecode:: python

    def main(argv):
        ctx = zmq_init(1)
        socket = zmq_socket(ctx, 8)
        zmq_connect(socket, "tcp://127.0.0.1:5555")

        #msg = malloc(sizeof(zmsg_t))
        msg = rffi.lltype.malloc(zmsg_t.TO, flavor='raw')

        zmq_msg_init_size(msg, 5)

        #memcpy already available!
        rffi.c_memcpy(zmq_msg_data(msg), "aaaaa", 5)

    def target(*args):
        return main, None


FIM
---

.. class:: huge, center

    Perguntas?
    Obrigado!
