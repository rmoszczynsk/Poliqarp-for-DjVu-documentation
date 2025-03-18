================
Browsing corpora
================

There are two ways to browse DjVu corpora and benefit from the functionality which allows the user to jump from the concordances to the place of their occurrence in the DjVu file. One is Marasca, which means using a Web-based interface, and the other is |d4p|, which is a client application.

Whichever way you use, it's recommended to familiarize yourself with Poliqarp's `query language <http://poliqarp.wbl.klf.uw.edu.pl/en/help/>`_ to fully realize its potential.


Web interface
=============

The Web interface provided by Marasca is the first interface to |p4d| developed as part of the original project.
It provides all the functionality necessary for browsing DjVu corpora. 
It's main benefit is that it doesn't require anything to be installed on the user's computer - all that is needed is a modern Web browser with JavaScript support.

Usage of the Web interface is fairly straightforward. 
The home page consists of a navigation pane and the main application area.
The navigation pane lists all the available corpora, and allows the user to change the interface language and access the settings.
The settings aren't numerous and should be self-explanatory.

.. note:: 

   You need to have cookies enabled, otherwise the settings won't be saved between sessions.

When any of the corpora links is selected, the main application area will display a short description of the corpus with a query field.
After a query is completed, the results will be displayed in the same area.
Clicking the result's number displays the context information and the metadata.
Clicking the result itself takes the user to the place where it occurs in the original digital document, on the basis of which the corpus was created.
Each result comes with a **Bookmark** link which allows to save the position of the result's occurrence in the digital document.


Client application
==================

The currently recommended way to access DjVu-based corpora is |d4p|, a client application which needs to be installed on your machine.
The application is available for Linux and Windows `here <https://bitbucket.org/mrudolf/djview-poliqarp/downloads>`_.

|d4p| has several advantages over the Web client:

- it has much better support for graphical concordances,
- it's not affected by occasional DjVu browser plugin issues.

On the other hand, it currently doesn't support the bookmarking feature offered by Marasca.

Before using |d4p|, it's necessary to configure the servers it will communicate with to access corpora.
This can be done through :menuselection:`Settings --> Configure... --> Servers`. 

.. note::

   |d4p| does not query Poliqarp servers (i.e. :samp:`poliqarpd`) directly -- it talks to Marasca web applications.

The main window of |d4p| consists of two parts. 
On the left side is the query pane, which allows to:

- select the Poliqarp server to be used;
- select the corpus you want to query;
- enter your query;
- switch between displaying metadata, textual concordances, and graphical concordances.

The right side displays pages of DjVu documents on which a particular query result appears.

In general, the user interface is rather straightforward.
If needed, help is available by pressing :kbd:`F1`.


Default Poliqarp client
=======================

Apart from the daemon, the Poliqarp package comes with two default clients, one command-line and another with a graphical user interface in Java.
None of them offers the ability to display query results in the graphical layer of the source documents. 
However, both can be useful for debugging during the corpus-creation stage.
In addition, they are currently the only interfaces for using statistical queries, so they might be useful for people performing qualitative linguistic research, who are more concerned with linguistic properties of texts than with their graphical appearance.


.. |p4d| replace:: Poliqarp for DjVu
.. |d4p| replace:: DjView for Poliqarp


..  LocalWords:  Poliqarp's menuselection samp poliqarpd kbd DjView
