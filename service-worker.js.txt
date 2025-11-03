// نام کش برای ذخیره فایل‌ها
const CACHE_NAME = 'abundance-game-v1';

// لیستی از فایل‌هایی که باید کش شوند
const urlsToCache = [
    './abundance_game.html', // فایل اصلی
    './manifest.json',       // فایل مانیفست
    'https://cdn.tailwindcss.com', // Tailwind CSS
    'https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap', // فونت
    // توجه: آدرس‌های Firebase و Gstatic را برای سادگی کش نمی‌کنیم، چون برای شروع بازی به هر حال نیاز به آنلاین بودن دارند.
];

// نصب: رویداد نصب Service Worker
self.addEventListener('install', (event) => {
    event.waitUntil(
        caches.open(CACHE_NAME)
            .then((cache) => {
                console.log('Opened cache and cached files.');
                return cache.addAll(urlsToCache);
            })
    );
});

// فعال‌سازی: حذف کش‌های قدیمی
self.addEventListener('activate', (event) => {
    const cacheWhitelist = [CACHE_NAME];
    event.waitUntil(
        caches.keys().then((cacheNames) => {
            return Promise.all(
                cacheNames.map((cacheName) => {
                    if (cacheWhitelist.indexOf(cacheName) === -1) {
                        // حذف کش‌های قدیمی
                        return caches.delete(cacheName);
                    }
                })
            );
        })
    );
});

// فچ: دریافت منابع از کش
self.addEventListener('fetch', (event) => {
    // از کش به عنوان اولویت اول استفاده کن
    event.respondWith(
        caches.match(event.request)
            .then((response) => {
                // اگر در کش بود، برگردان
                if (response) {
                    return response;
                }
                // اگر در کش نبود، از شبکه درخواست کن
                return fetch(event.request);
            })
    );
});
