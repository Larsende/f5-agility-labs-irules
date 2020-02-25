===============
Getting Started
===============

Starting the Lab
================

*Insert instructions here to access UDF*

Using NGINX with Docker
============

.. code-block:: shell

  docker run -i -t nginx /usr/bin/njs

.. code-block:: none

  interactive njs 0.2.4

  v.<Tab> -> the properties and prototype methods of v.
  type console.help() for more information

  >> function hi(msg) {console.log(msg)}
  undefined
  >> hi("Hello world")
  'Hello world'
  undefined

Downloading Lab Files
=====================

.. code-block:: shell

  git clone https://github.com/xeioex/njs-examples
  cd njs-examples
  ls *

  README.rst
  
  conf:
  complex_redirects.conf  decode_uri.conf  file_io.conf  hello.conf  inject_header.conf  join_subrequests.conf  secure_link_hash.conf
  
  njs:
  complex_redirects.js  decode_uri.js  file_io.js  hello.js  inject_header.js  join_subrequests.js  secure_link_hash.js

