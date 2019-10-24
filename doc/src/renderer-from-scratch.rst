Creating a map renderer from scratch
====================================

In this article, you will learn how to create a Map renderer from scratch using different game programming libraries:
`SDL 2`_, `Allegro 5`_ and `raylib`_. In this article we will assume that you are already proficient with one of these
libraries, have a working version installed or are able to compile it by yourself.

We will be using a map from Tiled's examples: `rpg/island.tmx`_. That particular map features an single :term:`Tileset`
defined in its own :term:`TSX` file, several :term:`Layers <Layer>`, :term:`Objects <Object>` and animated
:term:`Tiles <Tile>`.

Initial set-up
--------------

Unfortunately as in any project we have to start with the usual boilerplate code to initialise libraries, create a
display (window), and the main loop.

.. tabs::

   .. code-tab:: c SDL 2

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

           timer = al_create_timer(1.0/30.0); /* 30 Frames per seconds */
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

         void raylib_renderer() {}



Testing the tab group functionality:

.. tabs::

   .. code-tab:: c SDL 2

         void SDL_tex_loader() {}

   .. code-tab:: c Allegro 5

         void Allegro5_tex_loader() {}

   .. code-tab:: c raylib

         void raylib_tex_loader() {}


.. _SDL 2: https://www.libsdl.org/
.. _Allegro 5: https://liballeg.org/
.. _raylib: https://www.raylib.com/
.. _rpg/island.tmx: https://github.com/bjorn/tiled/tree/master/examples/rpg