=================
Designing corpora
=================

This chapter presents basic information about the structure of Poliqarp corpora and considerations that should be taken into account when creating a new corpus.
This mainly means preparing the DjVu sources (including performing optical character recognition), designing a tagset, and deciding on the structure of metadata. 

Preparing DjVu sources
======================

The first step to building a new corpus is to prepare the source DjVu files.
In general, there are two possible scenarios (which can sometimes co-occur in one corpus): the source documents are either paper publications, or they are digitally-born. 

In the former case, DjVu files need to be created by scanning and performing optical character recognition (OCR) on the paper documents.
This will not be described in detail here, as there are numerous ways to go about this task.
However, some examples will be given in subsequent chapters where real-life examples are discussed.

In the latter case, either DjVu files are already available, or the documents are in another digital format, such as PDF, which can be converted into DjVu. In both cases, it's crucial that the DjVu files contain the hidden text layer with the results of OCR.


Unbundling DjVu files
---------------------

If you have a single, multipage DjVu document, split it into single page documents (this is known as "unbundling").
This operation will make querying and displaying the results faster, because instead of downloading the whole, possibly big document, only the relevant page will need to be fetched to display the results.

Unbundling is a simple operation which requires the DjVuLibre package.
Let's assume you have a multiple page DjVu document called source.djvu.
To split it into separate pages, use the following command:

.. highlight:: bash

::

   djvmcvt -i source.djvu ./ index.djvu

This will create a set of single page documents in the current directory.


Other DjVu operations
---------------------

Apart from unbundling, djvulibre makes it possible to manipulate DjVu documents in additional ways.
The package comes with several utilities which can be used to, among other things, remove duplicate pages from a DjVu file.

In the example below, :samp:`djvm` is used in a simple :samp:`bash` loop to remove pages 1000-1105 from a document:

::

   for i in $(seq 1000 1105) ; do djvm -d document.djvu 1000 ; echo "Removed page $i" ; done

Refer to DjVuLibre's documentation for more information and examples.

Creating a tagset
=================

A tagset is a set of markers which describe the segments (roughly: words) in a corpus.
A tagset for a Poliqarp corpus needs to be defined in the corpus's configuration file (see :doc:`formats` for a full specification of the configuration file).

There are two groups of information defined in a tagset. 
First of all, a set of *categories*, that is attributes of a segment.
Secondly, there are *classes* to which a segment can belong.
If you think about linguistic corpora, a category would be something like "gender", and a class could be a "noun".

In technical terms, categories are specified by the :samp:`[ATTR]` keyword in the corpus configuration file, and are key/value pairs in which there can be multiple values.
Classes are specified by the :samp:`[POS]` keyword and are key/value pairs as well, but:

- the value list might be empty (i.e. the class has no attributes);
- if present, the values correspond to category keys.

Here's how the noun/gender example would look in a corpus configuration file, with "determiner" added to demonstrate a class with no attributes:

::

   [ATTR]
   gender = masculine feminine neuter

   [POS]
   noun = gender
   determiner = 


Practical examples of how such tagsets can be used to describe |p4d| corpora are provided in the following chapters.


Preparing metadata
==================

Metadata are used in Poliqarp to store additional information about each document in a corpus, so that it's possible for example to query only those documents that were published before 1950.
There's no predefined set of metadata in Poliqarp.
The corpus designer can create a custom set of fields, which then need to be filled with information for each individual document in the corpus.

Metadata fields are defined in the :samp:`bpng` [#f1]_ configuration file (see :doc:`formats` for more information) and use XPath syntax, because the actual metadata is then supplied in XML format.

Let's assume that you want your documents to be described with a set of three metadata fields: the year of publication, the author, and the source of the document.
In this case, the metadata section of the :samp:`bpng` configuration file will look like this:

::

   [meta]
   name = year
   path = meta/year

   [meta]
   name = author
   path = meta/author

   [meta]
   name = source
   path = meta/source


This definition corresponds to the following XML skeleton:

.. highlight:: xml

::

   <meta>
      <year></year>
      <author></author>
      <source></source>
   </meta>


Next steps
==========

The theory presented in this chapter is shown in practice in the following chapters, where you can find some step-by-step examples of building corpora of varying complexity from scratch.

.. rubric:: Footnotes

.. [#f1] :samp:`bpng` is a utility for building binary Poliqarp corpora. It's described in more detail in the following chapters.


.. |p4d| replace:: Poliqarp for DjVu

..  LocalWords:  PDF DjVuLibre djvu djvmcvt djvulibre samp djvm ATTR
..  LocalWords:  DjVuLibre's POS bpng XPath xml
