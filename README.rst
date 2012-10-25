Termite is Copyright 2005-2008 by Guillaume Germain
(guillaume.germain@gmail.com),  All Rights Reserved.

Termite is released under the same license as Gambit itself,
see the LICENSE file.

* See the 'INSTALL' file for installation instructions. *

The current code should be considered as beta quality.  That is,
some of it isn't yet implemented using a very good style, might
contains some bug, will probably change in the near future, and the
interaction with Gambit might be a little 'rough' for now.

Don't hesitate to bug me (guillaume.germain@gmail.com) if something
doesn't work as it should, if you have questions or if you have
feature requests.

--------------------------------------------------------

Here is some incomplete documentation about the system.


Some Notes
----------

See "examples/start1.sh" for a minimal Termite program.

The global environment should be the same on every node, because it
isn't included in the serialization of ojects.

One should avoid to make references to unserializable objects in
closures and continuations, else things will fail.

The programs should not use mutations.  Instead, rely on the fact that
passing messages around /is/ a representation of mutable state.  See
"examples/cons.scm" for an example.  Still, mutable data structures
can be hidden behind processes with some care.  Have a look at
'data.scm' for examples.

To stay in the "spirit" of Termite, one should not use SET!, SET-CAR!,
SET-CDR!, VECTOR-SET!, STRING-SET! and similar functions.  Better
integration in the future with Gambit might prevent those forms and
functions from being available.


Datatypes
---------

::

  NODE -> node ID
  (make-node ip-address tcp-port#)

  TAG -> universally unique identifier
  (make-tag)


Functions and special forms
---------------------------

.. code:: lisp

  (node-init node)

Necessary to initialize the system.

.. code:: lisp

  (spawn thunk)

Start a new process executing the 'body' code and return its PID.

.. code:: lisp

  (spawn-link thunk)

Start a new process executing the 'body' code and linking that process
to the current one and return its PID.

.. code:: lisp

  (remote-spawn node thunk)

Spawn a new thunk on a remote node and return its PID.

.. code:: lisp

  (self)

Get the PID of the running process.

.. code:: lisp

  (current-node)

Get the current node we're executing on.

.. code:: lisp

  (! pid message)

Send message to process.

.. code:: lisp

  (? [timeout [default-value]])

Receive a message, block for 'timeout' seconds if no messages.  An
exception will be raised if no default-value is specified.

.. code:: lisp

  (?? pred? [timeout [default-value]])

Receive a message for which (pred? message) is true.

.. code:: lisp

  (recv
    (pattern                . code)
    (pattern (where clause) . code)
    (after   seconds        . code))

Selectively receive a message that match a pattern, and destructure
it.  The last clause can optionally be a 'timeout' clause, with code
to execute if no messages received after a certain amount of time.

.. code:: lisp

  (!? pid message [timeout [default-value]])

Remote procedure call (or synchronous message).  This requires
doing something like:

.. code:: lisp

  (recv
    ...
    ((from token message) (! from (list token reply)))
    ...)

.. code:: lisp

  (shutdown!)

Nicely terminate the execution of the current process.

.. code:: lisp

  (terminate! pid)

Forcefully terminate the execution of a local process.
