# Prototype Architecture Reference

This document specifies the complete technical architecture for single-file HTML prototypes. Every prototype must follow these patterns — they've been refined over multiple iterations to handle cross-platform edge cases (iOS Safari height bugs, tap-vs-drag conflicts, dual rendering for desktop/mobile, etc.).

## Table of Contents
1. [Figma Value Extraction](#figma-value-extraction)
2. [HTML Shell](#html-shell)
3. [CSS Foundation](#css-foundation)
4. [DOM Helper](#dom-helper)
5. [Navigation System](#navigation-system)
6. [Screen Renderer Pattern](#screen-renderer-pattern)
7. [Modal System](#modal-system)
8. [Animation System](#animation-system)
9. [Responsive Dual Rendering](#responsive-dual-rendering)
10. [Gesture Handling](#gesture-handling)
11. [Common Components](#common-components)

---

## Figma Value Extraction

When Figma nodes are available, always call both `get_screenshot` and `get_design_context` for every node. The design context returns Tailwind-style React code — extract the raw values from it and translate to inline styles for the `h()` helper.

### What to extract from `get_design_context`

| Figma class pattern | What it tells you | Translate to |
|---|---|---|
| `text-[14px]` | Font size | `fontSize:'14px'` |
| `font-['Suisse_Intl:Semibold']` | Font weight | `fontWeight:'600'` |
| `leading-[20px]` | Line height | `lineHeight:'20px'` |
| `tracking-[-0.5px]` | Letter spacing | `letterSpacing:'-0.5px'` |
| `p-[16px]`, `px-[24px]`, `py-[8px]` | Padding | `padding:'8px 24px'` or individual |
| `gap-[16px]` | Flex gap | `gap:'16px'` |
| `rounded-[12px]` | Border radius | `borderRadius:'12px'` |
| `h-[48px]`, `w-[393px]` | Height/width | `height:'48px'` |
| `size-[32px]` | Width + height | `width:'32px',height:'32px'` |
| `inset-[...]` | Position offsets | Absolute positioning values |

### Color mapping (Figma → grayscale)

The design context includes a "styles contained in the design" section listing all tokens. Map them:

| Figma token | Shade equivalent |
|---|---|
| `Tilt/Shades/900` (#100F0F) | `var(--s900)` — use as-is |
| `Tilt/Shades/500` (#595954) | `var(--s500)` — use as-is |
| `Tilt/Shades/300` (#EBEAE0) | `var(--s300)` — use as-is |
| `Tilt/Shades/200` (#F7F5EF) | `var(--s200)` — use as-is |
| `Tilt/Chartreuse/*` | `var(--s900)` on light, `var(--s300)` on dark |
| `Tilt/Secondary/Persimmon/*` | `var(--s300)` for containers, `var(--s200)` for lighter |
| `Empower/Secondary/Lawn/*` | `var(--s300)` (green category bubbles) |
| `Empower/Secondary/Marigold/*` | `var(--s300)` (orange category bubbles) |
| `Empower/Secondary/Visteria/*` | `var(--s300)` (purple category bubbles) |
| `Empower/Chartreuse/600` (#E5E217) | `var(--s900)` (selected checkboxes) |

### Tilt DS component sizes (from Figma)

These are recurring component measurements extracted across multiple prototypes:

| Component | Size | Details |
|---|---|---|
| Primary CTA button | height 48px, radius 50px | Full-width minus 24px padding each side |
| Pill button (small) | height 28px, radius 24px | padding 0 12px |
| Pill button (medium) | height 40px, radius 24px | padding 8px 12-16px |
| Checkbox/radio (selected) | 32×32px, radius 50% | Filled shade-900, white ✓ |
| Checkbox/radio (unselected) | 32×32px, radius 50% | 2px border shade-400, white fill |
| Card icon | 20×14px, radius 2.5px | 0.5px border, shade-100 fill |
| Category bubble | 40×40px, radius 99px | shade-300 fill (grayscale) |
| Status bar | height 47px, pt 19px | SVG icons, 15.86px time |
| Home indicator | height 32px | 134×5px bar, radius 50px |
| Tab underline | 1px solid, 15px gap below text | Only on active tab |
| Horizontal padding | 24px | Standard for all content areas |

---

## HTML Shell

Every prototype starts with this structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=no, maximum-scale=1">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<meta name="mobile-web-app-capable" content="yes">
<meta name="theme-color" content="#000000">
<title>[Flow Name] Prototype</title>
<style>
  /* All CSS here */
</style>
</head>
<body>
<div id="app">
  <!-- Desktop chrome: tab bar, status bar, phone frame -->
</div>
<div id="ms"><div class="sa" id="mc">
  <!-- Static HTML fallback for no-JS / iOS Quick Look -->
</div></div>
<script>
  /* All JS here */
</script>
</body>
</html>
```

Key IDs:
- `#app` — Desktop layout (tab bar + phone frame). Hidden on mobile via media query.
- `#ms` — Mobile screen wrapper. Hidden on desktop, `position: fixed` fullscreen on mobile.
- `#mc` — Mobile content target. Receives rendered screens on both mobile and desktop (for dual rendering).
- `#pc` — Phone content target inside the phone frame. Desktop only.
- `#pf` — Phone frame (393×852px, 48px border-radius, 8px border).
- `#pn` — Phone notch (120×28px, centered at top).
- `#sl` — Screen label below phone frame.
- `#sn` — Screen name in the status bar.

## CSS Foundation

### Font Faces
```css
@font-face { font-family: 'Suisse Intl'; font-weight: 900; src: local('Suisse Intl Black'), local('SuisseIntl-Black'); }
@font-face { font-family: 'Suisse Intl'; font-weight: 700; src: local('Suisse Intl Bold'), local('SuisseIntl-Bold'); }
@font-face { font-family: 'Suisse Intl'; font-weight: 600; src: local('Suisse Intl SemiBold'), local('SuisseIntl-SemiBold'); }
@font-face { font-family: 'Suisse Intl'; font-weight: 400; src: local('Suisse Intl Regular'), local('SuisseIntl-Regular'); }
```

### CSS Variables (compressed for file size)
```css
:root {
  --ff: 'Suisse Intl', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  --s900:#100F0F;--s800:#262525;--s700:#3A3A3A;--s500:#595954;--s400:#8B8B87;--s300:#EBEAE0;--s200:#F7F5EF;--s100:#FFFDF6;
  --anim: 280ms; --ease: cubic-bezier(0,0,0.2,1);
}
```

### Reset
```css
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent}
html{-webkit-text-size-adjust:100%}
body{font-family:var(--ff);overscroll-behavior-y:contain;overflow:hidden;height:100vh;height:100dvh;color:var(--s900)}
button{font-family:inherit;touch-action:manipulation;-webkit-appearance:none;appearance:none;border:none;background:none;cursor:pointer}
```

### Screen Structure Classes
```css
.sc{display:flex;flex-direction:column;height:100%;overflow:hidden;position:relative}
.sc.dk{background:var(--s900);color:var(--s100)}
.sc.lt{background:#fff;color:var(--s900)}
.sh{flex-shrink:0;z-index:5}          /* header */
.ss{flex:1;overflow-y:auto;-webkit-overflow-scrolling:touch;scrollbar-width:none;-ms-overflow-style:none;min-height:0}
.ss::-webkit-scrollbar{display:none}   /* scroll area */
.sf{flex-shrink:0;z-index:5}          /* footer */
```

### Touch Feedback
```css
.t:active{opacity:0.7!important;transition:opacity 150ms}
```
Apply `.t` class to every tappable element.

### Screen Transitions
```css
.screen-push{animation:pushIn var(--anim) var(--ease) both}
@keyframes pushIn{from{transform:translateX(100%)}to{transform:translateX(0)}}
.screen-pop{animation:popIn var(--anim) var(--ease) both}
@keyframes popIn{from{transform:translateX(-30%);opacity:.6}to{transform:translateX(0);opacity:1}}
.screen-fade{animation:fadeIn 150ms ease both}
@keyframes fadeIn{from{opacity:0}to{opacity:1}}
```

### Modal Overlay + Bottom Sheet
```css
.mo{position:absolute;inset:0;z-index:50;background:rgba(0,0,0,0);transition:background var(--anim) ease;display:flex;flex-direction:column;justify-content:flex-end;pointer-events:none}
.mo.v{background:rgba(0,0,0,0.82);pointer-events:auto}
.ms{background:var(--s800);border-radius:32px 32px 0 0;transform:translateY(100%);transition:transform var(--anim) var(--ease);max-height:85%;overflow-y:auto;-webkit-overflow-scrolling:touch;padding-bottom:env(safe-area-inset-bottom,0)}
.mo.v .ms{transform:translateY(0)}
.mh{width:51px;height:4px;border-radius:9999px;background:var(--s500);margin:16px auto 0}
```

Note: Modal sheet (`.ms` inside `.mo`) uses `var(--s800)` background for dark-context modals. For light-context modals, override to `#fff`.

### Desktop Chrome
```css
#app{display:flex;flex-direction:column;height:100vh;height:100dvh;background:#000}
#tb{display:flex;background:#1e1e1e;border-bottom:1px solid #333;padding:0 16px;flex-shrink:0}
#tb button{padding:10px 16px;font-size:13px;border-bottom:2px solid transparent;color:#888;font-weight:400}
#tb button.a{color:#fff;font-weight:700;border-bottom-color:#fff}
#sb{display:flex;gap:6px;padding:10px 16px;background:#1a1a1a;border-bottom:1px solid #333;align-items:center;flex-shrink:0}
#sb .l{font-size:11px;font-weight:600;color:#777;margin-right:4px}
#vp{flex:1;display:flex;justify-content:center;align-items:center;padding:24px;overflow:hidden;min-height:0}
#pf{width:393px;height:852px;border-radius:48px;border:8px solid #444;background:#fff;overflow:hidden;box-shadow:0 24px 80px rgba(0,0,0,.6);display:flex;flex-direction:column;position:relative;flex-shrink:0}
#pn{position:absolute;top:0;left:50%;transform:translateX(-50%);width:120px;height:28px;background:#444;border-radius:0 0 16px 16px;z-index:10}
#pc{flex:1;overflow:hidden;position:relative;display:flex;flex-direction:column}
#pc>.sc{flex:1;min-height:0}
#sl{text-align:center;margin-top:12px;font-size:12px;color:#666;font-weight:600}
```

### Mobile Overrides
```css
@media(max-width:599px){
  #app{display:none!important}
  #ms{display:flex!important;flex-direction:column;position:fixed!important;top:0;left:0;right:0;bottom:0;width:100%;height:100vh;height:100dvh;z-index:9999}
  #ms .sa{flex:1;min-height:0;overflow:hidden;display:flex;flex-direction:column}
  #ms .sa>.sc{flex:1;min-height:0}
}
#ms{display:none}
@media(min-width:600px){#ms{display:none!important}}
```

## DOM Helper

The `h()` function is the backbone of all rendering. It creates DOM elements declaratively:

```javascript
function h(t,a){
  var e=document.createElement(t),c=Array.prototype.slice.call(arguments,2);
  if(a)Object.keys(a).forEach(function(k){
    var v=a[k];
    if(k==='style'&&typeof v==='object')Object.assign(e.style,v);
    else if(k.startsWith('on')&&typeof v==='function')e.addEventListener(k.slice(2),v);
    else if(k==='className')e.className=v;
    else if(k==='innerHTML')e.innerHTML=v;
    else e.setAttribute(k,v);
  });
  c.flat(Infinity).forEach(function(c){
    if(c==null||c===false)return;
    e.appendChild(typeof c==='string'?document.createTextNode(c):c);
  });
  return e;
}
```

Usage: `h(tag, attributes, ...children)`
- `style` accepts an object (camelCase properties)
- `on*` attributes become event listeners
- `className` sets the class
- `innerHTML` sets raw HTML (use sparingly — only for inline markup like underlined text)
- Children can be strings (become text nodes), elements, arrays, null, or false (skipped)

## Navigation System

```javascript
var H=[],S='home',D='forward',modal=null;
// Plus any screen-specific state: amt, agreed, selectedOption, etc.

function nav(s){H.push(S);S=s;D='forward';modal=null;render()}
function back(){if(!H.length)return;S=H.pop();D='back';modal=null;render()}
function sm(t){modal=t;update()}
function hm(){modal=null;update()}
window.R=function(){H=[];S='home';D='forward';/* reset state vars */;modal=null;render()};
```

- `H` — History stack (array of screen name strings)
- `S` — Current screen name
- `D` — Animation direction: `'forward'` | `'back'` | `'fade'`
- `modal` — Current modal name or null

## Screen Renderer Pattern

Each screen is a function that returns a `.sc` DOM element:

```javascript
function renderMyScreen(){
  dk = true; // or false — set this for helper functions that need to know
  var s = h('div',{className:'sc dk'}); // or 'sc lt'

  // Header
  var hdr = h('div',{className:'sh'});
  hdr.appendChild(sbar(true)); // status bar
  // ... nav buttons, title
  s.appendChild(hdr);

  // Scrollable body
  var scroll = h('div',{className:'ss',style:{padding:'24px'}});
  // ... content
  s.appendChild(scroll);

  // Footer
  var ft = h('div',{className:'sf',style:{padding:'12px 24px 0'}});
  // ... buttons
  ft.appendChild(hind(true)); // home indicator
  s.appendChild(ft);

  // Modal (conditional)
  if(modal==='myModal'){
    // render modal overlay inside this screen
  }

  return s;
}
```

## Modal System

Modals render as children of the screen that triggers them. The animation uses a `requestAnimationFrame` trick to trigger the CSS transition:

```javascript
if(modal==='myModal'){
  var ov = h('div',{className:'mo'});
  var sh = h('div',{className:'ms',style:{padding:'0 24px 0'}});
  sh.appendChild(h('div',{className:'mh'})); // drag handle
  // ... modal content
  // ... action buttons
  sh.appendChild(hind(true)); // home indicator inside modal
  ov.appendChild(sh);
  s.appendChild(ov);
  requestAnimationFrame(function(){ov.classList.add('v')});
}
```

The overlay (`.mo`) starts transparent and non-interactive. Adding `.v` triggers the scrim fade-in and sheet slide-up via CSS transitions.

To dismiss: call `hm()` which sets `modal=null` and calls `update()` (not `render()` — the modal simply won't be in the DOM on next update, and the screen won't re-animate).

## Animation System

The `render()` function applies animation classes based on `D`:

```javascript
function anim(el){
  if(D==='forward') el.classList.add('screen-push');
  else if(D==='back') el.classList.add('screen-pop');
  else el.classList.add('screen-fade');
}
```

The `render()` function:
1. Calls the screen router (`rs()`) to get the current screen DOM
2. Clears the mobile container (`mc`) and appends the new screen
3. Clears the phone frame container (`pc`) and appends a second copy
4. Applies animation to whichever container is currently visible
5. Updates screen labels

```javascript
var labels = {home:'Home', /* ... screen name to label map */};

function rs(){
  switch(S){
    case 'screenA': return renderScreenA();
    case 'screenB': return renderScreenB();
    default: return renderHome();
  }
}

function render(){
  var mob = isMob();
  mc.innerHTML=''; var m=rs(); if(mob) anim(m); mc.appendChild(m);
  pc.innerHTML=''; var d=rs(); if(!mob) anim(d); pc.appendChild(d);
  var sn=document.getElementById('sn'); if(sn) sn.textContent=labels[S]||S;
  var sl=document.getElementById('sl'); if(sl) sl.textContent='Flow / '+(labels[S]||S);
}
```

Both containers always get populated. Only the visible one gets animation.

## Live Interaction Pattern (update vs render)

**CRITICAL**: `render()` tears down and rebuilds the entire DOM with an animation. This is correct for **screen navigation** (nav/back) but causes visible flickering when used for **in-screen state changes** like toggling a radio button, checking a checkbox, expanding a disclosure, or typing in an input. These should feel like a live coded app, not a slideshow.

### The `update()` function

Use `update()` for any state change that doesn't involve changing screens. It re-renders without animation and preserves scroll position:

```javascript
function update(){
  var mScroll=mc.querySelector('.ss');
  var pScroll=pc.querySelector('.ss');
  var mst=mScroll?mScroll.scrollTop:0;
  var pst=pScroll?pScroll.scrollTop:0;
  mc.innerHTML='';mc.appendChild(rs());
  pc.innerHTML='';pc.appendChild(rs());
  var ms2=mc.querySelector('.ss');
  var ps2=pc.querySelector('.ss');
  if(ms2)ms2.scrollTop=mst;
  if(ps2)ps2.scrollTop=pst;
}
```

### When to use which

| Action | Function | Why |
|--------|----------|-----|
| Navigate to new screen | `nav()` → `render()` | Needs push/pop animation |
| Go back | `back()` → `render()` | Needs pop animation |
| Open modal | `sm()` → `update()` | Modal animates via CSS `.mo.v`, screen itself shouldn't re-animate |
| Close modal | `hm()` → `update()` | Same — just remove the overlay |
| Toggle radio/checkbox | Direct DOM | Store element refs, update `.style` and `.innerHTML` directly — no flicker |
| Expand/collapse disclosure | Direct DOM | CSS `max-height`/`opacity` transition on the content container |
| Input text (live update) | Direct DOM | Never re-render for typing |
| Form validation (enable/disable button) | Direct DOM | Update `opacity` and `pointerEvents` on the button element |

### Direct DOM manipulation for in-screen state

For **all in-screen state changes** (radios, checkboxes, disclosures, inputs), store element references in arrays or variables and update `.style` properties directly. NEVER call `render()` or `update()` — it causes visible flickering.

#### Radio / Checkbox selection

Build an array of `{key, el}` pairs during render. On tap, iterate and toggle styles:

```javascript
var chkEls = []; // populated during screen build
// ... for each option:
var circle = h('div',{style:{width:'32px',height:'32px',borderRadius:'50%',
  border: opt.key===selected ? 'none' : '2px solid var(--s400)',
  background: opt.key===selected ? 'var(--s900)' : '#fff',
  display:'flex',alignItems:'center',justifyContent:'center',
  flexShrink:'0',transition:'all 150ms var(--ease)'}});
if(opt.key===selected) circle.innerHTML='<span style="color:#fff;font-size:14px;font-weight:700">✓</span>';
chkEls.push({key:opt.key, el:circle});

row.addEventListener('click',function(){
  selected = opt.key;
  chkEls.forEach(function(c){
    var on = c.key === opt.key;
    c.el.style.background = on ? 'var(--s900)' : '#fff';
    c.el.style.border = on ? 'none' : '2px solid var(--s400)';
    c.el.innerHTML = on ? '<span style="color:#fff;font-size:14px;font-weight:700">✓</span>' : '';
    c.el.style.display='flex'; c.el.style.alignItems='center'; c.el.style.justifyContent='center';
  });
});
```

#### Disclosure expand/collapse

Use CSS `max-height` and `opacity` transitions — no DOM rebuild:

```javascript
var discContent = h('div',{style:{maxHeight:'0',opacity:'0',overflow:'hidden',
  transition:'max-height 280ms var(--ease), opacity 280ms var(--ease)'}});
// ... append disclosure body content to discContent

var arrow = h('span',{style:{transition:'transform 150ms var(--ease)',display:'inline-block'}},'›');
var discToggle = h('div',{className:'t', onclick:function(){
  discOpen = !discOpen;
  arrow.style.transform = discOpen ? 'rotate(90deg)' : 'rotate(0deg)';
  discContent.style.maxHeight = discOpen ? '500px' : '0';
  discContent.style.opacity = discOpen ? '1' : '0';
}});
```

#### Text inputs

Never re-render for typing. Use closures to update dependent elements:

```javascript
var inp = h('input', {type:'text', oninput: function(){
  myState = this.value;
  btn.style.opacity = myState.length > 0 ? '1' : '0.4';
  btn.style.pointerEvents = myState.length > 0 ? 'auto' : 'none';
}});
```

### Modal open/close with update()

Modals animate via CSS transitions (`.mo` → `.mo.v`), not via screen-level push/pop. So modal open/close should use `update()` to avoid the screen re-animating underneath:

```javascript
function sm(t){modal=t;update()}
function hm(){modal=null;update()}
```

The `requestAnimationFrame` trick still applies for the modal's own entrance animation.

### Arithmetic accuracy

All numbers in a prototype must be internally consistent. If a screen shows a total balance, the individual line items should sum to it. If a credit limit is shown, `balance + available = limit`. Check every number before delivering.

## Responsive Dual Rendering

The prototype serves two layouts from one codebase:
- **Desktop** (≥600px): `#app` visible with phone frame. Content renders into `#pc`.
- **Mobile** (<600px): `#app` hidden. `#ms` goes fullscreen via `position: fixed`. Content renders into `#mc`.

```javascript
function isMob(){return window.innerWidth<600}

// Resize listener re-renders when crossing the breakpoint
var wm = isMob();
window.addEventListener('resize',function(){
  var n=isMob();
  if(n!==wm){wm=n;render()}
});
```

## Gesture Handling

### Tap-vs-Drag Detection
Prevents accidental navigation when the user is scrolling:

```javascript
var dx=0,dy=0,dr=false;
document.addEventListener('mousedown',function(e){dx=e.clientX;dy=e.clientY;dr=false},true);
document.addEventListener('mousemove',function(e){if(Math.abs(e.clientX-dx)>6||Math.abs(e.clientY-dy)>6)dr=true},true);
document.addEventListener('click',function(e){if(dr){e.stopPropagation();e.preventDefault()}},true);
```

### Swipe-Back Gesture
iOS-style edge swipe to go back:

```javascript
var sx=-1;
document.addEventListener('touchstart',function(e){sx=e.touches[0].clientX<30?e.touches[0].clientX:-1},{passive:true});
document.addEventListener('touchend',function(e){if(sx<0)return;if(e.changedTouches[0].clientX-sx>80&&H.length)back();sx=-1},{passive:true});
```

## Common Components

### Status Bar (iOS-style)

The status bar renders a pixel-accurate iOS status bar with inline SVG icons for cellular signal, Wi-Fi, and battery. It takes a single `dk` boolean to flip fill color between light-on-dark and dark-on-light.

```javascript
function sbar(dk){
  var c=dk?'var(--s100)':'var(--s900)';
  // Cellular bars SVG — 4 bars, increasing height, 3px wide with 2px gap
  var cell='<svg width="18" height="12" viewBox="0 0 18 12" fill="none" xmlns="http://www.w3.org/2000/svg">'
    +'<rect x="0" y="9" width="3" height="3" rx="0.5" fill="'+c+'"/>'
    +'<rect x="5" y="6" width="3" height="6" rx="0.5" fill="'+c+'"/>'
    +'<rect x="10" y="3" width="3" height="9" rx="0.5" fill="'+c+'"/>'
    +'<rect x="15" y="0" width="3" height="12" rx="0.5" fill="'+c+'"/>'
    +'</svg>';
  // Wi-Fi SVG — three nested arcs + dot
  var wifi='<svg width="16" height="12" viewBox="0 0 16 12" fill="none" xmlns="http://www.w3.org/2000/svg">'
    +'<path d="M8 3.6C9.8 3.6 11.4 4.3 12.6 5.4L14.1 3.7C12.5 2.2 10.3 1.2 8 1.2C5.7 1.2 3.5 2.2 1.9 3.7L3.4 5.4C4.6 4.3 6.2 3.6 8 3.6Z" fill="'+c+'"/>'
    +'<path d="M8 7C9.1 7 10.1 7.4 10.8 8.1L12.3 6.4C11.2 5.4 9.7 4.8 8 4.8C6.3 4.8 4.8 5.4 3.7 6.4L5.2 8.1C5.9 7.4 6.9 7 8 7Z" fill="'+c+'"/>'
    +'<circle cx="8" cy="10.5" r="1.5" fill="'+c+'"/>'
    +'</svg>';
  // Battery SVG — outlined body with fill + nub
  var batt='<svg width="26" height="12" viewBox="0 0 26 12" fill="none" xmlns="http://www.w3.org/2000/svg">'
    +'<rect x="0.5" y="0.5" width="22" height="11" rx="2" stroke="'+c+'" stroke-opacity="0.35" stroke-width="1"/>'
    +'<rect x="2" y="2" width="19" height="8" rx="1" fill="'+c+'"/>'
    +'<path d="M24 4V8C24.8 7.7 25.5 6.9 25.5 6C25.5 5.1 24.8 4.3 24 4Z" fill="'+c+'" fill-opacity="0.4"/>'
    +'</svg>';
  return h('div',{style:{display:'flex',justifyContent:'space-between',alignItems:'center',
    padding:'12px 16px 4px',paddingTop:'max(12px, env(safe-area-inset-top, 12px))',
    fontSize:'15px',fontWeight:'600',color:c}},
    h('span',null,'9:41'),
    h('span',{style:{display:'flex',alignItems:'center',gap:'6px'},innerHTML:cell+wifi+batt})
  );
}
```

The SVGs use `fill` set to the same `c` color variable so they automatically flip between light and dark themes. The battery outline uses `stroke-opacity: 0.35` and the cap nub uses `fill-opacity: 0.4` to match iOS styling. Each icon is sized to match Apple's HIG proportions: cellular ~18×12, wifi ~16×12, battery ~26×12.

### Home Indicator
```javascript
function hind(dk){
  return h('div',{className:'hi'},
    h('div',{className:'hil',style:{background:dk?'var(--s100)':'var(--s900)'}})
  );
}
```

CSS for home indicator:
```css
.hi{height:32px;display:flex;align-items:flex-end;justify-content:center;padding-bottom:8px}
.hil{width:134px;height:5px;border-radius:50px}
```

### Illustration Placeholder
```javascript
function illo(size, isDark){
  return h('div',{style:{width:size+'px',height:size+'px',borderRadius:'50%',
    background:isDark?'var(--s800)':'var(--s300)',flexShrink:'0'}});
}
```

### Icon Circle (for feature rows, list items)
```javascript
function icircle(isDark){
  return h('div',{style:{width:'40px',height:'40px',borderRadius:'99px',
    background:isDark?'var(--s700)':'var(--s300)',flexShrink:'0'}});
}
```

### Initialization
```javascript
if(document.readyState==='loading')
  document.addEventListener('DOMContentLoaded',function(){mc.innerHTML='';render()});
else{mc.innerHTML='';render()}
```

Always clear `mc.innerHTML` before first render to remove the static HTML fallback.

## Desktop Chrome HTML

```html
<div id="app">
  <div id="tb"><button class="a" onclick="R()">[Flow Name]</button></div>
  <div id="sb">
    <span class="l">SCREEN:</span>
    <span id="sn" style="color:#aaa;font-size:11px;font-weight:600">Home</span>
    <button style="margin-left:auto;font-size:11px;padding:4px 10px;border-radius:99px;border:1px solid #444;background:#2a2a2a;color:#aaa;font-weight:600;cursor:pointer" onclick="R()">↺ Reset</button>
  </div>
  <div id="vp">
    <div style="position:relative">
      <div id="pf"><div id="pn"></div><div id="pc"></div></div>
      <div id="sl">[Flow Name] / Home</div>
    </div>
  </div>
</div>
<div id="ms"><div class="sa" id="mc">
  <!-- Static HTML fallback of the first screen goes here -->
</div></div>
```

## File Size Guidelines

Keep prototypes compact. Typical size: 15-35KB for a 4-8 screen flow.
- Compress CSS variable names (--s900 not --shade-900)
- Compress class names (.sc not .screen, .sh not .screen-header)
- Use inline styles via the `h()` helper rather than creating many CSS classes
- One-letter state variables in the IIFE (H, S, D, etc.)
- Use `var` not `const`/`let` for broader compatibility
