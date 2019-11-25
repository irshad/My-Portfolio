## Progressive Web Apps

# What is a PWA?
Originally intended for mobile devices (I say originally because there is now support for desktop PWAs), a PWA is a special type of mobile application built using traditional web technologies like HTML, CSS, and JavaScript. All modern browsers support PWAs. They are called “progressive” because fundamentally they behave just like an ordinary web page in your browser, but once installed can progressively add new features like interacting with hardware and managing push notifications. The bare minimum for a PWA is a manifest and a service worker.

# The Manifest
This is the manifest for Developer for Life.

`{
    "name": "Developer for Life",
    "short_name": "dev4life",
    "icons": [
        {
            "src": "/appicons/favicon-128.png",
            "sizes": "128x128",
            "type": "image/png"
        },
        {
            "src": "/appicons/apple-touch-icon-144x144.png",
            "sizes": "144x144",
            "type": "image/png"
        },
        {
            "src": "/appicons/apple-touch-icon-152x152.png",
            "sizes": "152x152",
            "type": "image/png"
        },
        {
            "src": "/appicons/favicon-196x196.png",
            "sizes": "196x196",
            "type": "image/png"
        },
        {
            "src": "/appicons/splash.png",
            "sizes": "512x512",
            "type": "image/png"
        }
    ],
    "start_url": "/",
    "display": "standalone",
    "orientation": "portrait",
    "background_color": "#FFFFFF",
    "theme_color": "#FFFFFF"
}`

It contains basic information such as what icons to display when the app is installed, colors to use, what the start page is and what the default orientation should be. It is installed at the root of your website. This link will download the manifest for Developer for Life: manifest.json.
Once your manifest is installed, you can view it in the developer tools under the “application” tab

# The Service Worker
The most critical part of a PWA is the associated service worker. This is a special JavaScript app that is registered by the browser or your mobile device to manage the website. For security, service workers are scoped to the domain they reside in. You cannot reference JavaScript from another domain for your service worker, and service workers are blocked from modifying the page directly. Instead, they work as a proxy to help marshal requests. If you place your service worker at mydomain.com/serviceworker/code.js it will only be able to access pages served underneath mydomain.com/serviceworker. For this reason, it is most often installed at the root.

I created a partial template that I reference in the footer. It contains this code:

`if ('serviceWorker' in navigator) {
    navigator.serviceWorker
        .register('/sw.js', { scope: '/' })
        .then(() => {
            console.info('Developer for Life Service Worker Registered');
        }, err => console.error("Developer for Life Service Worker registration failed: ", err));
    navigator.serviceWorker
        .ready
        .then(() => {
            console.info('Developer for Life Service Worker Ready');
        });
}`

The JavaScript registers the source code for the service worker (sw.js) and emits a console message when it is ready. The service worker I implemented acts mostly as a network proxy. It has a few primary tasks:

1. It fetches and stores content in the browser’s cache. This is used to serve content when online for a faster connection, as well as to make content available offline.
2. It serves a special offline page when you attempt to access non-cached content without a connection to the Internet.
3. It refreshes content based on Time to Live (TTL) settings.
4. If a new version is detected, it clears the old cache and starts fresh.
5. I based the source code on the “Offline First Service Worker” here:
