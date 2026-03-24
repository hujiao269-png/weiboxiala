// ==UserScript==
// @name         微博搜索下拉词采集工具（支持原词+a-z）
// @namespace    http://tampermonkey.net/
// @version      1.0
// @description  采集微博网页端搜索下拉词，支持批量获取原词+a-z组合词，支持直接输入搜索
// @author       You
// @match        https://weibo.com/*
// @match        https://s.weibo.com/*
// @match        https://m.weibo.cn/*
// @grant        none
// ==/UserScript==

(function () {
  'use strict';

  /********************
   * 全局状态
   ********************/
  let previousSuggest = null; // { url, keyword, list: [] }
  let isCollecting = false;
  let allContents = [];
  let currentKeyword = '';

  /********************
   * 初始化
   ********************/
  function init() {
    if (document.querySelector('#weibo-suggest-tool')) return;

    createPanel();
    monitorNetworkForSuggest();
    console.log('[WB-SUG] 微博下拉词采集工具已初始化');
  }

  /********************
   * UI
   ********************/
  function createPanel() {
    const panel = document.createElement('div');
    panel.id = 'weibo-suggest-tool';
    panel.style.cssText = `
      position: fixed;
      top: 20px;
      right: 20px;
      width: 320px;
      z-index: 100000;
      background: rgba(255,255,255,0.96);
      border-radius: 10px;
      box-shadow: 0 2px 12px rgba(0,0,0,0.15);
      padding: 12px;
      font-family: -apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Helvetica,Arial,"PingFang SC","Hiragino Sans GB","Microsoft YaHei",sans-serif;
      color: #222;
    `;

    const title = document.createElement('div');
    title.textContent = '微博下拉词采集工具';
    title.style.cssText = `font-weight:700;text-align:center;margin-bottom:10px;`;
    panel.appendChild(title);

    const row = document.createElement('div');
    row.style.cssText = `display:flex;gap:8px;margin-bottom:8px;`;
    panel.appendChild(row);

    const input = document.createElement('input');
    input.type = 'text';
    input.placeholder = '输入关键词（支持空格 + a-z）';
    input.style.cssText = `
      flex:1;
      padding: 8px;
      border: 1px solid #ddd;
      border-radius: 6px;
      outline: none;
    `;
    row.appendChild(input);

    const btnSearch = createButton('搜索触发');
    btnSearch.style.width = '88px';
    row.appendChild(btnSearch);

    const btnBatch = createButton('批量采集(原词+a-z)');
    btnBatch.style.marginTop = '6px';

    const btnSave = createButton('保存当前下拉词');
    btnSave.style.marginTop = '6px';

    const tip = document.createElement('div');
    tip.style.cssText = `
      margin-top:10px;
      font-size:12px;
      line-height:1.5;
      padding:8px;
      border-radius:6px;
      background:#f6f6f6;
      color:#555;
    `;
    tip.innerHTML = `
      提示：微博网页端下拉联想可能随账号/地区/AB变化。<br/>
      若“保存”提示没数据，请打开控制台(F12)把最新的 sug 接口 URL 和返回 JSON 发我，我帮你定向适配。
    `;

    panel.appendChild(btnBatch);
    panel.appendChild(btnSave);
    panel.appendChild(tip);
    document.body.appendChild(panel);

    // 输入框逻辑：同步 currentKeyword；当输入到 “关键词 + 空格 + 单字母” 时可自动触发
    input.addEventListener('input', async (e) => {
      const val = e.target.value || '';
      currentKeyword = val;

      // 自动触发：最后一段是单字母 a-zA-Z，并且光标在末尾
      const cursor = e.target.selectionStart;
      const isAtEnd = cursor === val.length;
      const parts = val.split(' ');
      const last = parts[parts.length - 1];
      if (isAtEnd && last && last.length === 1 && /[a-zA-Z]/.test(last)) {
        await triggerWeiboSuggest(val);
      }
    });

    btnSearch.addEventListener('click', async () => {
      const kw = input.value.trim();
      if (!kw) return alert('请输入关键词');
      await triggerWeiboSuggest(kw);
    });

    btnSave.addEventListener('click', () => {
      const kw = (input.value || '').trim();
      if (!kw) return alert('请输入关键词');
      if (!previousSuggest || !previousSuggest.list || !previousSuggest.list.length) {
        return alert('暂无下拉词数据（请先点击“搜索触发”或等待下拉接口返回）');
      }
      downloadContent(formatAsTxt(kw, previousSuggest.list), `${safeName(kw)}_下拉词.txt`);
    });

    btnBatch.addEventListener('click', async () => {
      if (isCollecting) return alert('正在采集中，请稍等…');
      const base = (input.value || '').trim().split(' ')[0];
      if (!base) return alert('请先输入基础关键词（空格前的部分）');

      isCollecting = true;
      allContents = [];
      btnBatch.disabled = true;

      const suffixes = [''].concat('abcdefghijklmnopqrstuvwxyz'.split(''));
      const total = suffixes.length;

      try {
        for (let i = 0; i < suffixes.length; i++) {
          const suffix = suffixes[i];
          const kw = suffix ? `${base} ${suffix}` : base;

          btnBatch.textContent = `采集中 ${Math.round(((i + 1) / total) * 100)}%`;

          await triggerWeiboSuggest(kw);
          const ok = await waitForSuggest(kw, 10, 350);

          if (ok && previousSuggest?.list?.length) {
            allContents.push(formatAsTxt(kw, previousSuggest.list));
          } else {
            allContents.push(`${kw} 下拉词：\n(未获取到数据)\n`);
          }

          await sleep(800);
        }

        const content = allContents.join('\n
' + '='.repeat(36) + '\n
');
        downloadContent(content, `${safeName(base)}_批量下拉词.txt`);
      } catch (e) {
        console.error('[WB-SUG] 批量采集错误:', e);
        alert('采集过程出现错误，详见控制台');
      } finally {
        btnBatch.textContent = '批量采集(原词+a-z)';
        btnBatch.disabled = false;
        isCollecting = false;
      }
    });
  }

  function createButton(text) {
    const b = document.createElement('button');
    b.textContent = text;
    b.style.cssText = `
      width: 100%;
      padding: 9px 10px;
      border: 0;
      border-radius: 6px;
      cursor: pointer;
      background: #ff8200;
      color: #fff;
      font-size: 13px;
      transition: filter .15s;
    `;
    b.addEventListener('mouseenter', () => { if (!b.disabled) b.style.filter = 'brightness(0.92)'; });
    b.addEventListener('mouseleave', () => { b.style.filter = 'none'; });
    return b;
  }

  /********************
   * 触发微博下拉：优先操控页面搜索框；找不到则直接请求（同源）接口不一定可行，因此以“触发输入”优先
   ********************/
  async function triggerWeiboSuggest(keyword) {
    previousSuggest = null;

    // 1) 尝试找到微博的搜索输入框（多套选择器，覆盖 weibo.com / s.weibo.com）
    const searchBox = await waitForElement([
      'input[type="search"]',
      'input[placeholder*="搜索"]',
      'input[node-type="searchInput"]',
      'input.W_input',                 // 老版
      'input.woo-input-main',          // 新版可能
      'input'                          // 兜底（最后才用）
    ], 2500);

    if (!searchBox) {
      console.warn('[WB-SUG] 未找到搜索框，无法触发输入事件。请切到带搜索框的页面（weibo.com 首页/搜索页）。');
      return;
    }

    // 2) 触发事件序列
    try {
      searchBox.focus();
      searchBox.click();

      // 设置值
      searchBox.value = keyword;

      // input/change + 键盘事件，尽量触发联想
      const events = [
        new Event('input', { bubbles: true }),
        new Event('change', { bubbles: true }),
        new KeyboardEvent('keydown', { bubbles: true, key: 'a' }),
        new KeyboardEvent('keyup', { bubbles: true, key: 'a' })
      ];
      events.forEach(ev => searchBox.dispatchEvent(ev));

      // 光标放末尾
      if (searchBox.setSelectionRange) {
        searchBox.setSelectionRange(keyword.length, keyword.length);
      }
    } catch (e) {
      console.error('[WB-SUG] 触发搜索框事件失败：', e);
    }
  }

  async function waitForElement(selectors, timeoutMs) {
    const start = Date.now();
    const pick = () => {
      for (const sel of selectors) {
        const el = document.querySelector(sel);
        if (el && el.tagName === 'INPUT') return el;
      }
      return null;
    };

    let el = pick();
    if (el) return el;

    return new Promise(resolve => {
      const obs = new MutationObserver(() => {
        el = pick();
        if (el) {
          obs.disconnect();
          resolve(el);
        } else if (Date.now() - start > timeoutMs) {
          obs.disconnect();
          resolve(null);
        }
      });
      obs.observe(document.documentElement, { childList: true, subtree: true });

      setTimeout(() => {
        obs.disconnect();
        resolve(pick());
      }, timeoutMs);
    });
  }

  async function waitForSuggest(keyword, maxAttempts = 10, interval = 300) {
    for (let i = 0; i < maxAttempts; i++) {
      if (previousSuggest?.list?.length) return true;
      await sleep(interval);
    }
    console.log('[WB-SUG] 等待下拉响应超时：', keyword);
    return false;
  }

  /********************
   * 网络监听：自动识别“下拉联想”响应
   ********************/
  function monitorNetworkForSuggest() {
    // 解析各种可能结构，尽量抽取成字符串数组
    const extractList = (obj) => {
      if (!obj) return [];

      // 常见：{ data: { ... } }
      const root = obj.data ?? obj;

      // 可能字段名集合
      const candidates = [
        root.sug,
        root.suggest,
        root.suggestion,
        root.suggestions,
        root.sugs,
        root.list,
        root.items,
        root.data, // 有的会套多层
      ].filter(Boolean);

      let arr = [];
      for (const c of candidates) {
        if (Array.isArray(c)) arr = arr.concat(c);
      }

      // 对象数组里常见字段：word/keyword/text/title
      const out = [];
      for (const it of arr) {
        if (typeof it === 'string') out.push(it);
        else if (it && typeof it === 'object') {
          out.push(it.word ?? it.keyword ?? it.text ?? it.title ?? it.query ?? '');
        }
      }

      return out.map(s => (s || '').trim()).filter(Boolean);
    };

    // 判断是否像“下拉建议接口”
    const looksLikeSuggest = (url, text) => {
      const u = (url || '').toLowerCase();
      if (u.includes('suggest') || u.includes('sug') || u.includes('recommend') || u.includes('completion') || u.includes('hint')) {
        return true;
      }
      // 兜底：响应体里含 sug/suggest 字样（仅做弱判断）
      return typeof text === 'string' && /"sug|suggest|suggestion|suggestions"/i.test(text);
    };

    // XHR hook
    const originOpen = XMLHttpRequest.prototype.open;
    const originSend = XMLHttpRequest.prototype.send;

    XMLHttpRequest.prototype.open = function (method, url) {
      this._wb_url = url;
      return originOpen.apply(this, arguments);
    };

    XMLHttpRequest.prototype.send = function () {
      this.addEventListener('load', () => {
        const url = this._wb_url || '';
        const text = this.responseText;

        if (!looksLikeSuggest(url, text)) return;

        try {
          const json = JSON.parse(text);
          const list = extractList(json);

          if (list.length) {
            previousSuggest = {
              url,
              keyword: currentKeyword,
              list: uniq(list)
            };
            console.log('[WB-SUG] 捕获下拉词', previousSuggest);
          }
        } catch (_) {}
      });

      return originSend.apply(this, arguments);
    };

    // fetch hook
    const originFetch = window.fetch;
    window.fetch = function (input, init) {
      return originFetch(input, init).then((resp) => {
        try {
          const url = typeof input === 'string' ? input : (input?.url || '');
          // 先快速判断 URL
          if (!looksLikeSuggest(url, '')) return resp;

          resp.clone().text().then((text) => {
            if (!looksLikeSuggest(url, text)) return;
            try {
              const json = JSON.parse(text);
              const list = extractList(json);
              if (list.length) {
                previousSuggest = {
                  url,
                  keyword: currentKeyword,
                  list: uniq(list)
                };
                console.log('[WB-SUG] 捕获下拉词(fetch)', previousSuggest);
              }
            } catch (_) {}
          });
        } catch (_) {}
        return resp;
      });
    };
  }

  /********************
   * 导出
   ********************/
  function formatAsTxt(keyword, list) {
    const lines = list.map((t, i) => `${i + 1}. ${t}`);
    return `${keyword} 下拉词：\n${lines.length ? lines.join('\n') : '(空)'}\n`;
  }

  function downloadContent(content, filename) {
    const blob = new Blob([content], { type: 'text/plain;charset=utf-8' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = filename;
    document.body.appendChild(a);
    a.click();
    a.remove();
    URL.revokeObjectURL(url);
  }

  function safeName(name) {
    return String(name).replace(/[\\/:*?"<>|]/g, '_').slice(0, 80);
  }

  function uniq(arr) {
    return [...new Set(arr)];
  }

  function sleep(ms) {
    return new Promise(r => setTimeout(r, ms));
  }

  /********************
   * 启动
   ********************/
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', init);
  } else {
    init();
  }
})();
