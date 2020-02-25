===============
Getting Started
===============

Lab Components
==============

The following table lists the virtual appliances in the lab along with their networks and credentials to use.

.. list-table::
    :widths: 20 40 40
    :header-rows: 1
    :stub-columns: 1

    * - **System Type**
      - **Networks**
      - **Credentials**

    * - NGINX+
      - Management: 10.1.1.4
        Internal: 10.1.10.11
      - SSH keys
    * - MyApplication Server
      - Management: 10.1.1.8
        Internal: 10.1.10.21
      - None
    * - NGINX Controller
      - Management: 10.1.1.7
      - SSH      


Starting the Lab
================

*Insert instructions here to access UDF*

Using NGINX with Docker
=======================

#. We will start by running an NGINX instance in docker on our Linux host.  To do this type the following commands on the Linux Shell.

   .. code-block:: shell

      docker run -i -t nginx /usr/bin/njs

#. The output will be as follows:

   .. code-block:: none

      interactive njs 0.2.4

      v.<Tab> -> the properties and prototype methods of v.
      type console.help() for more information

#. To test that the docker instance is running type the following command and you will recieve the output below:

   .. code-block:: none

      >> function hi(msg) {console.log(msg)}
      undefined
      >> hi("Hello world")
      'Hello world'
      undefined

#. Now type <CTRL> d to stop the interactive docker instance.

Downloading Lab Files
=====================

#. We will download the lab njs example and conf files from github by running the following command:

   .. code-block:: shell

      git clone https://github.com/xeioex/njs-examples
      cd njs-examples
      ls *

      README.rst
  
      conf:
      complex_redirects.conf  decode_uri.conf  file_io.conf  hello.conf  inject_header.conf  join_subrequests.conf  secure_link_hash.conf
  
      njs:
      complex_redirects.js  decode_uri.js  file_io.js  hello.js  inject_header.js  join_subrequests.js  secure_link_hash.js

