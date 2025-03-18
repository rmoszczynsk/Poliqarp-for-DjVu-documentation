============
File formats
============

Building a Poliqarp corpus involves converting between several data representations or file formats.
It's easy to get confused with all the acronyms, so this section provides a quick reference to all the formats used in a |p4d| pipeline.

DjVu
====

DjVu is a computer file format designed for efficient representation of scanned documents.
It's the source format for building  |p4d| corpora.
DjVu's key feature from the point of view of |p4d| is that documents can contain a *hidden text layer*.
This means that OCR-ed text can be stored along the graphical representation of the original document. 
Poliqarp corpora are built based on the text layer, and sophisticated Poliqarp queries can return graphical concordances because the items in the text layer are mapped to appropriate regions of the graphical layer or, in other words, the scanned image of the source document.

.. seealso::

   `DjVu specification from LizardTech <http://djvu.org/docs/DjVu3Spec.djvu>`_

   `DjVu resources <http://djvu.org/>`_

   `DjVuLibre utilities <http://djvu.sourceforge.net/>`_



hOCR
====

hOCR is a format for representing OCR output, which combines layout and recognition confidence information with the recognized text.
All this data is encoded in a standard HTML document, which makes it easy to process with existing tools.

In |p4d|, hOCR is used as the intermediate format between source DjVu files and XCES. 
The intermediate format can be annotated with information about the structure of the source document, which can be later used when querying the corpus to limit the query results e.g. only to the front matter or a particular chapter.

.. seealso::

   `hOCR specification <https://docs.google.com/document/d/1QQnIQtvdAC_8n92-LhwPcjtAUFwBlzE8EWnKAxlgVf0/preview?pli=1#heading=h.77bd784474e5>`_


XCES
====

XCES is an XML-based format for encoding text corpora. 
In |p4d|, it's used as the input for :samp:`bpng` for generating a binary corpus.

.. seealso::
   
   `Official XCES website <http://www.xces.org/>`_


Corpus configuration file
=========================

The corpus configuration file is used by the Poliqarp daemon.
First and foremost, it describes the tagset of the corpus, defining the categories to which the segments can belong and the attributes that categories might take.

.. seealso::

   Daniel Janus's `MSc thesis <http://nlp.ipipan.waw.pl/~adamp/msc/janus.daniel/praca.pdf.gz>`_ (in Polish)


bpng configuration file
=======================

:samp:`bpng` is the Poliqarp corpus builder.
Its configuration file controls which files are included in the corpus and defines the metadata fields.

.. seealso::

   :samp:`bpng` `man pages <http://poliqarp.sourceforge.net/man/bpng.html>`_



Corpus header
=============

There are only two requirements for the header file:

- It should be a well-formed XML document.
- It should include the metadata fields defined in the :samp:`bpng` configuration file.

A simple header file, with just two metadata fields, can look like this:

.. highlight:: xml

::

   <meta>
      <year>1990</year>
      <creator>John Doe</creator>
   </meta>


Structure file
==============

The structure file can be used to mark up the corpus with information about the physical layout of the source scanned document.
This allows the queries to be limited to particular parts of the document, such as the back matter.

The structure file is a plain-text document which must follow a specific syntax.
In short, the file:

- must refer to specific page ranges in the DjVu document;
- must include sequential numbers for each DjVu document that is described;
- can use any names for the individual sections;
- can describe a tree-like structure, i.e. sections can be nested;
- can include comments marked with "#".

An example is shown below.
The file describes the structure of a 200-page document with front matter, body, and back matter.

.. highlight:: bash

::

   1                         # DjVu document number
   front,1,20
   acknowledgement,5,20      # Nested section
   body,21,180
   back,181,200

The section identifiers can be used in corpus queries by means of the :samp:`within` clause to limit the query results to the particular section.

.. |p4d| replace:: Poliqarp for DjVu
.. |d4p| replace:: DjView for Poliqarp


..  LocalWords:  DjVu's seealso LizardTech DjVuLibre bd samp bpng MSc
..  LocalWords:  xml acknowledgement DjView
