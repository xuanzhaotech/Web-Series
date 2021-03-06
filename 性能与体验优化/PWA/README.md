# Progressive Web Apps

Progressive web apps are a new breed of web apps. They combine the benefits of a native app with the low friction nature of the web. Progressive web apps start off as simple websites, but as the user interacts with them, they progressively gain new powers. They transform from a website into something much more like a traditional native app.

# 离线优先

我们不可以在 Service Worker 中使用 Local Storage，并且 Service Worker 不可以使用任何的同步 API，不过可以使用 IndexDB，CacheAPI，或者利用 postMessage() 与界面进行交互。参考 [GoogleChromeLabs/airhorn](https://github.com/GoogleChromeLabs/airhorn)，可以使用如下的代码注册 ServiceWorker，并且使用 Cache API 来进行资源与请求缓存，首先需要注册 ServiceWorker：

```js
if (‘serviceWorker’ in navigator) {
  window.addEventListener(‘load’, function() {
    navigator.serviceWorker.register(‘/sw.js’).then(
      function(registration) {
        // Registration was successful
        console.log(‘ServiceWorker registration successful with scope: ‘, registration.scope); },
      function(err) {
        // registration failed :(
        console.log(‘ServiceWorker registration failed: ‘, err);
      });
  });
}
```

然后在 sw.js 文件中缓存资源：

```js
self.addEventListener("install", e => {
  let timeStamp = Date.now();
  e.waitUntil(
    caches.open("airhorner").then(cache => {
      return cache
        .addAll([
          `/`,
          `/index.html?timestamp=${timeStamp}`,
          `/styles/main.css?timestamp=${timeStamp}`,
          `/scripts/main.min.js?timestamp=${timeStamp}`,
          `/scripts/comlink.global.js?timestamp=${timeStamp}`,
          `/scripts/messagechanneladapter.global.js?timestamp=${timeStamp}`,
          `/sounds/airhorn.mp3?timestamp=${timeStamp}`
        ])
        .then(() => self.skipWaiting());
    })
  );
});

self.addEventListener("activate", event => {
  event.waitUntil(self.clients.claim());
});

self.addEventListener("fetch", event => {
  event.respondWith(
    caches.match(event.request, { ignoreSearch: true }).then(response => {
      // 默认不会保护 Cookie，可以使用 fetch(url, {credentials: 'include'}) 来发送 Cookie
      return response || fetch(event.request);
    })
  );
});
```

# 交互友好

# 原生体验
