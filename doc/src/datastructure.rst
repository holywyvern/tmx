The Data Structure
==================

**libTMX** loads maps into a plain old C datastructure, this page describes that data structure thoroughly.
**libTMX** reuse name from the `TMX Format`_ for all its structure and structure members.

.. _TMX Format: https://doc.mapeditor.org/en/stable/reference/tmx-map-format/

Definitions
-----------

Tiled use the 3 leftmost bits to store if a tile is flipped.

.. c:macro:: TMX_FLIPPED_HORIZONTALLY

Used to tell if a cell has the horizontal flip flag set: `int is_horizontally_flipped = cell & TMX_FLIPPED_HORIZONTALLY;`.

.. c:macro:: TMX_FLIPPED_VERTICALLY

Used to tell if a cell has the vertical flip flag set: `int is_horizontally_flipped = cell & TMX_FLIPPED_VERTICALLY;`.

.. c:macro:: TMX_FLIPPED_DIAGONALLY

Used to tell if a cell has the diagonal flip flag set: `int is_horizontally_flipped = cell & TMX_FLIPPED_DIAGONALLY;`.

.. c:macro:: TMX_FLIP_BITS_REMOVAL

Used to remove all flip flags: `int gid = cell & TMX_FLIPPED_HORIZONTALLY;`.

Enumerations
------------

.. c:type:: enum tmx_map_orient

Map orientation (orthogonal, isometric, hexagonal, ...).

+-----------------+-----------------------------------------------+
| Map orientation | Description                                   |
+-----------------+-----------------------------------------------+
| O_NONE          | In case of error, or unknown map orientation. |
+-----------------+-----------------------------------------------+
| O_ORT           | Orthogonal map.                               |
+-----------------+-----------------------------------------------+
| O_ISO           | Isometric map.                                |
+-----------------+-----------------------------------------------+
| O_STA           | Staggered map.                                |
+-----------------+-----------------------------------------------+
| O_HEX           | Hexagonal map.                                |
+-----------------+-----------------------------------------------+

.. c:type:: enum tmx_map_renderorder

How the tile layers should be drawn, the order in which tiles on tile layers are rendered.
This is especially usefull if you're drawing overlapping tiles (see the perspective_walls.tmx example that ships with
Tiled).

+-----------------+-----------------------------------------------+
| Map renderorder | Description                                   |
+-----------------+-----------------------------------------------+
| R_NONE          | In case of error, or unknown map renderorder. |
+-----------------+-----------------------------------------------+
| R_RIGHTDOWN     | Draw tiles from right to left, top to bottom. |
+-----------------+-----------------------------------------------+
| R_RIGHTUP       | Draw tiles from right to left, bottom to top. |
+-----------------+-----------------------------------------------+
| R_LEFTDOWN      | Draw tiles from left to right, top to bottom. |
+-----------------+-----------------------------------------------+
| R_LEFTUP        | Draw tiles from left to right, bottom to top. |
+-----------------+-----------------------------------------------+


.. c:type:: enum tmx_stagger_index

For staggered and hexagonal maps, determines whether the "even" or "odd" indexes along the staggered axis are shifted.

+---------------+---------------------------------------------+
| Stagger index | Description                                 |
+---------------+---------------------------------------------+
| SI_NONE       | In case of error, or unknown stagger index. |
+---------------+---------------------------------------------+
| SI_EVEN       | Odd.                                        |
+---------------+---------------------------------------------+
| SI_ODD        | Even.                                       |
+---------------+---------------------------------------------+

.. c:type:: enum tmx_stagger_axis

For staggered and hexagonal maps, determines which axis ("x" or "y") is staggered.

+--------------+--------------------------------------------+
| Stagger axis | Description                                |
+--------------+--------------------------------------------+
| SA_NONE      | In case of error, or unknown stagger axis. |
+--------------+--------------------------------------------+
| SA_X         | x axis.                                    |
+--------------+--------------------------------------------+
| SA_Y         | y axis.                                    |
+--------------+--------------------------------------------+

.. c:type:: enum tmx_layer_type

Type of layer.

+------------+------------------------------------------------------+
| Layer type | Description                                          |
+------------+------------------------------------------------------+
| L_NONE     | In case of error, or unknown layer type.             |
+------------+------------------------------------------------------+
| L_LAYER    | Tile layer type, use `content.gids`.                 |
+------------+------------------------------------------------------+
| L_OBJGR    | Objectgroup layer type, use `content.objgr`.         |
+------------+------------------------------------------------------+
| L_IMAGE    | Image layer type, use `content.image`.               |
+------------+------------------------------------------------------+
| L_GROUP    | Group of layer layer type, use `content.group_head`. |
+------------+------------------------------------------------------+

.. c:type:: enum tmx_objgr_draworder

Whether the objects are drawn according to the order of appearance ("index") or sorted by their y-coordinate ("topdown").

+------------------+------------------------------------------------------+
| Object draworder | Description                                          |
+------------------+------------------------------------------------------+
| G_NONE           | In case of error, or unknown draw order.             |
+------------------+------------------------------------------------------+
| G_INDEX          | Draw objects as they are ordered in the linked-list. |
+------------------+------------------------------------------------------+
| G_TOPDOWN        | Draw objects sorted by their y-coordinate, objects   |
|                  | must then be reordered by their y-coordinate.        |
+------------------+------------------------------------------------------+

.. c:type:: enum tmx_obj_type

Type of object.

+-------------+----------------------------------------------------------+
| Object type | Description                                              |
+-------------+----------------------------------------------------------+
| OT_NONE     | In case of error, or unknown object type.                |
+-------------+----------------------------------------------------------+
| OT_SQUARE   | Square, use members `x`, `y`, `width` and `height`.      |
+-------------+----------------------------------------------------------+
| OT_POLYGON  | Polygon, use `content.shape`.                            |
+-------------+----------------------------------------------------------+
| OT_POLYLINE | Polyline, use `content.shape`.                           |
+-------------+----------------------------------------------------------+
| OT_ELLIPSE  | Ellipse, use members `x`, `y`, width (horizontal radius) |
|             | and height (vertical radius)                             |
+-------------+----------------------------------------------------------+
| OT_TILE     | Tile, use `content.gid`.                                 |
+-------------+----------------------------------------------------------+
| OT_TEXT     | Text, use `content.text`.                                |
+-------------+----------------------------------------------------------+
| OT_POINT    | Point, use members `x`, `y`.                             |
+-------------+----------------------------------------------------------+

.. c:type:: enum tmx_property_type

Type of property.

+---------------+--------------------------------------------------------+
| Property type | Description                                            |
+---------------+--------------------------------------------------------+
| PT_NONE       | In case of error, or unknown property type.            |
+---------------+--------------------------------------------------------+
| PT_INT        | Integer, use `value.integer`.                          |
+---------------+--------------------------------------------------------+
| PT_FLOAT      | Float, use `value.decimal`.                            |
+---------------+--------------------------------------------------------+
| PT_BOOL       | Boolean, use `value.boolean`.                          |
+---------------+--------------------------------------------------------+
| PT_STRING     | String, use `value.string`.                            |
+---------------+--------------------------------------------------------+
| PT_COLOR      | Color, use `value.color` (RGBA encoded in an integer). |
+---------------+--------------------------------------------------------+
| PT_FILE       | Path to a file, use `value.file`.                      |
+---------------+--------------------------------------------------------+

.. c:type:: enum tmx_horizontal_align

Horizontal alignment of the text within the object.

+------------+------------------------------------------+
| Text align | Description                              |
+------------+------------------------------------------+
| HA_NONE    | In case of error, or unknown text align. |
+------------+------------------------------------------+
| HA_LEFT    | Left.                                    |
+------------+------------------------------------------+
| HA_CENTER  | Center.                                  |
+------------+------------------------------------------+
| HA_RIGHT   | Right.                                   |
+------------+------------------------------------------+

.. c:type:: enum tmx_vertical_align

Vertical alignment of the text within the object.

+------------+------------------------------------------+
| Text align | Description                              |
+------------+------------------------------------------+
| VA_NONE    | In case of error, or unknown text align. |
+------------+------------------------------------------+
| VA_TOP     | Top.                                     |
+------------+------------------------------------------+
| VA_CENTER  | Center.                                  |
+------------+------------------------------------------+
| VA_BOTTOM  | Bottom.                                  |
+------------+------------------------------------------+


Data Structures
---------------

