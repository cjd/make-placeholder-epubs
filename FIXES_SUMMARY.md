# Summary of Fixes for make-placeholder-epubs

## Issues Identified & Fixed

### 1. iOS Camera Enumeration (CRITICAL)
**Problem:** Only 1 camera shows in dropdown on iOS Chrome
**Root cause:** Line 1755 - `getVideoInputDevices()` called on page load before permission granted
**Solution:** Added "Refresh Cameras" button that re-enumerates after permission granted

**Files changed:**
- `index.php` line ~1080: Add refresh button HTML
- `index.php` line ~1245: Add refresh button click handler

### 2. Camera Preference Logic (BROKEN)
**Problem:** Lines 1760-1771 try to detect "back" or "environment" in camera labels
**Root cause:** iOS labels cameras as "Video Device 1", "Video Device 2" - no keywords to match
**Solution:** Remove complex detection, just default to first camera, let user choose

**Files changed:**
- `index.php` lines 1760-1771: Simplify to just use first camera

### 3. API Keys Missing (NETWORK ERROR)
**Problem:** App gets "network error" when scanning ISBN
**Root cause:** No API keys configured
**Solution:** Need `.env` file or docker-compose environment variables

**Required:**
- `HARDCOVER_BEARER_TOKEN` - Get from https://hardcover.app/account/api
- `GEMINI_API_KEY` - Get from https://aistudio.google.com (optional)

## Code Changes Summary

### Change 1: Add Refresh Cameras Button

**Location:** After line 1079 (after camera select dropdown)

```html
<button id="refresh-cameras-btn" 
    class="mt-2 w-full bg-gray-600 text-white py-2 px-4 rounded-lg hover:bg-gray-700 transition duration-150">
    🔄 Refresh Cameras (for iOS)
</button>
```

### Change 2: Add Refresh Button Handler

**Location:** After line 1245 (after cameraSelect is defined)

```javascript
const refreshCamerasBtn = document.getElementById('refresh-cameras-btn');
refreshCamerasBtn.addEventListener('click', () => {
  console.log('Refreshing camera list...');
  codeReader.getVideoInputDevices().then(devices => {
    console.log('Found', devices.length, 'cameras');
    const currentValue = cameraSelect.value;
    cameraSelect.innerHTML = '';
    devices.forEach((device, index) => {
      const option = document.createElement('option');
      option.value = device.deviceId;
      option.text = device.label || `Camera ${index + 1}`;
      cameraSelect.appendChild(option);
    });
    if (currentValue && cameraSelect.querySelector(`option[value="${currentValue}"]`)) {
      cameraSelect.value = currentValue;
    }
  }).catch(err => console.error('Camera refresh error:', err));
});
```

### Change 3: Simplify Camera Selection Logic

**Location:** Lines 1758-1771

**Replace this:**
```javascript
let preferredDeviceId = null;

// **PRIORITY LOGIC: Check for 'back' or 'environment' to select rear camera**
const rearCamera = videoInputDevices.find(device => {
    const label = device.label.toLowerCase();
    return label.includes('back') || label.includes('environment');
});

if (rearCamera) {
    preferredDeviceId = rearCamera.deviceId;
} else if (videoInputDevices.length > 0) {
    // Fallback to the first device found
    preferredDeviceId = videoInputDevices[0].deviceId;
}
```

**With this:**
```javascript
let preferredDeviceId = null;

// Default to first camera found (user can select others from dropdown)
if (videoInputDevices.length > 0) {
    preferredDeviceId = videoInputDevices[0].deviceId;
}
```

## Testing Workflow

### For iOS:
1. Open app
2. Click "Start Barcode Scan" → Grant camera permission
3. Click "🔄 Refresh Cameras" 
4. Dropdown now shows both cameras
5. Select rear camera
6. Scan ISBN

### For API Keys:
1. Create `.env` file with API tokens
2. Restart app
3. Scan ISBN → Should fetch book metadata
4. Generate EPUB file

## Future Improvements

Could add:
- Remember last selected camera in localStorage
- Auto-detect if on iOS and show helpful message
- Better error messages when API keys are missing
- Fallback to Open Library API if Hardcover fails

## Docker Build

After making changes, build with:

```bash
docker build -t placeholder-epubs:latest .
docker-compose down
docker-compose up -d
```

Make sure your fork is referenced in the Dockerfile.
