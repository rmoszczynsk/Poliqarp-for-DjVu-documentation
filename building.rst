===============================
Building corpora - basic tagset
===============================

.. highlight:: bash

This chapter provides a step-by-step walkthrough for building a new corpus based on the example of `The Dictionary of 16th Century Polish <http://poliqarp.wbl.klf.uw.edu.pl/en/slownik-polszczyzny-xvi-wieku/>`_.

The input for any corpus building operation should consist of:

- DjVu files,
- a corpus configuration file with a tagset definition, which should be created during the design phase (see :doc:`designing`).

In the case of this example, the tagset is very simple.
It's assumed that all the segments belong to one class ("ign", for "ignore") which doesn't have any attributes.
Thus, the configuration file looks like this:

::

  [attr]

  [pos]
  ign =

  [named-entity]
  entity-orth = orth
  entity-base = base
  entity-tag = tag
  entity-pos = pos

See :doc:`formats` for a more detailed specification of the corpus definition file.
Examples of corpora with more complex tagsets are provided in the following chapters.


Preparing DjVu sources
======================

The Dictionary of 16th Century Polish is a multivolume work. 
Work on the early volumes started before the onset of the digital age.
Thus, the DjVu sources for the corpus are a mix of scanned paper dictionaries and digitally-born documents.

Details of the OCR process will not be presented here (it was performed by the staff of the Kujawsko-Pomorska Digital Library).
The important thing for the purposes of this tutorial is that all the DjVu source files had both the graphic layer and the hidden layer with OCR-ed text.

The actual corpus covers 35 volumes of the dictionary. 
For the purposes of this demonstration, we'll work with 3 volumes.

The first thing to do is to create a workspace and arrange the source files.
Let's assume the following directory structure:

::

   .
   |___v01/
   | |___v01.djvu
   | |___hocr/
   | |___unbundled/
   | |___xces/
   |___v02/
   | |___v02.djvu
   | |___hocr/
   | |___unbundled/
   | |___xces/
   |___v03/
   | |___v03.djvu
   | |___hocr/
   | |___unbundled/
   | |___xces/
   |___structure.txt


The next thing to do is to unbundle the DjVu files, i.e. convert them into single page documents.
This can be achieved by running the following command on each DjVu file:

::

   djvmcvt -i v01/v01.djvu v01/unbundled/index.djvu

For each volume, this will create a set of DjVu files in the :samp:`unbundled` directory, each corresponding to a single page, and an :samp:`index.djvu` file which can be though of as an entry point to the document.


Creating intermediate files
===========================

After splitting the source DjVu file into single-page documents, it's time for some more conversion which will eventually lead to a Poliqarp corpus.

First of all, convert the DjVu files into hOCR documents.
In the case of a bundle of single page DjVu files, index.djvu needs to be used as the entry point.
Run the following command for each volume:

::

   djvu2hocr --word-segmentation=uax29 v01/unbundled/index.djvu > v01/hocr/v01.hocr

At this point it's possible to add structure information to the hOCR files, which can allow only specific parts of the corpus to be queried (such as the original work's front matter) using the :samp:`within` keyword.
In order to add structure, you need to prepare a special configuration file (the :doc:`formats` chapter contains a full specification).
For the 3-volume dictionary in this example, the structure file (:samp:`structure.txt`) looks like this, with each line representing a logical section in the original paper document:

::

   1
   front, 1,140
   intro, 5,140
   list, 141,146
   body, 147,452
   errata, 450,452
   back, 453,454
   inset, 455,457
   back, 458,459

   2
   front, 1,6
   list, 7,13
   body, 15,540
   errata, 536,540
   back, 541,542
   errata, 543,547

   3
   front, 1,4
   list, 5,11
   body, 13,741
   back, 742,742


Once you have the structure configuration file prepared, add the structure to the hOCR files with this command:

::

   annotate-hocr --in-place v01/hocr/v01.hocr v02/hocr/v02.hocr v03/hocr/v03.hocr < structure.txt

The last conversion step before creating the actual corpus files is to transform the hOCR file into the XCES format, which is understood by Poliqarp's corpus building utility.
You can do this with the following command (repeat for each file):

::

   hocr2xces.py < v01/hocr/v01.hocr > v01/xces/v01.xml


Creating the binary corpus
==========================

Now it's time to create the corpus files which will be then used by the Poliqarp server.
The corpus building utility (:samp:`bpng`) needs the following as input:

- A configuration file which controls the build process
- For each volume, the XCES file created in the previous step, and a header file with metadata information


First of all, arrange the input files into the following structure.
Note that you need to rename your XCES files to something uniform (in this example we opted for :samp:`data.xml`).

.. warning::

   Do not delete the hOCR files from previous section just yet.
   They'll be needed in one of the following steps.

::

   .
   |___v01/
   | |___header.xml
   | |___data.xml
   |___v02/
   | |___header.xml
   | |___data.xml
   |___v03/
   | |___header.xml
   | |___data.xml
   |___newcorpus.bp.conf

.. note::

   Note the format of the configuration file name.
   The first part (`newcorpus`) is the base name of the corpus we're creating.
   In real applications, this should be more unique and chosen with some consideration, as it will be used in other configuration files and some of the subsequent steps.


Secondly, prepare the build configuration file.
It defines the corpus locale, the file names that :samp:`bpng` should expect, and the metadata fields (preparing metadata is described in more detail in :doc:`designing`).

::

  [locale]
  locale = pl_PL

  [filenames]
  header = header.xml
  morphosyntax = data.xml

  [meta]
  name = vol
  path = /meta/volume

  [meta]
  name = year
  path = /meta/year

  [meta]
  name = range
  path = /meta/range

  [meta]
  name = orig
  path = /meta/orig

Thirdly, edit the header file for each volume to make it conform to the metadata structure defined in the configuration file.
For example, the header file for volume 1 in our example looks like this:

.. highlight:: xml

::

   <meta>
      <volume>I</volume>
      <year>1966</year>
      <range>A-Bany</range>
      <orig>scan</orig>
   </meta>


Now everything is ready to create the corpus.
Run the following commands to create the binary files and an index which will speed up getting query results.
Depending on the size of your corpus, this might take a while.

.. highlight:: bash

::

   bpng newcorpus v01/ v02/ v03/
   bpindexer -i o newcorpus

You should end up with a lot of new files prefixed with the base name of your corpus.
At this point, the corpus is almost ready.


Augmenting the corpus
=====================

The final step is to augment the corpus with information about the coordinates of the individual segments on the pages of source DjVu files.
This makes it possible to jump from query results to the exact place in the scanned source where the match is located.

To augment the corpus, you need the corpus from the previous step, as well as the hOCR files created at the very beginning of the procedure.
Run the following command to augment your corpus:

::

   augment-djvu-corpus.py newcorpus v01/hocr/v01.hocr v02/hocr/v02.hocr v03/hocr/v03.hocr

This should result in creating a :samp:`newcorpus.djvu.coordinates` file.

.. note::

   It's s good idea to perform a quick validation step now.
   The :samp:`.poliqarp.corpus.image` file should be 1.5 times bigger than the :samp:`.djvu.coordinates` file.
   If this is not the case, something went wrong and Poliqarp will probably crash when querying your corpus.

The corpus is now ready.


Configuring Poliqarp
====================

Now you should be able to test the new corpus by running the Poliqarp daemon and querying the corpus with either the command line client or the graphical client.
To do this, copy the binary corpus files and the corpus configuration file (the file with tagset definition, not the :samp:`bpng` configuration file!) to a separate location, e.g. :samp:`/srv/corpora/newcorpus`.
Next, create a configuration file for the Poliqarp daemon and save it under :samp:`/srv/corpora/config/poliqarpd.conf`.
For the purposes of this example, it will look like this (see Poliqarp manual pages for a full description of the options):

::

   port = 4567
   logging = on
   log-file = /srv/corpora/newcorpus/query.log
   corpus = newcorpus: /srv/corpora/newcorpus
   max-match-length = 100

You can now start the Poliqarp daemon with the following command:

::

   /usr/local/bin/poliqarpd -c /srv/corpora/config/poliqarpd.conf

If the daemon starts successfully, you can try to query the corpus to see if it's working.
See :doc:`browsing` for information on accessing the corpus.


Next steps
==========

If the corpus works fine locally, you can configure Marasca, which will make it possible to display query results directly on the pages of the digitalized documents.
See :doc:`serving` for details.



..  LocalWords:  th ign attr pos orth multivolume Kujawsko Pomorska txt
..  LocalWords:  djvu hocr xces djvmcvt samp uax Poliqarp's py bpng xml
..  LocalWords:  filenames morphosyntax Bany newcorpus bpindexer
