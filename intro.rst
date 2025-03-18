============
Introduction
============

|p4d| (a.k.a. Marasca [#f1]_) is a set of tools for building corpora from scanned documents, such as historical dictionaries.
The tools can be roughly divided into two groups.

The first one includes scripts for augmenting binary corpora with information about graphical coordinates of corpus segments in DjVu documents. 
This makes it possible to display graphical concordances for the corpus query results.
In other words, the user can see the results of their query in the digital version of the source document.
The second group allows serving such augmented corpora over the network and browsing the graphical concordances.

|p4d| is available under the terms of the GNU General Public License v. 2.
The current version is stable, but new features are still under development.
The upcoming additions will make it possible to create corpora with more sophisticated tagsets.

If you want to try out |p4d|, the best place to start is the `Virtual Lexicographical Library <poliqarp.wbl.klf.uw.edu.p>`_ of the Formal Linguistics Department of the University of Warsaw.
The library gives access to several corpora built from digitalized versions of historical Polish dictionaries.

If you like what you see there and want to learn more about how the software works, you can download `a virtual machine <http://fleksem/ndt/ubuntu4poliqarp/>`_ which comes with a preconfigured version of |p4d|.
It can be used to try out the software and to learn about the technical aspects of its configuration.

This documentation is primarily for administrators and corpus creators. 
It assumes some proficiency with Linux, especially working with the console.
Because the software is based on several third-party tools, the documentation contains many references to external sources to avoid duplicating information and instructions that are already available elsewhere.


Background
==========

The idea behind |p4d| combines the advantages of two technologies: the Poliqarp corpus query engine and the DjVu document format.
|p4d|  operates by augmenting a standard Poliqarp corpus with information about the bounding box coordinates of the segments in DjVu documents.

Poliqarp was originally intended for linguistic corpora, but some of its features can be used successfully to facilitate access to large digitalized documents.
These features include:

- a powerful query language,
- support for two-level annotation and polyinterpretation.

The latter means that Poliqarp corpora store not only the surface (i.e. orthographic) representation of words, but also the base forms of words that result from morphological analysis.
Since automatic morphological analysis often cannot produce unambiguous results, all possible interpretations are stored in the corpus - hence *polyinterpretation*.
This feature can be useful for digitalized and OCR-ed documents as well, for example in the context of historical documents in which some characters might map into several possible characters in modern orthography.

The DjVu format is useful for scanned documents for three main reasons:

- the possibility to create "unbundled" (a.k.a. single-page) documents, which can speed up displaying query results for large documents (e.g. dictionaries), because only a single page with a particular result needs to be downloaded;
- support for hidden text, which makes it possible to store the results of OCR together with the digital (i.e. graphical) representation of the original document;
- support for highlighting regions in the documents determined by graphical coordinates, which makes it possible e.g. to jump directly to specific words in the graphical representation of the documents.

When combined, Poliqarp and DjVu can be used to build corpora that allow efficient searching in large digitalized documents, using a powerful query language and optionally displaying search results in the graphical representation of source documents.


There are several possible workflows for creating a new |p4d| corpus and making it accessible to the public.
Some of them are described in the following chapters.

The most basic workflow  consists of the following steps:

#. Preparing the DjVu sources, which includes optical character recognition (OCR) and unbundling the documents.
#. Creating an hOCR document from DjVu sources.
#. Transforming the hOCR into XCES.
#. Creating a binary Poliqarp corpus from XCES.
#. Augmenting the binary corpus with graphical coordinates of the words (segments) in the DjVu sources.


.. TODO <workflow diagram>

.. seealso::

   Bień, Janusz S. (2009) `Facilitating access to digitalized dictionaries in DjVu format <http://bc.klf.uw.edu.pl/118/>`_. n: MONDILEX project open workshop " Representing Semantics in Digital Lexicography" within an international scientific conference COGNITIVE and CONTRASTIVE STUDIES, June 29 - July 1, 2009, Warszawa.

   Bień, Janusz S. (2011) `Efficient search in hidden text of large DjVu documents <http://bc.klf.uw.edu.pl/177/>`_. In: Advanced Language Technologies for Digital Libraries. Lecture Notes in Computer Science (Theoretical Computer Science and General Issues) (6699). Springer, pp. 1-14.

   `Poliqarp for DjVu source code repository <https://bitbucket.org/jwilk/marasca>`_

   `Poliqarp for DjVu instance at the Formal Linguistics Department of the University of Warsaw <http://poliqarp.wbl.klf.uw.edu.pl/>`_

   `A virtual machine with a working instance of Poliqarp for DjVu <http://fleksem/ndt/ubuntu4poliqarp/>`_

.. rubric:: Footnotes

.. [#f1] In the remainder of this document, I'll use the term *Poliqarp for DjVu* to refer to the whole set of tools necessary to build and serve DjVu-based corpora, and the term *Marasca* to refer to the Web client which allows querying the corpora and displaying results in the graphical layer of the source documents.

.. |p4d| replace:: Poliqarp for DjVu

..  LocalWords:  Bień
