---
title: Getting Started with AEM Sites Chapter 3 - Client-Side Libraries and Responsive Grid
seo-title: Getting Started with AEM Sites Chapter 3 - Client-Side Libraries and Responsive Grid
description: Covers creation of AEM Client-Side Libraries or clientlibs to deploy and manage CSS and Javascript for an AEM Sites implementation. Integration with AEM's responsive grid and mobile emulator. aemfed module is used to accelerate front-end development.
seo-description: Covers creation of AEM Client-Side Libraries or clientlibs to deploy and manage CSS and Javascript for an AEM Sites implementation. Integration with AEM's responsive grid and mobile emulator. aemfed module is used to accelerate front-end development.
uuid: c0692b1e-1a9d-4c16-bf10-12bf3e376079
products: SG_EXPERIENCEMANAGER/6.5/SITES
products: SG_EXPERIENCEMANAGER/6.4/SITES
discoiquuid: aee38279-dffc-4540-96f3-41e3de059bb3
targetaudience: target-audience new
mini-toc-levels: 1
index: y
---

# Chapter 3 - Client-Side Libraries and Responsive Grid {#client-side-libraries-and-responsive-grid}

Covers creation of AEM Client-Side Libraries or clientlibs to deploy and manage CSS and Javascript for an AEM Sites implementation. Integration with AEM's responsive grid and mobile emulator. aemfed module is used to accelerate front-end development.

## Prerequisites {#prerequisites}

This is Chapter 3 of the multi-part tutorial. **[Chapter 2 can be found here](pages-templates.md)** and an **[overview can be found here](overview.md)**.

View the finished code on [GitHub](https://github.com/adobe/aem-guides-wknd) or download the finished package for the previous part of the tutorial: [WKND Chapter Solutions](https://github.com/adobe/aem-guides-wknd/releases/download/archetype-18.1/chapter-solutions.zip).

## Objective

1. Understand how Client-Side libraries are used to manage CSS and JavaScript assets.
2. How to integrate AEM's Responsive Grid to enable Layout Mode authoring of components.
3. Debugging and tools to accelerate front-end development.

## Front-end Frameworks {#front-end-workflow}

Below are some of the front-end technologies used in the creation of the WKND site.

**[LESS](https://lesscss.org/)** - popular CSS pre-compiler that allows variables and other functionality. AEM client libraries natively support LESS compilation. Saas or other pre-compilers can be used but would need to be compiled outside of AEM.

**[jQuery](https://jquery.com/)** - popular JavaScript feature-rich library for manipulating HTML

**[aemfed](https://aemfed.io/)** -  npm  module that leverages  **aemsync**  and **BrowserSync** to instantly deploy and reload changes to accelerate front-end development.

## Client-Side Libraries Structure {#clientlibs-structure}

Client-Side Libraries provides a mechanism to organize and manage CSS and JavaScript files necessary for an AEM Sites implementation. The basic goals for client libraries or clientlibs are:

1. Store CSS/JS in small discrete files for easier development and maintenance
2. Manage dependencies on 3rd party frameworks in an organized fashion
3. Minimize the number of client-side requests by concatenating CSS/JS into one or two requests
4. Minify CSS/JS that is delivered to optimize speed/performance of a site

More information about using [Client-Side Libraries can be found here.](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/clientlibs.html)

Client-Side Libraries provide many ways for organization, below is a convention used by the WKND site and WE.Retail sites and can be applied to most Sites implementations.

![High Level Clientlib Architecture](assets/chapter-3/wknd-clientlibs.png)

## Implement Site Client-side Library {#site-clientlibs}

We will now implement Client-Side libraries that contain styles and javascript that will be applied site wide. This includes some global styles and brand variables to create a consistent look and feel.

Creating a client library is similar to creating any other type of node in AEM. The basic structure includes:

```plain

[cq:ClientLibraryFolder] - jcr:primaryType
     - categories (String[])
     - embed (String[])
     - dependencies (String[])
     + css.txt (nt:file)
     + js.txt (nt:file)

```

There are a lot of nodes and files for the WKND site client libraries. To expedite the tutorial (and to avoid tedious steps) we have pre-created a base-line of styles for some of the foundational HTML elements.

Client Libraries are considered code and thus are stored in the **ui.apps** module beneath **/apps/wknd/clientlibs**.

1. The AEM project archetype creates **clientlib-base** and **clientlib-site** automatically. The below zip adds additional files and includes the source for the additional client libraries:

    * **clientlib-author**
    * **clientlib-dependencies**

2. Download **[chapter3-src-code-start.zip](assets/chapter-3/chapter3-src-code-start.zip)**
3. Unzip **chapter3-src-code-start.zip** directly inside your project on the file system beneath: `ui.apps/src/main/content/jcr_root/apps/wknd/clientlibs`.

    Make sure to only copy the folders within the zip archive. Overwrite the **clientlib-site** folder completely.

4. In the **ui.apps** module beneath `/apps/wknd/clientlibs` there should now be 4 sub-folders:

   ![four client library folders](assets/chapter-3/four-clientlib-folders.png)

   Below we will inspect the new client-side libraries of **clientlib-dependencies**, **clientlib-site**, and **clientlib-author**.

5. **clientlib-dependencies**

    This client-side library embeds some common AEM javascript dependencies that would be used for integrating analytics, ContextHub, [CSRF protection](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/csrf-protection.html) and other Adobe marketing cloud offerings. The version of jquery that ships with AEM (`/libs/clientlibs/granite/jquery`) will be used. You can download a newer version of jquery and create a new client library if needed.

    | Name | Type | Value | Description |
    |---   |---   |---    |---          |
    | jcr:primaryType | Name | **cq:ClientLibraryFolder** | *Defines a client library* |
    | allowProxy | Boolean | **true** | *Exposes CSS/JS via /etc.clientlibs* |
    | categories | String[] | **wknd.dependencies** | *Tag-like string that allows the clientlib to be referenced* |
    | embed | String[] | **jquery**, **granite.utils**, **granite.jquery**, **cq.jquery**, **granite.shared**,  **cq.shared**, **underscore** | *Embed jQuery and several Granite frameworks.* |

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <jcr:root xmlns:cq="https://www.day.com/jcr/cq/1.0" xmlns:jcr="https://www.jcp.org/jcr/1.0"
       jcr:primaryType="cq:ClientLibraryFolder"
       allowProxy="{Boolean}true"
       categories="[wknd.dependencies]"
       embed="[jquery,granite.utils,granite.jquery,cq.jquery,granite.shared,cq.shared,underscore]"/>

   ```

   >[!NOTE]
   >
   >**clientlib-dependencies** typically consists mostly of Javascript. However since these dependencies are needed for features like ContextHub they will be included in the at the top of the page in the HTML head.

6. **clientlib-site**

    This is the main client library for the WKND site. It includes site specific styles. It also includes two files named **variables.less** and **mixins.less** that establish some leverage features of LESS to ensure consistency.

    Two files **grid.less**, **main.less** are inspected below in more detail.

    | Name | Type | Value | Description |
    |---   |---   |---    |---          |
    | jcr:primaryType | Name | **cq:ClientLibraryFolder** | *Defines a client library* |
    | allowProxy | Boolean | **true** | *Exposes CSS/JS via /etc.clientlibs* |
    | categories | String[] | **wknd.site** | *Tag-like string that allows the clientlib to be referenced* |

    **main.less** - `/apps/wknd/clientlibs/clientlib-site/main.less`

    This is the entry point for all of the LESS styles in the client library. At the top of the file **variables.less** and **mixins.less** are imported to be able to share with the rest of the files. At the bottom of **main.less** you can see the inclusion of component specific styles. Notice the organization folder structure for the component specific files.The main.less also includeds a file named **grid.less** that will be responsible for creating AEM's responsive grid.

   ```css
   /* WKND main.less */

   /* Import variables */
   @import "less/variables.less";

   /* Import mixins */
   @import "less/mixins.less";

   /* Import Responsive Grid */
   @import "less/grid.less";

   /* Custom Icons for WKND site */
   @import "less/wkndicons.less";

   /* Page Styles at the top */
   @import "components/page/page.less";

   /* Component Styles */
   @import "components/breadcrumb/breadcrumb.less";
   @import "components/contentfragment/contentfragment.less";
   @import "components/image/image.less";
   @import "components/text/text.less";
   @import "components/title/title.less";

   ```

   **grid.less** - `/apps/wknd/clientlibs/clientlib-site/less/grid.less`

   Generates the AEM responsive grid that will allow content authors to use all the capabilities of the [AEM Layout Mode](https://helpx.adobe.com/experience-manager/6-4/sites/authoring/using/responsive-layout.html) and the Layout Container. It uses a custom mixin found in grid_base.less to generate the grid. Notice that the Phone and Tablet breakpoints use variables found in the **less/variables.less** file. These breakpoints match the breakpoints defined in the empty template type: `/conf/wknd/settings/wcm/template-types/empty-page/structure/jcr:content/cq:responsive/breakpoints`.

   ```css
    /* grid.less snippet */

    /* maximum amount of grid cells to be provided */
        @max_col: 12;

        /* default breakpoint */
        .aem-Grid {
            .generate-grid(default, @max_col);
            width: auto;
        }
        /* phone breakpoint */
        @media (max-width: @screen-small) {
            .aem-Grid {
                .generate-grid(phone, @max_col);
            }
        }
        /* tablet breakpoint */
        @media (min-width: (@screen-small + 1)) and (max-width: @screen-large) {
            .aem-Grid {
                .generate-grid(tablet, @max_col);
            }
        }

    ```

   >[!CAUTION]
   >
   >The location of the grid_base LESS file provided by AEM has changed from 6.3 to 6.4. If referencing the file directly in your own project ensure that the correct location is used:
   >
   >* **6.3** Path to Responsive Grid: `/etc/clientlibs/wcm/foundation/grid/grid_base.less`
   >* **6.4, 6.5+** Path to Responsive Grid: `/libs/wcm/foundation/clientlibs/grid/grid_base.less`

   Within **clientlib-site** you will also notice a folder named **webfonts**. This is a small client-side library that includes a call to Google webfonts. This has been split from the rest of **clientlib-site** in order to load this library at the top of the CSS file. In the next section we will embed this as part of **clientlib-base**.

7. **clientlib-author**

    As a best practice the code base for the Author environment should be nearly identical to the Publish environment. There are certain cases in which some extra CSS/JS is necessary to provide a better authoring experience. The WKND site has a fixed-header design and in the Editor this makes it difficult to select the Header component in edit mode. Therefore a small amount of CSS overrides will be stored in this client library and only loaded in the Author environment. This practice should be used sparingly and only when absolutely necessary.

    | Name | Type | Value | Description |
    |---   |---   |---    |---          |
    | jcr:primaryType | Name | **cq:ClientLibraryFolder** | *Defines a client library* |
    | allowProxy | Boolean | **true** | *Exposes CSS/JS via /etc.clientlibs* |
    | categories | String[] | **wknd.author** | *Tag-like string that allows the clientlib to be referenced* |

## Update Base Client-side library {#update-clientlib-base}

**clientlib-base**, which is automatically generated by the archetype,  represents the base level of CSS and JavaScript needed for the WKND site to function. This library won't include any CSS/JS files directly,  instead  it will include multiple client libraries via an **embed** property.

1. At `/apps/wknd/clientlibs/clientlib-base`, **update** the **embed** property. Include **wknd.webfonts** as the first embed. Note that **wknd.site** category (**clientlib-site**) is already at the end of the String array.

   >[!CAUTION]
   >
   >The order in which the **embed** array matters. It determines the order in which libraries are included. We want the webfonts to be loaded first and we want the site specific libraries (**wknd.site**) to be loaded last to ensure we can override styles inherited from core components.

    | Name | Type | Value | Description |
    |---   |---   |---    |---          |
    | jcr:primaryType | Name | **cq:ClientLibraryFolder** | *Defines a client library* |
    | allowProxy | `Boolean` | **true** | *Exposes CSS/JS via /etc.clientlibs* |
    | categories | `String[]` | **wknd.base** | *Tag-like string that allows the clientlib to be referenced* |
    | embed      | `String []` | **wknd.webfonts**, core.wcm.components.tabs.v1, core.wcm.components.carousel.v1, core.wcm.components.image.v2,core.wcm.components.breadcrumb.v2, core.wcm.components.search.v1, core.wcm.components.form.text.v2, wknd.breadcrumb, **wknd.site** | *Embeds the clientlibs by respective category. Include any CoreComponent clientlibs here. The **wknd.site** category embeds the clientlib-site library. **wknd.webfonts** is split and embedded at the top so that the webfonts are correctly imported at the top of the generated CSS file.* |

   ```xml

    <?xml version="1.0" encoding="UTF-8"?>
    <jcr:root xmlns:cq="http://www.day.com/jcr/cq/1.0" xmlns:jcr="http://www.jcp.org/jcr/1.0"
        jcr:primaryType="cq:ClientLibraryFolder"
        allowProxy="{Boolean}true"
        categories="[wknd.base]"
        embed="[wknd.webfonts,core.wcm.components.tabs.v1,core.wcm.components.carousel.v1,core.wcm.components.image.v2,core.wcm.components.breadcrumb.v2,core.wcm.components.search.v1,core.wcm.components.form.text.v2,wknd.breadcrumb,wknd.site]" />

   ```

   >[!NOTE]
   >
   > Several Core Components come with client libraries that need to be included on the page in order for the component to function properly. These have already been added via the **embed** property to **clientlib-base**.
   >
   > For example see `/apps/core/wcm/components/image/v2/image/clientlibs`.
   > There are two client libraries in this folder one named **editor** and another named **site**. The **editor** clientlib is meant just for the author environment and typically included by the component dialog. The **site** clientlib is the one that needs to be included in the project's clientlib in order for the component to behave properly. For each Core Component you can view the `README.md` to understand what each clientlib does: `/apps/core/wcm/components/image/v2/image/README.md`.

## Implement Header Styles {#header-styles}

Next we will implement some basic styles for the Header component. We want to make the header component behave as a "sticky" header and scroll with the page. We also want to resize the header to optimize for tablet/mobile views. LESS and JavaScript will be added to the **clientlib-site** library.

We will add a piece of javascript that will listen for a browser scroll and inject a CSS class into the body tag of the HTML page. We can then modify CSS based on this class change. We could have made this javascript specific to just the Header component, however we may want other scroll behavior across components. We will add this script with the global styles with the other page styles.

1. Beneath `/apps/wknd/clientlibs/clientlib-site/components/page` add a new file named **page.js**.

2. Populate **page.js** with the following:

   ```js

   /* JS Snippet to trigger class change on page scroll */

   (function (element, $) {
       'use strict';
       var target = $(element),
           className = "scrolly",
           scroll,
           mobileBreakpoint = 992;

       if($(window).scrollTop() > 0) {
           target.addClass(className);
       }

       $(window).scroll(function(){

            scroll = $(window).scrollTop();
       if(scroll > 0 ) {
           target.addClass(className);
       } else {
           target.removeClass(className);
       }
    });
   }('body',jQuery));

   ```

   The above function will add a CSS class named **"scrolly"** to the body tag whenever the scroll position changes form 0. This javascript depends on jQuery being loaded. The **clientlib-dependencies** library will include jQuery on the page.

3. Update `/apps/wknd/clientlibs/clientlib-site/js.txt` to include **page.js**. The js.txt and css.txt files in a client library act as a manifest for which files to be surfaced and the order in which they will be loaded. Simply adding a file in the client library is not enough to include it in library.

   Update of **js.txt**:

   ```plain

   #base=.

   components/page/page.js

   ```

4. Beneath `/apps/wknd/clientlibs/clientlib-site/components` create the following folder and file structure for the **header** styles:

   ```plain

   /apps/wknd/clientlibs/clientlib-site/components/
       +/header
           + header.less
           /styles
               + default.less

   ```

   ![header client-lib structure](assets/chapter-3/header-clientlib-structure.png)

5. Populate **header.less** to include the default style for the **Header** component:

   ```css

   /* WKND Header Styles */

   @import "styles/default.less";

   ```

   Populate **default.less** with the following:

   ```css

   /* WKND Header - default.less */

   .wknd-header {
    z-index: 1030;
    position: fixed;
    top: 0;
    height:92px;
    padding-top: 45px;
    left:0;
    right:0;

    .container {
     .container-content();
     max-width: 1116px;
    }

     @media (max-width: @screen-medium) {
        padding: 12px;
        top: 0;
        left: 0;
        right: 0;
        height: 60px;
        position: absolute;
    }

    a.wknd-header-logo {
        color: @text-color;
        font-weight: bolder;
        font-size: @font-size-xlarge;
        float:left;
        padding: 11px 12px;
        line-height: 28px;
        height: 50px;
        margin:0;
        padding-left: 0;
        text-decoration: none;
        text-align: left;

     @media (max-width: @screen-medium) {
      font-size: (@font-size-large + 8px);
      margin-left: 0;
      padding-top: 10px;
      padding-left: @gutter-padding;
      }
    }

    h1 {
     float: left;
     margin-top: 0.25em;
     font-size: 1.5em;
    }
   }

   .scrolly {
    .wknd-header {

     @media (min-width: (@screen-medium + 1)) {
      height: 80px;
      background-color: @body-bg;
      -webkit-box-shadow: 0px 8px 20px 0px rgba(0,0,0,0.26);
      -moz-box-shadow: 0px 8px 20px 0px rgba(0,0,0,0.26);
      box-shadow: 0px 8px 20px 0px rgba(0,0,0,0.26);
     }
    }
   }

   ```

   Notice that at the bottom of the file we add a box-shadow to the header div based on the addition of the **scrolly** class name. The **scrolly** class name will be populated based on the JavaScript added earlier when the page scrolls.

6. Lastly, update `/apps/wknd/clientlibs/clientlib-site/main.less` to include the **header.less** style:

   ```css

    /* main.less */

    ...

    /* Component Styles */
    @import "components/breadcrumb/breadcrumb.less";
    @import "components/contentfragment/contentfragment.less";
    /* add header.less */
    @import "components/header/header.less";
    @import "components/image/image.less";
    @import "components/layout-container/layout-container.less";
    @import "components/text/text.less";
    @import "components/title/title.less";

   ```

## Including Client-side libraries to the Page {#clientlibs-to-page}

Next the clientlibs needed to be included via the base page component. The Core Component page provides two files designed to include custom clientlibs in the html head for CSS and at the very bottom of the page for JavaScript. The AEM project archetype has already included the **wknd.base** client library.

Updates are needed to include **wknd.dependencies** and in the authoring environment **wknd.author**.

In the **ui.apps** module modify files beneath: `/apps/wknd/components/structure/page`:

1. Update `/apps/wknd/components/structure/page/customheaderlibs.html`:

    This will override `/apps/core/wcm/components/page/v2/page/customheaderlibs.html`.

   ```xml

   <!--/* /apps/wknd/components/structure/page/customheaderlibs.html */-->

   <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

   <sly data-sly-use.clientLib="/libs/granite/sightly/templates/clientlib.html"
        data-sly-call="${clientlib.js @ categories='wknd.dependencies'}"/>
   <sly data-sly-use.clientLib="/libs/granite/sightly/templates/clientlib.html"
        data-sly-call="${clientlib.css @ categories='wknd.base'}"/>

   <!--/* Load authoring related client libraries */-->
   <sly data-sly-test="${wcmmode.preview || wcmmode.edit}" data-sly-call="${clientLib.all @ categories='wknd.author'}"/>

   <!--/* Include Context Hub */-->
   <sly data-sly-resource="${'contexthub' @ resourceType='granite/contexthub/components/contexthub'}"/>

   ```

   This includes the CSS for the **wknd.base** client-side library and the JS for the **wknd.dependencies** client-side library. Typically it is a best practice to include JS at the bottom of the page. The wknd.dependencies JS is used for loading libraries that support targeted content like ContextHub and so in this case it is a best practice to load it at the top of the page. The author-specific client library **wknd.author** *is also included*.

   Notice the **wcmmode** conditional, this means that only if the page is open in the Author editing environment will this client library be loaded. More information about the [WCMMode can be found here.](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/SightlyWCMMode.html)

   ContextHub is a JS based framework for personalization, it is loaded in the header. This tutorial does not cover implementing ContextHub but more information about [ContextHub can be found here.](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/contexthub.html)

2. Inspect the file `/apps/wknd/components/structure/page/customfooterlibs.html`.

    This will be included at the very bottom of the page and will include the JS for **wknd.base** client library. It will override: `/apps/core/wcm/components/page/v2/page/customfooterlibs.html`.

    No changes are needed as the AEM project archetype created this file already.

   ```xml

   <!--/*
       /apps/wknd/components/structure/page/customfooterlibs.html
   */-->
   <sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
       <sly data-sly-call="${clientlib.js @ categories='wknd.base'}"/>
   </sly>

   ```

3. Deploy the code base to add both client libraries and updated page component to the local AEM instance with Eclipse dev tools or using your Maven skills.

4. Verify the inclusion of client-side libraries.

    Updating the base page component will update any pages created via the WKND Article Template.

    1. Open up a page created from the Article Template: [http://localhost:4502/editor.html/content/wknd/en/first-article.html](http://localhost:4502/editor.html/content/wknd/en/first-article.html). You should see the updated fonts and fixed width container.
    2. View the page as published by clicking the [!UICONTROL **Page Properties**] menu icon (upper left, next to the sidebar icon) `->` [!UICONTROL **View as Published**] button.
        ![view as published](assets/chapter-3/view-as-publish.png)

    3. This will open a new tab with **wcmmode=disabled** added as a URL parameter. Viewing the page with **wcmmode=disabled** is a great way to debug/develop as there is a significant amount of Javascript for the Sites editor to support component authoring. 

        [http://localhost:4502/content/wknd/en/first-article.html?wcmmode=disabled](http://localhost:4502/content/wknd/en/first-article.html?wcmmode=disabled)

    4. Viewing the page source you should see following tags:

   ```xml

   /* Source of http://localhost:4502/content/wknd/first-article.html?wcmmode=disabled */
   <html>
       <head>
       ...
           <script type="text/javascript" src="/etc.clientlibs/wknd/clientlibs/clientlib-dependencies.js"></script>
           <link rel="stylesheet" href="/etc.clientlibs/wknd/clientlibs/clientlib-base.css" type="text/css">
       </head>
       <body>
           ...
        <script type="text/javascript" src="/etc.clientlibs/wknd/clientlibs/clientlib-base.js"></script>
       </body>
   </html>

   ```

   The main takeaway is that the CSS and Javascript is dynamically loaded from a path that starts with **/etc.clientlibs**. The client libraries are stored beneath **/apps/wknd** to make it easier from an organizational standpoint as it is in the same directory as our component code. 

    >[!WARNING]
    >
    >It is critical on the publish side that the client libraries are **not** served from **/apps** as this path should be restricted for security reasons using the [Dispatcher filter section](https://docs.adobe.com/content/help/en/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html#example-filter-section). The [allowProxy property](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/clientlibs.html#main-pars_title_8ced) of the client library ensures the CSS and JS are served from **/etc.clientlibs**.

5. While viewing the page with **wcmmode=disabled** check that the scrolling header is working when the browser is sized **&gt; 992px**.

   ![scroll header](assets/chapter-3/scroll-header.png)

## Debugging Client Libraries {#debugging-clientlibs}

With different methods of **categories** and **embeds** to include multiple client libraries it can be cumbersome to troubleshoot. AEM exposes several tools to help with this. One of the most important tools is **Rebuild Client Libraries** which will force AEM to re-compile any LESS files and generate the CSS.

* [**Dump Libs**](http://localhost:4502/libs/granite/ui/content/dumplibs.html) - Lists all of the client libraries registered in the AEM instance. `<host>/libs/granite/ui/content/dumplibs.html`

* [**Test Output**](http://localhost:4502/libs/granite/ui/content/dumplibs.test.html) - allows a user to see the expected HTML output of clientlib includes based on category. `<host>/libs/granite/ui/content/dumplibs.test.html`

* [**Libraries Dependencies validation**](http://localhost:4502/libs/granite/ui/content/dumplibs.validate.html) - highlights any dependencies or embedded categories that cannot be found. `<host>/libs/granite/ui/content/dumplibs.validate.html`

* [**Rebuild Client Libraries**](http://localhost:4502/libs/granite/ui/content/dumplibs.rebuild.html) - allows a user to force AEM to rebuild all of the client libraries or invalidate the cache of client libraries. This tool is particularly effective when developing with LESS as this can force AEM to re-compile the generated CSS. In general it is more effective to Invalidate Caches and then perform a page refresh versus rebuilding all of the libraries. `<host>/libs/granite/ui/content/dumplibs.rebuild.html`

    ![rebuild client library](assets/chapter-3/rebuild-clientlibs.png)

## Developing with aemfed {#develop-aemfed}

[**aemfed**](https://aemfed.io/) is an open-source,  command-line  tool that can be used to speed up front-end development. It is powered by  [aemsync](https://www.npmjs.com/package/aemsync), [Browsersync](https://www.npmjs.com/package/browser-sync) and [Sling Log Tracer](https://sling.apache.org/documentation/bundles/log-tracers.html).

At a high level **aemfed** listens to file changes and automatically syncs the to a running AEM instance. Based on the changes, a local browser will automatically refresh, thereby speeding up front-end development. It also is built to work with Sling Log tracer to automatically display any server-side errors directly in the terminal. [Full documentation can be found here.](https://github.com/abmaonline/aemfed)

>[!NOTE]
>
>We will now walk through the steps of installing **aemfed** globally. For the latest and greatest instructions make sure to check the [official aemfed documentation](https://github.com/abmaonline/aemfed).

1. A key part of aemfed is the ability to relay errors directly to the command line. To enable this behavior we need to update the configuration for Sling Log Tracer.

   Navigate to [http://localhost:4502/system/console/configMgr](http://localhost:4502/system/console/configMgr) and search for **Apache Sling Log Tracer**. Update the configuration to **Enabled** and **Recording Servlet Enabled**:

   ![sling log tracer](assets/chapter-3/sling-log-tracer.png)

   >[!CAUTION]
   >
   >Apache Sling Log Tracer version 1.0.0+ should be used and should only be enabled in development and other non-production environments.

2. To install **aemfed** you will need **npm** installed available via the command-line:

   ```shell

   $ npm --version
   6.4+

   ```

   If you do not have **npm** installed, the easiest way is to download and install [Node.js](https://nodejs.org/en/download/).

3. Install **aemfed** globally with the following command:

   ```shell

   $ npm install aemfed --global

   # (aemfed is installed globally)

   $ aemfed -h
   Usage: aemfed [OPTIONS]
   Options:
     -t targets           Default is https://admin:admin@localhost:4502
     -p proxy_port        Default is 3000
     -w path_to_watch     Default is current
     -e exclude_filter    Anymatch exclude filter; disabled by default
     -i sync_interval     Update interval in milliseconds; default is 100
     -o open_page         Browser page to be opened after successful launch; default is "false".
     -b browser           Browser where page should be opened in; this parameter is platform dependent; for example, Chrome is "google chrome" on OS X, "google-chrome" on Linux and "chrome" on Windows; default is "google chrome"
     -h                   Displays this screen
     -v                   Displays version of this package

   ```

   >[!NOTE]
   >
   >This will allow us to run **aemfed** from any directory. See the [aemfed documentation](https://github.com/abmaonline/aemfed#installation) for instructions on how to add it to a project that uses **package.json**.

4. From the command line navigate to the `<src>/aem-guides-wknd` directory. Run the following command to start  **aemfed** against an  aem  instance running on **localhost:4502**:

   ```shell

   aemfed -t "http://admin:admin@localhost:4502" -w "ui.apps/src/main/content/jcr_root/"

   ---------------------------------------
   [Browsersync] Proxying: http://localhost:4502
   [Browsersync] Access URLs:
    --------------------------------------
          Local: https://localhost:3000
       External: https://192.168.1.152:3000
    --------------------------------------
             UI: https://localhost:3001
    UI External: https://localhost:3001
    --------------------------------------

   ```

5. Navigate using the proxy URL to the AEM page you have been working with: `http://localhost:3000/content/wknd/en/first-article.html?wcmmode=disabled`

   You should now see the same contents of AEM but proxied via port 3000.

6. Update some of the **.less** files like **header.less** to see the changes automatically reflected in the browser

   ![aemfed demo](assets/chapter-3/aemfed.gif)

## Authoring with Responsive Grid {#responsive-grid-use}

At this point everything needed to leverage [AEM's Responsive Grid](https://helpx.adobe.com/experience-manager/6-4/sites/authoring/using/responsive-layout.html) and Layout features is enabled. Try resizing a few components using different device widths to see this working.

1. Add a text component and use the Layout Mode to shrink it to half the page width. Repeat with a second text component to get 2 components side by side.

   >[!NOTE]
   >
   >Currently the layout takes the full width of the page and it may be a little difficult to find the handles. In the next chapter we will correct this with some styles to the Layout Container.

   ![resize text components](assets/chapter-3/text-resize-components.png)

   Populate the Text Component with some text.

   ![populated columns of text](assets/chapter-3/populatedcolumns.png)

2. Add 3 Image components to the page. Next resize each of them so that the three Image components equal 1/3 of the page width and sit side by side.

   ![three images](assets/chapter-3/three-images.png)

   Populate the images with some pictures.

   ![three images populated](assets/chapter-3/three-images-populated.png)

3. While in [Layout](https://helpx.adobe.com/experience-manager/6-4/sites/authoring/using/responsive-layout.html) mode use the emulator to preview the page in Mobile views. Notice that the columns simply shrink, creating a layout that is not optimized for the screen device.

   ![mobile emulator resize](assets/chapter-3/layout-mode-resize.png)

   Resize the components to make them stack vertically in Mobile view by expanding the width to full page width for each one.

   ![layout resize](assets/chapter-3/layout-resize.gif)

## Next Steps {#next-steps}

Next part in the tutorial: 

* [Getting Started with AEM Sites Chapter 4 - Developing with the Style System](style-system.md)

View the finished code on [GitHub](https://github.com/adobe/aem-guides-wknd) or download the finished package for this part of the tutorial: **[WKND Chapter Solutions](https://github.com/adobe/aem-guides-wknd/releases/download/archetype-18.1/chapter-solutions.zip)**
