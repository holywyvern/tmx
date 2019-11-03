Creating a map renderer from scratch
====================================

In this article, you will learn how to create a Map renderer from scratch using different game programming libraries:
`SDL 2`_, `Allegro 5`_ and `raylib`_. In this article we will assume that you are already proficient with one of these
libraries, have a working version installed or are able to compile it by yourself.

We will be using a map from Tiled's examples: `rpg/island.tmx`_. That particular map features an single :term:`Tileset`
defined in its own :term:`TSX` file, several :term:`Layers <Layer>`, :term:`Objects <Object>` and animated
:term:`Tiles <Tile>`.

.. _SDL 2: https://www.libsdl.org/
.. _Allegro 5: https://liballeg.org/
.. _raylib: https://www.raylib.com/
.. _rpg/island.tmx: https://github.com/bjorn/tiled/tree/master/examples/rpg

Initial set-up
--------------

Unfortunately as in any project we have to start with the usual boilerplate code to initialise libraries, create a
display (window), and the main loop.

.. tabs::

   .. code-tab:: c SDL 2

         #include <stdio.h>
         #include <tmx.h>
         #include <SDL.h>
         #include <SDL_events.h>

         #define DISPLAY_H 480
         #define DISPLAY_W 640

         static SDL_Renderer *ren = NULL;

         Uint32 timer_func(Uint32 interval, void *param) {
           SDL_Event event;
           SDL_UserEvent userevent;

           userevent.type = SDL_USEREVENT;
           userevent.code = 0;
           userevent.data1 = NULL;
           userevent.data2 = NULL;

           event.type = SDL_USEREVENT;
           event.user = userevent;

           SDL_PushEvent(&event);
           return(interval);
         }

         int main(int argc, char **argv) {
           SDL_Window *win;
           SDL_Event ev;
           SDL_TimerID timer_id;

           SDL_SetMainReady();
           if (SDL_Init(SDL_INIT_VIDEO|SDL_INIT_EVENTS|SDL_INIT_TIMER) != 0) {
             fputs(SDL_GetError(), stderr);
             return 1
           }

           if (!(win = SDL_CreateWindow("RPG", SDL_WINDOWPOS_UNDEFINED, SDL_WINDOWPOS_UNDEFINED, DISPLAY_W, DISPLAY_H, SDL_WINDOW_SHOWN))) {
             fputs(SDL_GetError(), stderr);
             return 1
           }

           if (!(ren = SDL_CreateRenderer(win, -1, SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC))) {
             fputs(SDL_GetError(), stderr);
             return 1
           }

           SDL_EventState(SDL_MOUSEMOTION, SDL_DISABLE);

           timer_id = SDL_AddTimer(30, timer_func, NULL);

           while (SDL_WaitEvent(&e)) {

             if (e.type == SDL_QUIT) break;

             SDL_RenderClear(ren);
           }

           SDL_RemoveTimer(timer_id);
           SDL_DestroyRenderer(ren);
           SDL_DestroyWindow(win);
           SDL_Quit();

           return 0;
         }

   .. code-tab:: c Allegro 5

         #include <stdio.h>
         #include <tmx.h>
         #include <allegro5/allegro.h>

         #define DISPLAY_H 480
         #define DISPLAY_W 640

         int main(int argc, char **argv) {
           ALLEGRO_DISPLAY *display = NULL;
           ALLEGRO_TIMER *timer = NULL;
           ALLEGRO_EVENT_QUEUE *equeue = NULL;
           ALLEGRO_EVENT ev;

           if (!al_init()) {
             fputs("Cannot initialise the Allegro library", stderr);
             return 1;
           }

           display = al_create_display(DISPLAY_W, DISPLAY_H);
           if (!display) {
             fputs("Cannot create a display", stderr);
             return 1;
           }

           equeue = al_create_event_queue();
           if (!equeue) {
             fputs("Cannot create an event queue", stderr);
             return 1;
           }

           timer = al_create_timer(1.0/30.0);
           if (!timer) {
             fputs("Cannot create a timer", stderr);
             return 1;
           }

           al_register_event_source(equeue, al_get_display_event_source(display));
           al_register_event_source(equeue, al_get_timer_event_source(timer));

           while(al_wait_for_event(equeue, &ev), 1) {

             if (ev.type == ALLEGRO_EVENT_DISPLAY_CLOSE) break;

             al_clear_to_color(al_map_rgb(0,0,0));
           }

           al_destroy_timer(timer);
           al_destroy_event_queue(equeue);
           al_destroy_display(display);

           return 0;
         }

   .. code-tab:: c raylib

         #include <stdio.h>
         #include <tmx.h>
         #include <raylib.h>

         #define DISPLAY_H 480
         #define DISPLAY_W 640

         int main(int argc, char **argv) {
           InitWindow(DISPLAY_W, DISPLAY_H, "RPG");
           if (!IsWindowReady()) {
             fputs("Cannot create a window", stderr);
           }

           SetTargetFPS(30);

           while (!WindowShouldClose()) {
             BeginDrawing();
             ClearBackground(BLACK);
             EndDrawing();
           }

           CloseWindow();

           return 0;
         }


Now we can load the map and check for errors. **libTMX** offers :ref:`several means to load a map <load-functions>`,
we will be using the simple :c:func:`tmx_load` function.

.. code-block:: c

   tmx_map *map = tmx_load(argv[1]);
   if (map == NULL) {
     tmx_perror("Cannot load map");
     return 1;
   }

The :c:func:`tmx_load` function returns a pointer to a :doc:`datastructure <datastructure>` that contains all the map
informations.

Now the map is loaded, except it is not entirely loaded, we need to load all the images referenced by the map.
We could navigate the map :doc:`datastructure <datastructure>`, and for every image we meet, load it. But libTMX offers
an easier way to do that without the hassle: :ref:`callback functions <image-autoload-autofree>`.

| **libTMX** can use two callback functions to delegate the image loading and image freeing to your library/engine.
| One callback to load: :c:data:`tmx_img_load_func`.
| One callback to free: :c:data:`tmx_img_free_func`.
| These callbacks **must be set BEFORE you call any load function**.

.. tabs::

   .. code-tab:: c SDL 2

         void* SDL_tex_loader(const char *path) {
           return IMG_LoadTexture(ren, path);
         }

         /* Set the callback globs in the main function */
         tmx_img_load_func = SDL_tex_loader;
         tmx_img_free_func = (void (*)(void*))SDL_DestroyTexture;

         tmx_map *map = tmx_load(argv[1]);
         /* ... */
         tmx_map_free(map);

   .. code-tab:: c Allegro 5

         void* Allegro5_tex_loader(const char *path) {
           ALLEGRO_BITMAP *res    = NULL;
           ALLEGRO_PATH   *alpath = NULL;

           if (!(alpath = al_create_path(path))) return NULL;

           al_set_new_bitmap_format(ALLEGRO_PIXEL_FORMAT_ANY_WITH_ALPHA);
           res = al_load_bitmap(al_path_cstr(alpath, ALLEGRO_NATIVE_PATH_SEP));

           al_destroy_path(alpath);

           return (void*)res;
         }

         /* Set the callback globs in the main function */
         tmx_img_load_func = Allegro5_tex_loader;
         tmx_img_free_func = (void (*)(void*))al_destroy_bitmap;

         tmx_map *map = tmx_load(argv[1]);
         /* ... */
         tmx_map_free(map);

   .. code-tab:: c raylib

         void* raylib_tex_loader(const char *path) {
           Texture2D texture = LoadTexture(path);
           Texture2D *returnValue = malloc(sizeof(Texture2D));
           memcpy(returnValue, &texture, sizeof(Texture2D));
           return returnValue;
         }

         void raylib_free_tex(void *ptr)
         {
           UnloadTexture(*((Texture2D*)ptr));
           free(ptr);
         }

         /* Set the callback globs in the main function */
         tmx_img_load_func = raylib_tex_loader;
         tmx_img_free_func = raylib_free_tex;

         tmx_map *map = tmx_load(argv[1]);
         /* ... */
         tmx_map_free(map);

.. note::
   It is better to load the image into a :term:`Texture` that can be drawn directly without overhead.


Rendering
---------

In the datastructure, all the :term:`layers <Layer>` are stored in a :term:`Linked List` ordered from background
to foreground, to make it easier to draw these layers in the correct order:

.. code-block:: c

   void draw_all_layers(tmx_map *map, tmx_layer *layers) {
     while (layers) {
       if (layers->visible) {

         if (layers->type == L_GROUP) {
           draw_all_layers(map, layers->content.group_head); // recursive call
         }
         else if (layers->type == L_OBJGR) {
           draw_objects(layers->content.objgr); // Function to be implemented
         }
         else if (layers->type == L_IMAGE) {
           draw_image_layer(layers->content.image); // Function to be implemented
         }
         else if (layers->type == L_LAYER) {
           draw_layer(map, layers); // Function to be implemented
         }
       }
       layers = layers->next;
     }
   }

Image Layers
^^^^^^^^^^^^

Foo.

.. tabs::

   .. code-tab:: c SDL 2

      void draw_image_layer(tmx_image *img) {
        SDL_Rect dim;

        dim.x = dim.y = 0;
        SDL_QueryTexture((SDL_Texture*)img->resource_image, NULL, NULL, &(dim.w), &(dim.h));

        SDL_RenderCopy(ren, (SDL_Texture*)img->resource_image, NULL, &dim);
      }

   .. code-tab:: c Allegro 5

      void draw_image_layer(tmx_image *image, float opacity) {
        ALLEGRO_BITMAP *bitmap = (ALLEGRO_BITMAP*)layers->content.image->resource_image;
        ALLEGRO_COLOR tint = al_map_rgba_f(op, op, op, op);
        al_draw_tinted_bitmap(bitmap, tint, 0, 0, 0);
      }

   .. code-tab:: c raylib

      void draw_image_layer(tmx_image *image, float opacity) {
        ;
      }


Tile layers
^^^^^^^^^^^

Bar.

.. tabs::

   .. code-tab:: c SDL 2

      void draw_layer(tmx_map *map, tmx_layer *layer) {
        ;
      }

   .. code-tab:: c Allegro 5

      void draw_layer(tmx_map *map, tmx_layer *layer) {
        ;
      }

   .. code-tab:: c raylib

      void draw_layer(tmx_map *map, tmx_layer *layer) {
        ;
      }


Object layers
^^^^^^^^^^^^^

Baz.

.. tabs::

   .. code-tab:: c SDL 2

      void draw_objects(tmx_object_group *objgr) {
        ;
      }

   .. code-tab:: c Allegro 5

      void draw_objects(tmx_object_group *objgr) {
        ;
      }

   .. code-tab:: c raylib

      void draw_objects(tmx_object_group *objgr) {
        ;
      }
