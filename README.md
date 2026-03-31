so basically paste this into your tampermonkey

```
// ==UserScript==
// @name         Addiction Interceptor - One-Time Enforcement
// @namespace    http://tampermonkey.net/
// @version      4.1
// @description  Intercepts only once per session and respects the pass flag
// @author       Gemini
// @match        *://*.youtube.com/*
// @match        *://*.roblox.com/*
// @run-at       document-start
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    // Use URLSearchParams for reliable parameter extraction
    const urlParams = new URLSearchParams(window.location.search);

    // 1. Check if the URL has the pass flag
    if (urlParams.get('pass') === 'true') {
        // Save to session so other pages in this tab are safe
        sessionStorage.setItem('gate_passed', 'true');
        
        // [Optional but Recommended] Clean up the URL so 'pass=true' doesn't clutter it
        urlParams.delete('pass');
        const cleanSearch = urlParams.toString();
        const cleanUrl = window.location.origin + window.location.pathname + (cleanSearch ? '?' + cleanSearch : '') + window.location.hash;
        window.history.replaceState({}, '', cleanUrl);
        
        return;
    }

    // 2. Check Session storage (if this tab passed once, let them through)
    if (sessionStorage.getItem('gate_passed') === 'true') {
        return;
    }

    // --- Intercept Logic ---
    const config = {
        'youtube.com': { level: 'A', time: 30 },
        'roblox.com':  { level: 'B', time: 20 },
        'default':     { level: 'C', time: 10 }
    };

    const hostname = window.location.hostname;
    const siteKey = Object.keys(config).find(key => hostname.includes(key)) || 'default';
    const siteData = config[siteKey];

    const myGatePage = "https://propom2620.github.io/addiction_gate/";
    const originalUrl = encodeURIComponent(window.location.href);

    // Generate redirect target
    const target = `${myGatePage}?level=${siteData.level}&time=${siteData.time}&return=${originalUrl}&v=${Date.now()}`;

    // Execute redirect
    window.location.replace(target);

})();
```
