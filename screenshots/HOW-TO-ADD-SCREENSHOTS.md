# How to Add Screenshots

Place your screenshots in the folders below, then update `index.html` to show them.

## Folder Structure

```
screenshots/
├── lead-hunter/
│   ├── screen1.png     ← Dashboard or main UI
│   └── screen2.png     ← AI agent in action / search results
├── meat-and-more/
│   ├── screen1.png     ← Home screen / restaurant listing
│   └── screen2.png     ← Order tracking / checkout
├── coatops/
│   ├── screen1.png     ← Site dashboard / attendance view
│   └── screen2.png     ← Progress report / material tracker
└── snapkart/
    ├── screen1.png     ← Product catalog / storefront
    └── screen2.png     ← Admin panel
```

## How to Activate a Screenshot in index.html

Find the relevant `screenshot-slot` div and uncomment the `<img>` tag inside it:

**Before (placeholder):**
```html
<div class="screenshot-slot">
  <!-- <img src="screenshots/lead-hunter/screen1.png" alt="Dashboard" onclick="openLightbox(this.src)"> -->
  <div class="slot-icon">🖼️</div>
  <span>Add screenshot...</span>
</div>
```

**After (with real screenshot):**
```html
<div class="screenshot-slot">
  <img src="screenshots/lead-hunter/screen1.png" alt="Dashboard" onclick="openLightbox(this.src)">
</div>
```

## How to Add a Demo URL

Find the Live Demo row for your project and replace the yellow warning div:

```html
<!-- Replace this: -->
<div class="todo">⚠️ Add demo URL...</div>

<!-- With this: -->
<a class="btn btn-outline" href="https://your-demo-url.com" target="_blank">🌐 Open Demo</a>
```

## How to Add Login Credentials

Find the Credentials row and replace the yellow warning div:

```html
<!-- Replace this: -->
<div class="todo">⚠️ Add demo login credentials here</div>

<!-- With this: -->
<div class="creds-box">
  <div class="cred-item">
    <span class="cred-key">Email</span>
    <span class="cred-val">demo@example.com</span>
  </div>
  <div class="cred-item">
    <span class="cred-key">Password</span>
    <span class="cred-val">Demo@1234</span>
  </div>
</div>
```
