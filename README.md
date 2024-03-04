# Using Generative AI (ChatGPT v4) to create an "instant" ArcGIS web application

## Find the video accompanying this code at https://youtu.be/oRB0SiIIH0o

ArcGIS Online has many no-code options for deploying applications. With minimal understanding of coding, you can use Generative AI (Like ChatGPT v4 in this example) to create an application for you! Here is the full journey of how the code is generated, and a last prompt that demonstrates how when the prompts get more complicated it asks you to do more of the work. 

## My Prompt to ChatGPT

Using this map service from ArcGIS Online "https://services.arcgis.com/As5CFN3ThbQpy8Ph/ArcGIS/rest/services/Great_Lakes_Bathymetry/FeatureServer/0" create a web page to display the map of the great lakes using the latest ArcGIS JavaScript API v4

## ChatGPT (Response to Me)

To display a map of the Great Lakes using the ArcGIS Online service you provided with the latest ArcGIS JavaScript API version 4, you will need to write HTML and JavaScript code. Below is a basic example of how to do this. This code will create a simple web page that loads and displays the Great Lakes Bathymetry map layer on a base map.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Great Lakes Bathymetry Map</title>
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no" />
    <link rel="stylesheet" href="https://js.arcgis.com/4.24/esri/themes/light/main.css" />
    <script src="https://js.arcgis.com/4.24/"></script>

    <script>
      require([
        "esri/Map",
        "esri/views/MapView",
        "esri/layers/FeatureLayer"
      ], function(Map, MapView, FeatureLayer) {

        var layer = new FeatureLayer({
          url: "https://services.arcgis.com/As5CFN3ThbQpy8Ph/ArcGIS/rest/services/Great_Lakes_Bathymetry/FeatureServer/0"
        });

        var map = new Map({
          basemap: "topo-vector",
          layers: [layer]
        });

        var view = new MapView({
          container: "viewDiv",
          map: map,
          center: [-84.5, 44.5], // Longitude, latitude
          zoom: 6
        });
      });
    </script>
  </head>

  <body>
    <div id="viewDiv" style="height: 100vh; width: 100vw;"></div>
  </body>
</html>
```

Here's a breakdown of what this code does:
- Includes the ArcGIS API for JavaScript and its stylesheet by linking to the hosted library and CSS file.
- The `require` function is used to load modules needed to create a map, view, and feature layer.
- A `FeatureLayer` is created using the URL you provided, which points to the Great Lakes Bathymetry service.
- A `Map` object is created with a "topo-vector" basemap and the Great Lakes Bathymetry layer.
- A `MapView` is set up to display the map within a `<div>` element in the HTML body. The center and zoom level are set to focus on the Great Lakes.

To use this code, simply copy it into an HTML file and open that file in a web browser. The map should display centered on the Great Lakes with the bathymetry data visible. Adjust the `center` and `zoom` properties in the `MapView` constructor to change the initial view of the map as needed.

## Me

Can you add a basemap selector?

## ChatGPT

Certainly! Adding a basemap selector (or basemap gallery) to your web page allows users to switch between different basemaps provided by ArcGIS. This feature enhances the user experience by offering various geographical contexts for your data. Below is an updated version of the previous code, now including a basemap gallery.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Great Lakes Bathymetry Map with Basemap Selector</title>
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no" />
    <link rel="stylesheet" href="https://js.arcgis.com/4.24/esri/themes/light/main.css" />
    <script src="https://js.arcgis.com/4.24/"></script>

    <script>
      require([
        "esri/Map",
        "esri/views/MapView",
        "esri/layers/FeatureLayer",
        "esri/widgets/BasemapGallery"
      ], function(Map, MapView, FeatureLayer, BasemapGallery) {

        var layer = new FeatureLayer({
          url: "https://services.arcgis.com/As5CFN3ThbQpy8Ph/ArcGIS/rest/services/Great_Lakes_Bathymetry/FeatureServer/0"
        });

        var map = new Map({
          basemap: "topo-vector",
          layers: [layer]
        });

        var view = new MapView({
          container: "viewDiv",
          map: map,
          center: [-84.5, 44.5], // Longitude, latitude
          zoom: 6
        });

        var basemapGallery = new BasemapGallery({
          view: view,
          container: document.createElement("div")
        });

        view.ui.add(basemapGallery, {
          position: "top-right"
        });
      });
    </script>
  </head>

  <body>
    <div id="viewDiv" style="height: 100vh; width: 100vw;"></div>
  </body>
</html>
```

Changes and additions to the original code include:
- Added the `esri/widgets/BasemapGallery` module to the `require` function to use the BasemapGallery widget.
- Created a `BasemapGallery` widget that allows users to select from available basemaps.
- The `BasemapGallery` widget is associated with the `view` and is placed in the top-right corner of the map view using `view.ui.add`.

This code will add a basemap selector to your web page, enabling users to switch between different basemaps while viewing the Great Lakes Bathymetry layer. The basemap gallery widget automatically provides a selection of basemaps from ArcGIS, and users can click on it to choose a different basemap.

## My Prompt to ChatGPT

Can you add a UI to zoom to each of the great lakes?

## ChatGPT

To add a user interface (UI) that allows users to zoom to each of the Great Lakes, you can create a set of buttons and use the `MapView`'s `goTo` method to zoom the map to predefined extents for each lake. Below is the updated code that includes this functionality:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Great Lakes Bathymetry Map with Zoom Controls</title>
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no" />
    <link rel="stylesheet" href="https://js.arcgis.com/4.24/esri/themes/light/main.css" />
    <script src="https://js.arcgis.com/4.24/"></script>

    <style>
        .zoom-button {
            padding: 10px;
            margin: 5px;
            background-color: #0079c1;
            color: white;
            border: none;
            cursor: pointer;
        }
        .zoom-button:hover {
            background-color: #005a8b;
        }
    </style>

    <script>
        require([
            "esri/Map",
            "esri/views/MapView",
            "esri/layers/FeatureLayer",
            "esri/widgets/BasemapGallery"
        ], function(Map, MapView, FeatureLayer, BasemapGallery) {

            var layer = new FeatureLayer({
                url: "https://services.arcgis.com/As5CFN3ThbQpy8Ph/ArcGIS/rest/services/Great_Lakes_Bathymetry/FeatureServer/0"
            });

            var map = new Map({
                basemap: "topo-vector",
                layers: [layer]
            });

            var view = new MapView({
                container: "viewDiv",
                map: map,
                center: [-84.5, 44.5], // Longitude, latitude
                zoom: 6
            });

            var basemapGallery = new BasemapGallery({
                view: view,
                container: document.createElement("div")
            });

            view.ui.add(basemapGallery, {
                position: "top-right"
            });

            // Great Lakes coordinates and zoom levels
            var lakes = {
                "Superior": { center: [-85.5, 47.5], zoom: 6 },
                "Michigan": { center: [-87.0, 44.0], zoom: 6 },
                "Huron": { center: [-82.4, 44.5], zoom: 6 },
                "Erie": { center: [-81.2, 42.2], zoom: 7 },
                "Ontario": { center: [-77.5, 43.6], zoom: 7 }
            };

            // Add zoom buttons
            Object.keys(lakes).forEach(function(lake) {
                var button = document.createElement("button");
                button.className = "zoom-button";
                button.textContent = "Zoom to " + lake;
                button.onclick = function() {
                    view.goTo({
                        center: lakes[lake].center,
                        zoom: lakes[lake].zoom
                    });
                };

                view.ui.add(button, "top-left");
            });
        });
    </script>
</head>
<body>
    <div id="viewDiv" style="height: 100vh; width: 100vw;"></div>
</body>
</html>
```

Here's what was added to the original code:
- A `style` section to add some basic styling to the zoom buttons.
- A `lakes` object containing the center coordinates and zoom levels for each of the Great Lakes.
- A loop that creates a button for each lake, sets its text, and adds an `onclick` event that uses the `goTo` method to zoom the map to the specified lake.

This code will create a series of buttons on the top left of the map view, each labeled with the name of a Great Lake. When a button is clicked, the map will zoom to the pre-defined extent for that lake. You can adjust the center coordinates and zoom levels in the `lakes` object to better fit the extents of each lake as desired.

## My Prompt to ChatGPT

Create another webpage to include this already created page. On that other page give information about the depths of the great lakes with the map as a key element within it. 

## ChatGPT

To create a new webpage that includes the map page you've already created and provides information about the depths of the Great Lakes, you can use an `iframe` to embed the map page within the new page. Below is an example of how you could structure this new webpage. For the sake of this example, let's assume the map page you created is saved as `great_lakes_map.html`.

### New Webpage Code

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Great Lakes Depths Information</title>
    <style>
        body {
            font-family: Arial, sans-serif;
        }
        .container {
            max-width: 1200px;
            margin: auto;
            padding: 20px;
        }
        .map-iframe {
            width: 100%;
            height: 500px;
            border: 1px solid #ccc;
            margin-bottom: 20px;
        }
        h1, h2 {
            color: #005a8b;
        }
        .lake-info {
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Great Lakes Depths Information</h1>
        <iframe src="great_lakes_map.html" class="map-iframe"></iframe>
        <div class="lake-info">
            <h2>Lake Superior</h2>
            <p>Lake Superior is the largest and deepest of the Great Lakes. It has an average depth of 483 feet (147 meters), with its deepest point at 1,333 feet (406 meters).</p>
        </div>
        <div class="lake-info">
            <h2>Lake Michigan</h2>
            <p>Lake Michigan is the second-largest of the Great Lakes by volume and the third-largest by surface area. It has an average depth of 279 feet (85 meters), with its deepest point at 923 feet (281 meters).</p>
        </div>
        <div class="lake-info">
            <h2>Lake Huron</h2>
            <p>Lake Huron is the third largest of the Great Lakes. It has an average depth of 195 feet (59 meters), with its deepest point at 750 feet (229 meters).</p>
        </div>
        <div class="lake-info">
            <h2>Lake Erie</h2>
            <p>Lake Erie is the fourth largest of the Great Lakes by surface area and the smallest by volume. It has an average depth of 62 feet (19 meters), with its deepest point at 210 feet (64 meters).</p>
        </div>
        <div class="lake-info">
            <h2>Lake Ontario</h2>
            <p>Lake Ontario is the smallest of the Great Lakes in surface area and the second smallest in volume. It has an average depth of 283 feet (86 meters), with its deepest point at 802 feet (244 meters).</p>
        </div>
    </div>
</body>
</html>
```

### How This Works:

- The `<style>` section defines some basic CSS to make the page more visually appealing.
- The `<iframe>` element with the class `map-iframe` is used to embed the previously created map page (`great_lakes_map.html`). You'll need to ensure the `src` attribute of the `iframe` points to the correct location of your map page.
- The information about the depths of each Great Lake is organized into separate `<div>` elements with the class `lake-info`, making it easy to read and style.

Ensure the `src` attribute in the `<iframe>` tag correctly points to the location of your map page. If the map page is in a different directory, you'll need to adjust the path accordingly.

## My Prompt to ChatGPT

Can you make the lake info headings clickable to zoom to the lake on the map?

## ChatGPT

To make the lake info headings clickable and zoom to the respective lake on the map within the `iframe`, you'll need to establish a way for the parent page (the one with the lake information) to communicate with the child `iframe` (the map page). One common approach is to use the `postMessage` API for cross-document messaging.

First, ensure your map page (the one inside the `iframe`) can handle messages and zoom to the appropriate lake when a message is received. You'll need to modify the map page (`great_lakes_map.html`) to listen for messages and act upon them.

### Modified Map Page JavaScript

Add this to the map page (`great_lakes_map.html`) within the `<script>` tag:

```javascript
window.addEventListener("message", (event) => {
    // Check if the message is from a trusted domain
    if (event.origin !== "YOUR_PARENT_PAGE_ORIGIN") return;

    const lake = event.data;
    const lakes = {
        "Superior": { center: [-85.5, 47.5], zoom: 6 },
        "Michigan": { center: [-87.0, 44.0], zoom: 6 },
        "Huron": { center: [-82.4, 44.5], zoom: 6 },
        "Erie": { center: [-81.2, 42.2], zoom: 7 },
        "Ontario": { center: [-77.5, 43.6], zoom: 7 }
    };

    if (lakes[lake]) {
        view.goTo({
            center: lakes[lake].center,
            zoom: lakes[lake].zoom
        });
    }
});
```

Replace `"YOUR_PARENT_PAGE_ORIGIN"` with the origin of your parent page (e.g., `"http://localhost"` if you're testing locally). This is important for security to ensure you only accept messages from trusted sources.

### Parent Page Modification

On the parent page, modify each lake info heading to include an `onclick` event that sends a message to the `iframe`. Here's an example of how you might adjust a heading:

```html
<h2 onclick="zoomToLake('Superior')">Lake Superior</h2>
```

And add this JavaScript function to the parent page:

```javascript
function zoomToLake(lakeName) {
    var iframe = document.querySelector('.map-iframe');
    iframe.contentWindow.postMessage(lakeName, "URL_OF_YOUR_MAP_PAGE");
}
```

Replace `"URL_OF_YOUR_MAP_PAGE"` with the URL where your map page is hosted.

### Full Example for One Lake Heading

Incorporating the above, here's how a section of your parent page might look for Lake Superior:

```html
<div class="lake-info">
    <h2 onclick="zoomToLake('Superior')">Lake Superior</h2>
    <p>Lake Superior is the largest and deepest of the Great Lakes...</p>
</div>

<script>
function zoomToLake(lakeName) {
    var iframe = document.querySelector('.map-iframe');
    iframe.contentWindow.postMessage(lakeName, "URL_OF_YOUR_MAP_PAGE");
}
</script>
```

Repeat the process for the headings of each lake. Ensure your map page and parent page are set up to allow for proper communication and that your environment is configured to test these interactions, especially considering the same-origin policy and CORS (Cross-Origin Resource Sharing) restrictions.

## My Prompt to ChatGPT

Create me the full HTML for the parent page please

## ChatGPT

Below is the full HTML for the parent page that includes clickable lake info headings to zoom into each lake on the map within an `iframe`. Make sure to replace `"URL_OF_YOUR_MAP_PAGE"` in the `zoomToLake` function with the actual URL of your map page, and adjust the origin check in the map page's `addEventListener` script to match the origin of this parent page for security reasons.

### Parent Page HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Great Lakes Depths Information</title>
    <style>
        body {
            font-family: Arial, sans-serif;
        }
        .container {
            max-width: 1200px;
            margin: auto;
            padding: 20px;
        }
        .map-iframe {
            width: 100%;
            height: 500px;
            border: 1px solid #ccc;
            margin-bottom: 20px;
        }
        h1, h2 {
            color: #005a8b;
            cursor: pointer;
        }
        .lake-info {
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Great Lakes Depths Information</h1>
        <iframe src="great_lakes_map.html" class="map-iframe"></iframe>
        <div class="lake-info">
            <h2 onclick="zoomToLake('Superior')">Lake Superior</h2>
            <p>Lake Superior is the largest and deepest of the Great Lakes. It has an average depth of 483 feet (147 meters), with its deepest point at 1,333 feet (406 meters).</p>
        </div>
        <div class="lake-info">
            <h2 onclick="zoomToLake('Michigan')">Lake Michigan</h2>
            <p>Lake Michigan is the second-largest of the Great Lakes by volume and the third-largest by surface area. It has an average depth of 279 feet (85 meters), with its deepest point at 923 feet (281 meters).</p>
        </div>
        <div class="lake-info">
            <h2 onclick="zoomToLake('Huron')">Lake Huron</h2>
            <p>Lake Huron is the third largest of the Great Lakes. It has an average depth of 195 feet (59 meters), with its deepest point at 750 feet (229 meters).</p>
        </div>
        <div class="lake-info">
            <h2 onclick="zoomToLake('Erie')">Lake Erie</h2>
            <p>Lake Erie is the fourth largest of the Great Lakes by surface area and the smallest by volume. It has an average depth of 62 feet (19 meters), with its deepest point at 210 feet (64 meters).</p>
        </div>
        <div class="lake-info">
            <h2 onclick="zoomToLake('Ontario')">Lake Ontario</h2>
            <p>Lake Ontario is the smallest of the Great Lakes in surface area and the second smallest in volume. It has an average depth of 283 feet (86 meters), with its deepest point at 802 feet (244 meters).</p>
        </div>
    </div>

    <script>
    function zoomToLake(lakeName) {
        var iframe = document.querySelector('.map-iframe');
        iframe.contentWindow.postMessage(lakeName, "URL_OF_YOUR_MAP_PAGE");
    }
    </script>
</body>
</html>
```

### Notes:

- When clicking on a lake name, the `zoomToLake` function sends a message to the `iframe` containing the map. This message is the name of the lake, which the map page will use to determine where to zoom.
- Ensure the map page (likely `great_lakes_map.html`) is properly set up to listen for messages and zoom to the appropriate lake. The JavaScript snippet provided in the previous message should be added to your map page for this functionality.
- The `URL_OF_YOUR_MAP_PAGE` in the `zoomToLake` function should be replaced with the actual URL where your map page is hosted. This is important for the `postMessage` API to work correctly and securely.
- The `cursor: pointer;` style for `h1, h2` makes it clear to users that these headings are clickable.
- Remember to adjust the origin check in the map page's message listener to the actual origin of the parent page to ensure secure communication between the pages.

## My Prompt to ChatGPT

The error "Uncaught DOMException: Failed to execute 'postMessage' on 'Window': Invalid target origin 'greatlakes.html' in a call to 'postMessage'.
    at zoomToLake (http://localhost/agol/lakesinfo.html:60:30)
    at HTMLHeadingElement.onclick (http://localhost/agol/lakesinfo.html:40:50)" appears. How can I fix this?

## ChatGPT

The error you're encountering indicates an issue with the `postMessage` call in your `zoomToLake` function. Specifically, the second argument of `postMessage` must be the target origin (the scheme, hostname, and port of the URL) where the message is intended to be sent. It cannot be a path like `'great_lakes_map.html'`. Instead, it should be the full origin of the page, such as `'http://localhost'` if you're working locally, or the full URL to where your map page is hosted if it's on a server.

Here's how to fix the issue:

1. **Use a proper origin or `'*'` (not recommended for production due to security reasons):** The second parameter of `postMessage` should be the origin of the target window. For local testing, it might be `'http://localhost'` or `'http://localhost:PORT'` if you're using a specific port. For a live website, it would be something like `'https://yourwebsite.com'`.

   For local development, your function might look like this:

   ```javascript
   function zoomToLake(lakeName) {
       var iframe = document.querySelector('.map-iframe');
       iframe.contentWindow.postMessage(lakeName, "http://localhost"); // Adjust the URL as per your local or live environment
   }
   ```

2. **Adjust for dynamic origins:** If your page could be served from multiple locations (like different environments for development, testing, and production), you can make the origin dynamic:

   ```javascript
   function zoomToLake(lakeName) {
       var iframe = document.querySelector('.map-iframe');
       var targetOrigin = iframe.src.split('/').slice(0, 3).join('/'); // This extracts the origin from the iframe's src
       iframe.contentWindow.postMessage(lakeName, targetOrigin);
   }
   ```

This approach extracts the scheme, hostname, and port from the `src` of the `iframe` and uses it as the target origin for `postMessage`. This way, you don't have to hard-code the origin, making your code more flexible and secure.

**Note:** Using `'*'` as the target origin (meaning "no preference" for the target origin) in `postMessage` is generally not recommended for production environments due to security concerns, as it allows any website to receive the message. It's better to specify the exact target origin whenever possible.

## My Prompt to ChatGPT

The iframe of the parent page has scroll bars in it. How can I remove those?

## ChatGPT

To remove the scroll bars from an `iframe` in your parent page, you can apply some CSS styles. Specifically, you can set the `overflow` property to `hidden` on the `iframe`. Additionally, ensuring the `iframe` dimensions are large enough to accommodate the content without needing to scroll can help prevent scroll bars from appearing.

Here's how you can adjust your existing CSS for the `.map-iframe` class to remove the scroll bars:

```css
.map-iframe {
    width: 100%; /* Adjust the width as needed */
    height: 500px; /* Adjust the height as needed to fit the content */
    border: 1px solid #ccc;
    margin-bottom: 20px;
    overflow: hidden; /* This will hide the scroll bars */
}
```

Add the `overflow: hidden;` line to your `.map-iframe` class in the parent page's `<style>` section. This will hide any scroll bars in the `iframe`.

If the content within the `iframe` is still causing overflow, you might need to adjust the `height` and `width` properties to ensure the `iframe` is large enough to display its entire content without needing to scroll.

Keep in mind that hiding scroll bars might cause parts of the `iframe` content to become inaccessible if the `iframe` isn't large enough to display everything. It's important to balance the size of the `iframe` and the size of its content to ensure a good user experience.

## My Prompt to ChatGPT

There are still scroll bars shown, perhaps they are coming from the map page?

## ChatGPT

If scroll bars still appear even after setting `overflow: hidden;` on the `iframe` in the parent page, it's possible that the content within the `iframe` (the map page) inherently has overflow causing scroll bars to appear. To address this, you'll need to ensure that the content within the `iframe` is styled to prevent overflow or that the `iframe` itself is sized to accommodate the content without scroll bars.

Here's what you can do:

1. **Adjust the `iframe` Size:** First, try adjusting the size of the `iframe` to make sure it's large enough to contain the map without needing scroll bars. Increase the `height` and `width` in the CSS for `.map-iframe` in the parent page.

2. **Style the Map Page:** If adjusting the `iframe` size doesn't help or if you want to ensure the map page doesn't cause overflow, you'll need to adjust the CSS on the map page itself. Make sure the map container div in the map page is styled to fit within the page without causing overflow. For example, you might need to ensure that the body and html elements of the map page have `margin: 0;` and `padding: 0;` to eliminate any default spacing that could cause overflow:

    ```css
    /* Add this to the map page's CSS */
    html, body {
        height: 100%;
        margin: 0;
        padding: 0;
        overflow: hidden; /* Hide overflow */
    }
    ```

    Also, ensure the map container itself is not larger than the viewport or has `overflow: hidden` applied.

3. **Inspect Element:** If you're still seeing scroll bars, use your browser's Developer Tools to inspect the elements within the `iframe` and identify which element is causing the overflow. Right-click the content within the `iframe` and select "Inspect" or "Inspect Element" to open the Developer Tools, and look for elements that extend beyond the intended bounds or have scroll bars. Adjust the CSS for those elements as needed.

By carefully adjusting the sizes and overflow properties of the elements within the `iframe` and the `iframe` itself, you should be able to eliminate unwanted scroll bars.
