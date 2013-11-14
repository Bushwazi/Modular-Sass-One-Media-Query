I create my folder structure and have a modules folder, but then once I get into the media queries, I find myself adding the addition layouts into a different file then the module file. I don't want to have media queries called all over my css and I don't want my module SASS broken into different files.

The solution I've decided to use is adding mixins into my module file and then calling them in another area, so then all the sass related to the module is in one file.

Is there a smarter solution?

Below is a basic breakdown to my solution

    sass
    -- base
    ---- [all base files]
    -- layout
    ---- desktop.scss
    ---- tablet.scss
    ---- mobile.scss
    -- modules
    ---- columns.scss
    ---- modulesDesktop.scss
    ---- modulesTablet.scss
    ---- modulesMobile.scss
    screen.scss

1) the module.scss (columns.scss) has the default sass plus the resizes as mixins.
2) the modules.scss (modulesDesktop, modulesTablet, modulesMobile) files in turn, call the mixins in the module.scss (columns.scss) files  
3) the layout.scss files (dekstop, tablet, mobile) have the actual media queries in them, in those media queries, I call the related modules.scss (modulesDesktop, modulesTablet, modulesMobile) file.  
4) `screen.scss` calls all the layout.scss files.  

SAMPLE MODULE *I just wrote this out as and example but I didn't QA this:

    /*
     * columns.scss
     * default styles => desktop first
     */
    .columns {
      overflow:hidden;
      .column {
        display:inline-block;
        float:left;
      }
    }
    .col-two .column {
      width:50%;
    }
    .col-three .column {
      width:33.33333%;
    }
    .col-four .column {
      width:25%;
    }
    @mixin columnsTablet(){
      /* tablet styles */
      .col-four .column {
        width:50%;
      }	
    }
    @mixin columnsMobile(){
      /* mobile styles */
      .columns {
        overflow:hidden;
        .column {
          float:none;
        }
      }
      .col-two .column,
      .col-three .column,
      .col-four .column {
        width:100%;
      }
    }

MODULE INDEXES:  
__DESKTOP__

    /*
     * modulesDesktop.scss
     * 
     */
    @import 'columns.scss'
    /* ... and any other modules */

__TABLET__

    /*
     * modulesTablet.scss
     * 
     */
    @include columnsTablet
    /* ... and any other tablet module mixins */

__MOBILE__

    /*
     * modulesMobile.scss
     * 
     */
    @include columnsMobile
    /* ... and any other mobile module mixins */

LAYOUT SASS:  
__DESKTOP__

    /* Normal css workflow ... */
    /*
     * MODULES
     */
    @import '../modules/modulesDesktop';
    /* ... Normal css workflow */

__TABLET__

    @media all and (max-width:960px){
      /* styles for 960px and smaller */
      /* Normal css workflow ... */
      /*
       * MODULES
       */
      @import '../modules/modulesTablet';
      /* ... Normal css workflow */
    }

__MOBILE__

    @media all and (max-width:600px){
      /* styles for 600px and smaller */
      /* Normal css workflow ... */
      /*
       * MODULES
       */
      @import '../modules/modulesMobile';
      /* ... Normal css workflow */
    }

and then `screen.scss` calls all the layouts and groups them into one file.