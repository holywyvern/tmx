The Data Structure
==================

**libTMX** loads maps into a plain old C datastructure, this page describes that data structure thoroughly.
**libTMX** reuse names from the `TMX Format`_ for all its structures and structure members.

.. _TMX Format: https://doc.mapeditor.org/en/stable/reference/tmx-map-format/

Definitions
-----------

Tiled use the 3 leftmost bits to store if a tile is flipped.

.. c:macro:: TMX_FLIPPED_HORIZONTALLY

   Used to tell if a cell has the horizontal flip flag set:

   .. code-block:: c

      int is_horizontally_flipped = cell & TMX_FLIPPED_HORIZONTALLY;

.. c:macro:: TMX_FLIPPED_VERTICALLY

   Used to tell if a cell has the vertical flip flag set:

   .. code-block:: c

      int is_horizontally_flipped = cell & TMX_FLIPPED_VERTICALLY;

.. c:macro:: TMX_FLIPPED_DIAGONALLY

   Used to tell if a cell has the diagonal flip flag set:

   .. code-block:: c

      int is_horizontally_flipped = cell & TMX_FLIPPED_DIAGONALLY;

.. c:macro:: TMX_FLIP_BITS_REMOVAL

   Used to remove all flip flags:

   .. code-block:: c

      int gid = cell & TMX_FLIPPED_HORIZONTALLY;

Enumerations
------------

.. c:type:: enum tmx_map_orient

   Map orientation (orthogonal, isometric, hexagonal, ...).

   +-----------------+-----------------------------------------------+
   | Map orientation | Description                                   |
   +=================+===============================================+
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
   +=================+===============================================+
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
   +===============+=============================================+
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
   +==============+============================================+
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
   +============+======================================================+
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

   Whether the objects are drawn according to the order of appearance ("index") or sorted by their y-coordinate
   ("topdown").

   +------------------+------------------------------------------------------+
   | Object draworder | Description                                          |
   +==================+======================================================+
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
   +=============+==========================================================+
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
   +===============+========================================================+
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
   +============+==========================================+
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
   +============+==========================================+
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

The datastructure is a tree, just like the source document, from the root (:c:type:`tmx_map`) you can access everything.

.. c:type:: tmx_map

   The root of the datastructure.

   .. c:member:: enum tmx_map_orient orient

      Map orientation, see :c:type:`tmx_map_orient`.

   .. c:member:: unsigned int width

      The width of the map in cells.

   .. c:member:: unsigned int height

      The height of the map in cells.

   .. c:member:: unsigned int tile_width

      The width of tiles in pixels.

   .. c:member:: unsigned int tile_height

      The height of tiles in pixels.

   .. c:member:: enum tmx_stagger_index stagger_index

      Stagger index, see :c:type:`tmx_stagger_index`.

   .. c:member:: enum tmx_stagger_axis stagger_axis

      Stagger axis, see :c:type:`tmx_stagger_axis`.

   .. c:member:: int hexsidelength

      Only for hexagonal maps. Determines the width or height (depending on the staggered axis) of the tile’s edge,
      in pixels.

   .. c:member:: unsigned int backgroundcolor

      Global background color, encoded in an integer, 4 bytes: ARGB.

   .. c:member:: enum tmx_map_renderorder renderorder

      Map render order, see :c:type:`tmx_map_renderorder`.

   .. c:member:: tmx_properties *properties

      Properties of the map, see :c:type:`tmx_properties`.

   .. c:member:: tmx_tileset_list *ts_head

      Head of the tileset linked list, see :c:type:`tmx_`.

   .. c:member:: tmx_layer *ly_head

      Head of the layers linked list, see :c:type:`tmx_layer`.

   .. c:member:: unsigned int tilecount

      length of the :c:member:`tiles` array described below.

   .. c:member:: tmx_tile **tiles

      GID indexed tile array (array of pointers to :c:type:`tmx_tile`).

   .. c:member:: tmx_user_data user_data

      Use that member to store your own data, see :c:type:`tmx_user_data`.

.. c:type:: tmx_layer

   .. c:member:: char *name

      Name of the layer (user defined).

   .. c:member:: double opacity

      Opacity of the layer (0.0 = transparent, 1.0 = opaque).

   .. c:member:: int visible

      Boolean, visibility of the layer (0 = false, any other value = true).

   .. c:member:: int offsetx

      Horizontal offset in pixels, a positive value shifts the layer to the right.

   .. c:member:: int offsety

      Vertical offset in pixels, a positive value shifts the layer down.

   .. c:member:: enum tmx_layer_type type

      Type of layer, see :c:type:`tmx_layer_type`, tells you which member to use in :c:member:`tmx_layer.content`.

   .. c:member:: union layer_content content

      Content of the layer, as there are several types of layers (tile, object, image, ...) the content is different for
      each type.
      You should check the value of member :c:member:`tmx_layer.type` to use the correct union member.

   .. c:member:: tmx_user_data user_data

      Use that member to store your own data, see :c:type:`tmx_user_data`.

   .. c:member:: tmx_properties *properties

      Properties of the layer, see :c:type:`tmx_properties`.

   .. c:member:: tmx_layer *next

      Next element of the linked-list, if NULL then you reached the last element.

.. c:type:: tmx_tileset_list

   .. c:member:: int is_embedded

      Private member used internally to free this tileset.

   .. c:member:: unsigned int firstgid

      GID (Global ID) of the first tile in this tileset.

   .. c:member:: tmx_tileset *tileset

      Tileset data, see :c:type:`tmx_tileset`.

   .. c:member:: tmx_tileset_list *next

      Next element of the linked-list, if NULL then you reached the last element.

.. c:type:: tmx_tileset

   .. c:member:: char *name

      Name of the tileset (user defined).

   .. c:member:: unsigned int tile_width

      The width of tiles in pixels.

   .. c:member:: unsigned int tile_height

      The height of tiles in pixels.

   .. c:member:: unsigned int spacing

      The spacing in pixels between the tiles in this tileset (applies to the tileset image).

   .. c:member:: unsigned int margin

      The margin around the tiles in this tileset (applies to the tileset image).

   .. c:member:: int x_offset

      Horizontal offset in pixels, a positive value shifts the drawing of tiles to the right.

   .. c:member:: int y_offset

      Vertical offset in pixels, a positive value shifts the drawing of tiles down.

   .. c:member:: unsigned int tilecount

      The number of tiles in this tileset, length of the :c:member:`tmx_tileset.tiles` array.

   .. c:member:: tmx_image *image

      Image for this tileset, may be NULL if this tileset is a collection of single images (one image per tile).

   .. c:member:: tmx_user_data user_data

      Use that member to store your own data, see :c:type:`tmx_user_data`.

   .. c:member:: tmx_properties *properties

      Properties of the tileset, see :c:type:`tmx_properties`.

   .. c:member:: tmx_tile *tiles

      Array of :c:type:`tmx_tile`, its length is :c:member:`tmx_tileset.tilecount`.

.. c:type:: tmx_tile

   .. c:member:: unsigned int id

      LID (Local ID) of the tile, unsigned int GID = :c:member:`tmx_tileset.firstgid` + LID;

   .. c:member:: tmx_tileset *tileset

      The owner of this tile, see :c:type:`tmx_tileset`.

   .. c:member:: unsigned int ul_x

      Upper-left x coordinate of this tile on the tileset image, irrelevant if the this tile has its own image.

   .. c:member:: unsigned int ul_y

      Upper-left y coordinate of this tile on the tileset image, irrelevant if the this tile has its own image.

   .. c:member:: tmx_image *image

      Image for this tile, may be NULL if this tileset use a single image (atlas) for all tiles.

   .. c:member:: tmx_object *collision

      Collision shape of this tile, may be NULL (optional).

   .. c:member:: unsigned int animation_len

      Length of the :c:member:`tmx_tile.animation` array.

   .. c:member:: tmx_anim_frame *animation

      Array of :c:type:`tmx_anim_frame` animation frames.

   .. c:member:: char *type

      Type (user defined).

   .. c:member:: tmx_properties *properties

      Properties of the tile, see :c:type:`tmx_properties`.

   .. c:member:: tmx_user_data user_data

      Use that member to store your own data, see :c:type:`tmx_user_data`.

.. c:type:: tmx_object_group

   .. c:member:: TODO

      

.. c:type:: tmx_objecttmx_object

   .. c:member:: TODO

      

.. c:type:: tmx_shape

   .. c:member:: TODO

      

.. c:type:: tmx_text

   .. c:member:: TODO

      

.. c:type:: tmx_template

   .. c:member:: TODO

      

.. c:type:: tmx_anim_frame

   .. c:member:: TODO

      

.. c:type:: tmx_image

   .. c:member:: TODO

      

.. c:type:: tmx_properties

   .. c:member:: TODO

      

.. c:type:: tmx_property

   .. c:member:: TODO

      

.. c:type:: tmx_property_value

   .. c:member:: TODO

      

.. c:type:: tmx_user_data

   .. c:member:: TODO

      
