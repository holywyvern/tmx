.. title:: LibTMX Reference Documentation

About libTMX
============

`libTMX <https://github.com/baylej/tmx>`_ is a small and simple library to load maps created with
`Tiled <https://www.mapeditor.org/>`_.

Tiled map are stored in an `XML <https://www.w3.org/XML/>`_ document.
XML organizes data in a tree structure, libTMX loads TMX maps in a C datastructures that is organized just like the
source XML document.

libTMX has an ever-changing API (and ABI) therefore the recommended way to use it is to directly incorporate it in the
sources of your project.

.. toctree::
   :maxdepth: 1

   getting-started
   renderer-from-scratch
   build
   datastructure
   functions

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
