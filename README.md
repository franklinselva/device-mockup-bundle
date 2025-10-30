# Device Mockup Assets

This directory contains device mockup assets distributed via GitHub Pages.

## 📂 Structure

- `/assets/` - Raw PNG templates and masks (for reference/development)
- `/device-bundles/` - Protected binary bundles for production use (51 bundles)

## 📦 Device Bundles

Device bundles are binary-encoded files that contain device templates and masks in a protected format. They are **grouped by device model** for efficient lazy loading.

### Bundle Strategy

Each bundle contains **all color variants and orientations** for a specific device model:

- `apple-iphone-15-pro.bundle` - All iPhone 15 Pro colors (black, blue, white, natural titanium) + orientations
- `samsung-galaxy-s24.bundle` - All Galaxy S24 variants + orientations
- `google-pixel-8.bundle` - All Pixel 8 colors + orientations

### Size Distribution

| Size Range  | Count      | Examples                             |
| ----------- | ---------- | ------------------------------------ |
| **< 1 MB**  | 16 bundles | Apple Watch variants, small devices  |
| **1-3 MB**  | 18 bundles | iPads, single-color devices          |
| **3-7 MB**  | 12 bundles | iPhone Pro models, MacBooks          |
| **7-14 MB** | 5 bundles  | iPhone standard models (many colors) |

**Largest bundles:**

- `apple-iphone-15.bundle` - 13 MB (15 color/orientation variants)
- `apple-iphone-15-plus.bundle` - 9 MB (10 variants)
- `apple-iphone14plus.bundle` - 8.8 MB (10 variants)

**Average bundle size:** 2.93 MB

### Complete Bundle List

See `bundle-index.json` for the complete mapping of all 223 device variants to their bundle files.

## 🌐 Usage

### Option 1: Fetch via GitHub Pages (Recommended)

```javascript
// 1. Fetch the bundle index
const indexUrl =
  "https://franklinselva.github.io/device-mockup/device-bundles/bundle-index.json";
const index = await fetch(indexUrl).then((r) => r.json());

// 2. Find which bundle contains your device
const device = index.devices.find(
  (d) =>
    d.device_id === "apple-iphone-15-pro-black-titanium" &&
    d.orientation === "portrait"
);

console.log(`Bundle: ${device.bundle_file}, Model: ${device.model}`);
// Output: Bundle: apple-iphone-15-pro.bundle, Model: apple-iphone-15-pro

// 3. Fetch the bundle (only if not already cached)
const bundleUrl = `https://franklinselva.github.io/device-mockup/device-bundles/${device.bundle_file}`;
const bundleData = await fetch(bundleUrl).then((r) => r.arrayBuffer());

// 4. Decode using WASM module
const bundle = await wasmModule.decode_bundle(new Uint8Array(bundleData));
```

### Option 2: Direct Download

Browse and download bundles at:

**https://franklinselva.github.io/device-mockup/**

### For Rust Applications

```rust
use mockup_assets::BrandBundle;

// Load from file or URL
let bundle = BrandBundle::read_from_file("apple-iphone-15-pro.bundle")?;

// Find specific device variant
for device in &bundle.devices {
    if device.metadata.device_id == "apple-iphone-15-pro-black-titanium"
        && device.metadata.orientation == "portrait" {
        // Use device.template_data and device.mask_data (PNG bytes)
        let template_image = image::load_from_memory(&device.template_data)?;
        let mask_image = image::load_from_memory(&device.mask_data)?;
    }
}
```

## 🔒 Asset Protection

Bundles use XOR obfuscation to prevent trivial extraction of PNG files. This:

- Makes automated scraping more difficult
- Requires understanding of the bundle format to extract assets
- Preserves attribution to the original asset sources

**Note:** This is obfuscation, not cryptographic security - it's a deterrent against casual reuse.

## 📊 Statistics

- **Total bundles:** 51 device models
- **Total device variants:** 223 (including all colors and orientations)
- **Total size:** 149.33 MB (distributed across all bundles)
- **Average bundle:** 2.93 MB
- **Typical download:** 1-6 MB per device selection

## 💡 Benefits Over Raw PNGs

1. **Lazy Loading:** Download only the device model you need (~3 MB vs 150 MB)
2. **Asset Protection:** Obfuscated format prevents easy extraction
3. **CDN Caching:** GitHub Pages + Fastly CDN = fast global delivery
4. **Browser Caching:** Once downloaded, bundles are cached locally
5. **Grouped Intelligently:** All colors/orientations for a model in one bundle

## 📝 Credits

All device assets are sourced from official design resources:

- Apple devices: [Apple Design Resources](https://developer.apple.com/design/resources/)
- Samsung devices: Official Samsung assets
- Google Pixel: Google design resources

## 📄 License

Assets are provided for use with the Device Mockup application. Redistribution of extracted assets is not permitted. Please use the bundle format as provided.

## 🚀 GitHub Pages

These assets are served via GitHub Pages at:

```
https://franklinselva.github.io/device-mockup/
```

Cached globally via Fastly CDN for optimal performance.
