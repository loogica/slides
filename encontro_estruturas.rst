.. include:: <s5defs.txt>

Estruturas de dados em C
========================

:Authors: Felipe Cruz
:Date:    $Date: 2013-06-28 22:10:00 +0000 (Fri, 28 Jun 2013) $


Listas e Arrays
---------------

.. class:: big

* Tipo mais básico
* Contíguo X Não Contíguo


Array
-----

* IMAGEM

Array
-----

.. sourcecode:: c

    typedef struct {
        int fd;
        char *path;
    } client;

    client clients[MAX_CLIENTS];


Array - Características - Boas
------------------------------

.. class:: big

* Acesso constante por índice.
* Eficiente em espaço - apenas dados.
* Localidade de Memória - pode se beneficiar de caches.

Array - Características - Ruins
-------------------------------

* Não pode crescer durante execução
* Ou pode ser aumentado com cópia de elementos


Listas - Single-linked
----------------------

.. sourcecode:: c

    typedef struct list {
        client c;
        struct list *next;
    } list;

    list *clients;


Listas - Doubly-linked
----------------------

.. sourcecode:: c

    typedef struct list {
        client c;
        struct list *next;
        struct list *prev;
    } list;

    list *clients;

Listas - Características - Boas
-------------------------------

* Inserir e Deletar é mais fácil
* Mover ponteiros é mais fácil e rápido que mover itens

Listas - Características - Ruins
--------------------------------

* Espaço extra para os ponteiros.
* Sem acesso randómico eficiente.
* Localidade de memória ruim.

Filas e Pilhas
--------------

* Implementadas com arrays ou listas

Avaliando Operações em Arrays
-----------------------------

* Não Ordenaxo Vs Ordenado

=================  ====   =======            
Search(L, k)       O(n)   O(logn)
-----------------  ----   -------
Insert(L, x)       O(1)   O(n)
-----------------  ----   -------
Delete(L, x)       O(1)   O(n)
-----------------  ----   -------
Successor(L, x)    O(n)   O(1)
-----------------  ----   -------
Predecessor(L, x)  O(n)   O(1)
-----------------  ----   -------
Minimum(L)         O(n)   O(1)
-----------------  ----   -------
Maximum(L)         O(n)   O(1)
=================  ====   =======


Avaliando Operações em Listas
-----------------------------

* Não Ordenaxo Vs Ordenado
* Single Vs Double

================= ==========  ==========  ==========  ===========
Op                single uns  double uns  single sor  double sor
----------------- ----------  ----------  ----------  -----------
Search(L, k)      O(n)        O(n)        O(n)        O(n)
----------------- ----------  ----------  ----------  -----------
Insert(L, x)      O(1)        O(1)        O(n)        O(n)
----------------- ----------  ----------  ----------  -----------
Delete(L, x)      O(n)        O(1)        O(n)        O(1)
----------------- ----------  ----------  ----------  -----------
Successor(L, x)   O(n)        O(n)        O(1)        O(1)
----------------- ----------  ----------  ----------  -----------
Predecessor(L, x) O(n)        O(n)        O(n)        O(1)
================= ==========  ==========  ==========  ===========
