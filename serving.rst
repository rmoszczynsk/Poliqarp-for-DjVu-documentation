===============
Serving corpora
===============

This chapter describes the ways in which you can serve corpora locally or over the network, so that they can be accessed and queried using various clients.

If you're a regular user and only want to query a corpus, head to the :doc:`browsing` chapter.


Running the Poliqarp daemon
===========================

The main prerequisite for serving any corpus is to install and run the Poliqarp daemon.
The installation process is described in the :doc:`gettingstarted` chapter.

There's no single, mandatory way to run the Poliqarp daemon.
If you only use it occasionally, you can start it manually like this:

::

   poliqarpd -c <path to Poliqarp configuration file>

If, on the other hand, you want to run a proper Poliqarp server, some additional effort will be needed.
First of all, you should create a separate user for running the Poliqarp daemon.
You probably also want to create a startup script and add it to the appropriate run-level in your system, so that you don't have to start the daemon manually after each reboot.

The content of the configuration file is described in more detail in the :doc:`designing` and :doc:`formats` chapters.


Adding corpora to Poliqarp
--------------------------

After you've built a corpus (see the :doc:`building` chapter) and installed the Poliqarp daemon, it's time to add a corpus to it.
Set up a directory for storing corpora files. 
It's possible to keep each corpus in a separate directory, but for the purposes of these instructions we'll take an easier (although a bit more messy) way and assume that all the corpora will be kept in :samp:`/srv/corpora`.
We'll also assume that the Poliqarp daemon configuration file is :samp:`/srv/poliqarp/poliqarp.conf`.

For each corpus that you've built:

1. Copy all its files (the binary corpus files, not the source or intermediate files) to :samp:`/srv/corpora`.

2. Add an appropriate line to :samp:`/srv/poliqarp/poliqarp.conf`, for example:

::

   corpus = newcorpus: /srv/corpora/newcorpus

3. Restart the Poliqarp daemon (the exact way to do this depends on your setup, how you started the daemon in the first place, etc.).


Configuring and running Marasca
===============================

.. note::

  Marasca is based on the well-known Django framework.
  The instructions below are not the only way to deploy Marasca.
  For more information about alternative ways of deployment, refer to `Django documentation <https://docs.djangoproject.com/en/dev/howto/deployment/>`_.

Technically speaking, Marasca is a Poliqarp client, and not a server, but it's described in this chapter because it's mostly used for giving users easy access to your Poliqarp server through a Web interface.

The steps presented below are intended for an Ubuntu system. 
Installing Marasca on other distributions should be very similar, but some details, such as package names, might be different.

First of all, install all the prerequisite software, as described in the :doc:`gettingstarted` chapter.
Secondly, make sure there's a Poliqarp daemon running on the same host.
Then, follow the instructions below:

1. Create a directory from which Marasca files will be served by Apache. 
   For the purposes of these instructions, we assume it will be :samp:`/srv/marasca`.
2. Copy Marasca files from the cloned source code repository to :samp:`/srv/marasca`.
3. Create an entry script (e.g. :samp:`/srv/django.wsgi`) for your application and make it readable for the Web server.
   The script should look like this:

.. highlight:: python

::

   import os
   import sys

   os.environ['DJANGO_SETTINGS_MODULE'] = 'settings'

   sys.path.append('/srv/marasca/')
   import django.core.handlers.wsgi
   application = django.core.handlers.wsgi.WSGIHandler()

4. Create an Apache configuration file for your Marasca installation and save it as :samp:`/etc/apache2/sites-available/marasca`.
   For example:

.. highlight:: apache

::

   <VirtualHost *:80>
	ServerName marasca.example.com
	Alias /css/ /srv/marasca/media/css/
	Alias /js/ /srv/marasca/media/js/
	Alias /extra/ /srv/marasca/media/extra/
	WSGIDaemonProcess marasca
	WSGIProcessGroup marasca
	WSGIScriptAlias / /srv/django.wsgi
	CustomLog ${APACHE_LOG_DIR}/poliqarp-access.log vhost_combined
   </VirtualHost>

.. note::

   Change the :samp:`ServerName` directive to match your server.

5. Rename the :samp:`/srv/marasca/setting/wbl.py` file to match your hostname.
   For example, if your hostname is "foo", name the file :samp:`foo.py`.

6. Open the file for editing and customize it for your site.

   a. Make sure the :samp:`_corpora_dir` variable points to the directory where you keep your Poliqarp corpora. 
   b. Edit the list returned by the :samp:`_get_corpora` function to match your corpora.
   c. Update other variables, such as the administrator's email, to match your environment.

7. Create information pages for your corpora (see :ref:`serving_information-pages`).
8. After customizing Marasca to your needs, use :samp:`a2ensite marasca` to enable the application and restart Apache.

.. note::

   Make sure to configure your Web server to serve the corpus's DjVu files at the location specified by :samp:`.djvu.filenames`.
   The files don't need to reside on the same server as Marasca -- you can host them in a separate location, as long as it's accessible to Marasca.


.. _serving_information-pages:

Information pages
-----------------

Each corpus in Marasca is accompanied by information pages.
The pages are encoded using Django's template language and can be easily customized.

Marasca's template files are located in the :samp:`templates` directory. 
The files in there are generic error messages, help pages, etc.
At least the :samp:`about.html` template should be customized before you put Marasca online.
The other files should be more or less ready to be used as they are.

The most important templates reside in the :samp:`corpora` subdirectory. 
They are information pages for each corpus served by Marasca.
The information page for each corpus can consist of two parts: the main part, and a "suffix" page (the mechanism is slightly similar in nature to Apache's :samp:`HeaderName` and :samp:`ReadmeName` directives), each having its own template.

A mapping between a corpus and its templates is achieved through the corpus identifier from the Poliqarp configuration file (see :doc:`building`).
The template name needs to match the identifier, so for example, if the corpus's identifier is "newcorpus", the relevant template files should be named :samp:`newcorpus.html` and :samp:`newcorpus_suffix.html`. If both templates are used for a corpus, the information page will consist of the main part, followed by the query text field, and ended by the "suffix". 

An example of a simple template is presented below.
Note the usage of internationalization related tags, which make it possible to create a multilingual interface (see next section for details).
Also note how standard HTML tags are used to handle layout and links to external pages.

.. highlight:: html

::

   {% load i18n %}

   {% blocktrans %}

   <p>The search engine uses the electronic versions of the 15 volumes of
   <a href='http://www.mimuw.edu.pl/polszczyzna/SGKPi/'>The Geographical 
   Dictionary of the Polish Kingdom and other Slavic Countries</a>
   (comprising together about 15 thousand pages including 14758 pages with entries)
   published on <a href='http://www.mimuw.edu.pl/polszczyzna/'>the Computer 
   and Polish Language site</a> hosted on a server courtesy of
   <a href='http://www.mimuw.edu.pl/?LANG=en'>the Faculty of Mathematics, 
   Informatics and Mechanics</a> of <a href='http://www.uw.edu.pl/en/'>the 
   University of Warsaw</a>.</p>

   {% endblocktrans %}


.. seealso::

   `Official Django documentation <https://docs.djangoproject.com/en/dev/topics/templates/>`_ on the template engine.

Localizing Marasca
------------------

Marasca follows Django's mechanisms for localization, so it's possible to create a multilingual user interface.
The prerequisite for that is to include proper markup in your template files (using :samp:`trans` or :samp:`blocktrans` directives).

When your template files are properly marked up, run the :samp:`update-i18n` script distributed with marasca. 
This will create :samp:`.po` files in the :samp:`locale` directory.
Those files can be used to translate the messages to a target language.
The exact format of the .po files and instructions for translating them go beyond the scope of this document.
Refer to official GNU Gettext documentation (see below for a link to their website).

After translating the strings in the .po file, run the following command in Marasca's root directory to generate the messages in the target language:

::

   ./manage compilemessages

Finally, restart the Web server for the changes to take effect.


.. seealso::

   Official Django manual on `localization <https://docs.djangoproject.com/en/1.3/topics/i18n/localization/>`_.

   GNU Gettext manual on `PO files <https://www.gnu.org/software/gettext/manual/html_node/PO-Files.html>`_.


Using Marasca to access a remote Poliqarp server
------------------------------------------------

.. warning::

   This has not been tested, so proceed with caution. 
   If you succeed in setting this up, we'll be happy to hear from you.

The setup described in the previous section assumes that both the Poliqarp daemon and Marasca are running on the same server.
However, in theory nothing should prevent you from running the Poliqarp daemon on one machine, and hosting Marasca on another.

The key thing to achieve this is to add :samp:`host` and :samp:`port` parameters to a piece of code responsible for establishing a connection to a Poliqarp server.
The modification should be made in the :samp:`app/views.py` file in Marasca.
For example:

.. highlight:: python

::

   class Connection(poliqarp.Connection):

      def __init__(self, request):
         poliqarp.Connection.__init__(self,host="192.168.0.1",port="9876")


.. seealso::

   `Marasca <https://bitbucket.org/jwilk/marasca-wbl>`_ 
      Main page for Marasca development efforts.


Query logging
=============

There are two types of logs that you can collect with Marasca:

- query logs
- Apache access and error logs

Query logs have the following format:

::

   
   2013-07-03T19:31:44	89.174.214.254/86d7e0d8cb906	slownik-polszczyzny-xvi-wieku	T	dispenza
   2013-07-03T19:32:29	89.174.214.254/86d7e0d8cb906	slownik-polszczyzny-xvi-wieku	T	dyspensa
   2013-07-03T19:32:34	89.174.214.254/86d7e0d8cb906	slownik-polszczyzny-xvi-wieku	T	dispensa

The most important information that can be gleaned from them is the corpus that was queried and the query itself (the last column).
Additionally, the logs include the time of the query and the IP address of the client. 
The location of the query log is configured through the :samp:`QUERY_LOG` variable in Marasca's configuration file (:samp:`<hostname>.py`).
The logs can grow quite large, so it's a good idea to set up :samp:`logrotate` to clean them up periodically.

Apache logs work on a different level and are mainly useful for debugging problems with Marasca (the error log), as well as for collecting detailed information about the HTTP communication taking place between the client and the server.


.. seealso::

   `Apache logs documentation <https://httpd.apache.org/docs/2.2/logs.html>`_

Next steps
==========

Once you have Marasca up and running, the corpus is ready to be used by remote users.
See :doc:`browsing` for information on how to access your corpus.


..  LocalWords:  gettingstarted poliqarpd startup samp newcorpus Django
..  LocalWords:  os sys environ apache VirtualHost ServerName css js py
..  LocalWords:  WSGIDaemonProcess WSGIProcessGroup WSGIScriptAlias dir
..  LocalWords:  CustomLog vhost hostname sSee ensite Django's html po
..  LocalWords:  Marasca's HeaderName ReadmeName blocktrans href init
..  LocalWords:  Informatics endblocktrans seealso Gettext poliqarp IP
..  LocalWords:  compilemessages slownik polszczyzny wieku dispenza
..  LocalWords:  dyspensa dispensa logrotate
