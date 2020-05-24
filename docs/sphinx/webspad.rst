=======
webSPAD
=======
The Lisp file ``webspad.lisp`` is divided into two parts.

The first part adds a few functions to the ``BOOT`` package of FriCAS_
to make the FriCAS_ output contain a number of markers (via the
``$ioHook`` facility) that make the parsing of the output easier.
Furthermore, it contains a function ``interpret-block`` which
interprets a (potentially multiline) block of code.

The second part is the actual interface between the webserver (package
``HUNCHENTOOT``) and FriCAS_ (essentially package ``BOOT``).



In package ``WEBSPAD``, we define the data structures that will be
interchanged by a HTML client and server. Instead of ``defclass`` we
only use `defstruct`_, which is sufficient for our purposes. ::

   (defstruct r
     (stdout    "" :type string)
     (error?    "" :type string)
     (input     "" :type string))

Although FriCAS_ provides several streams for different formats of
output, we have chosen to direct everything to just one stream and
then split into different parts. The reason for this decision was that
a multiline input might actually belong to different commands. which
step the output counter of FriCAS_. Fetching the output of different
formats from different output streams of FriCAS_ would make it
difficult to synchronize it with the step number that only comes on
one output stream (namely the ``$algebraOutputStream``). In addition,
it would be difficult to determine which stream contains the error
message in case an error occurs.

The entry ``error?`` can either the string ``"T"`` or ``"F"``
depending on whether there was an error signalled on the FriCAS_ side.

The function ``webpad-eval`` (which is the main function for the
connection to the Jupyter_ notebook) first saves all FriCAS_ output
streams. Then it creates a new ``string-output-stream`` and tells
FriCAS_ to write any output to this stream. Eventually, it lets
FriCAS_ interpret the input, collects the output from the stream and
put the saved FriCAS_ stream back into their original state.


.. _defstruct: https://lispcookbook.github.io/cl-cookbook/data-structures.html
.. _Hunchentoot: https://edicl.github.io/hunchentoot/
.. _Jupyter: https://jupyter.org/
