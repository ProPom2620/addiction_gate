so basically paste this into your tampermonkey

// ==UserScript==
// @name         Addiction Interceptor - One-Time Enforcement
// @namespace    http://tampermonkey.net/
// @version      4.0
// @description  Intercepts only once per session and respects the pass flag
// @author       Gemini
// @match        *://*.youtube.com/*
// @match        *://*.roblox.com/*
// @run-at       document-start
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    // 1. 檢查網址是否有通過標記（這是最直接的攔截終止符）
    if (window.location.search.includes('pass=true')) {
        // 為了保險，進一步寫入 session，防止 SPA 切換頁面後參數消失
        sessionStorage.setItem('gate_passed', 'true');
        return;
    }

    // 2. 檢查 Session 狀態（只要這個分頁通過過，就不再攔截）
    if (sessionStorage.getItem('gate_passed') === 'true') {
        return;
    }

    // --- 攔截逻辑 --- --A, B, C
    const config = {
        'youtube.com': { level: 'A', time: 30 },
        'roblox.com':  { level: 'B', time: 20 },
        'default':     { level: 'C', time: 10 }
    };

    const hostname = window.location.hostname;
    let siteKey = Object.keys(config).find(key => hostname.includes(key)) || 'default';
    const siteData = config[siteKey];

    // 填寫你的 GitHub Pages 網址
    const myGatePage = "https://propom2620.github.io/addiction_gate/";
    const originalUrl = encodeURIComponent(window.location.href);

    // 生成跳轉網址，確保帶著所有參數
    const target = `${myGatePage}?level=${siteData.level}&time=${siteData.time}&return=${originalUrl}&v=${Date.now()}`;

    // 執行跳轉
    window.location.replace(target);
})();
