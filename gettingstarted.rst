===============
Getting started
===============

.. highlight:: bash

Using |p4d| can be roughly divided into four main areas:

- Designing a corpus
- Building a corpus from DjVu materials
- Serving a corpus locally or over the Internet
- Browsing a corpus served on the local or on a remote host

The following sections contain tips to get you started as quickly as possible and describe the prerequisites for all the scenarios. 
More detailed instructions are provided in other chapters of the documentation.


Designing corpora
=================

The design phase consists of specifying the tagset and the metadata for the corpus, as well as preparing the source DjVu material.
This step does not require any special tools, but a general understanding of Poliqarp is needed.

.. seealso::

   :doc:`designing`

   `Man pages <http://poliqarp.sourceforge.net/man/bpng.html>`_ for :samp:`bpng`

   Daniel Janus's `MSc thesis <http://nlp.ipipan.waw.pl/~adamp/msc/janus.daniel/praca.pdf.gz>`_ (in Polish, contains valuable information about Poliqarp internals)

Building corpora
================

To create corpora, you need a set of tools installed on your system: Poliqarp, DjVuLibre, and Jakub Wilk's scripts (distributed as part of Marasca).

.. _getting-started-installing-poliqarp:

Installing Poliqarp
-------------------

Poliqarp is available from `this <http://sourceforge.net/projects/poliqarp/>`_ website.
If you're running a Debian-based Linux distribution, you can download and install one of the binary packages.
Otherwise, download the source package and perform the following steps to compile Poliqarp and its corpus building utilities:

::

   tar xzf poliqarp_X.Y.ZZ.tar.gz
   cd poliqarp_X.Y.ZZ
   ./configure
   make

At this point, you can use :samp:`make install` to install both the corpus building tools, and the Poliqarp server and client.

If you only plan to create corpora and not query them on your local machine, you can just copy the :program:`bpng` binary from :samp:`poliqarp_X.Y.ZZ/bin` to a directory in your :samp:`PATH`. However, it's still recommended to run a local Poliqarp server to be able to query the new corpus for validation and debugging purposes.

Installing DjVuLibre
--------------------

`DjVuLibre <http://djvu.sourceforge.net/>`_ is a library and a set of utilities built around it for manipulating DjVu files.
If you're building a corpus from DjVu files, you're very likely to need some of these utilities.
DjVuLibre is packaged in all popular Linux distributions, so use your package manager to find and install it.
In general, there should be no need to compile DjVuLibre on your own.

Installing pdf2djvu and ocrodjvu
--------------------------------

pdf2djvu is a converter for creating DjVu files from PDF documents. It's available `here <https://code.google.com/p/pdf2djvu/>`_.

ocrodjvu is a utility for performing optical character recognition (OCR) on DjVu documents. It can be downloaded from the author's `website <http://jwilk.net/software/ocrodjvu>`_.



Installing additional scripts
-----------------------------

To create corpora from DjVu, some additional scripts are needed which are part of Marasca (a web interface to Poliqarp which supports displaying results in DjVu files).
The easiest way to obtain the scripts is to clone the source repository.
Make sure you have Mercurial installed on your machine, and follow the steps below:

#. Visit Marasca's `repository <https://bitbucket.org/jwilk/marasca-wbl>`_.
#. Click the :program:`Clone` button. This will display a clone command for Mercurial.
#. Paste the command into a terminal and execute it. This will copy the content of the repository to your local machine.
#. Finally, use this command to update the cloned repository to the correct branch: :samp:`hg update wbl`.

If you're not planning to serve corpora over the Internet, you can ignore most of the contents of Marasca's repository and only copy the files from :samp:`marasca-wbl/misc` into a directory in your :samp:`PATH` (make sure to make them executable).

.. seealso::

   :doc:`building`

   :doc:`building2`


Serving corpora
===============

If you want to serve your corpora locally or over the network, you need to install the Poliqarp daemon (:program:`poliqarpd`). 
Additionally, if you want create a Web interface to your corpora, you need to install and set up Marasca.

Installing the Poliqarp daemon
------------------------------

To install the Poliqarp daemon, follow the instructions in :ref:`getting-started-installing-poliqarp` and use :samp:`make install` after compilation.

If you plan to access the daemon remotely, make sure your machine is not blocking connections to Poliqarp's port (4567 by default).

.. note:: 

   If you perform corpus development activities on the same machine which is used for serving your corpora, it's useful to run two separate Poliqarp daemons: one for remote users, and one for the local user/administrator.

Prerequisites for installing Marasca
------------------------------------

Installing and configuring Marasca is described in detail in the following chapters.
To prepare your machine for running Marasca, install the following dependencies from your distribution's package repository (the names below correspond to Debian/Ubuntu packages, they might be different in your distribution):

- apache2
- django-localeurl
- libapache2-mod-wsgi
- pycairo
- python
- python-django
- python-djvulibre

.. seealso::

   :doc:`serving`


Browsing corpora
================

Accessing DjVu corpora is possible either through a Web interface provided by Marasca, or through a standalone client (DjView for Poliqarp).

To access the Web interface, all you need is a modern Web browser with a DjVu plugin, such as the one provided with DjView4.

If you rather use a client application, download and install |d4p| from `its website <https://bitbucket.org/mrudolf/djview-poliqarp>`_.

DjVu corpora can also be accessed using the standard Poliqarp GUI.
In this case, it's not possible to display graphical results. On the other hand, statistical queries can be used (they are blocked in Marasca to prevent choking the server with computationally intensive requests by malicious users).


.. seealso::

   :doc:`browsing`

.. |p4d| replace:: Poliqarp for DjVu
.. |d4p| replace:: DjView for Poliqarp
