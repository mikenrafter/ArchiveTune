# Implementation Guide: External Download Directory for ArchiveTune

## 📋 Project Overview

This document tracks the implementation of external download directory support for ArchiveTune, allowing users to save their downloaded music to a custom location on external storage.

## ⚠️ Current Status

**In Progress** - Implementing external download directory feature.

### ✅ Completed:
1. Download path preference keys added to `PreferenceKeys.kt`

### 🔄 In Progress:
1. Creating download directory manager
2. Updating DownloadUtil to support external paths
3. Adding settings UI

---

## TODO List

### Phase 1: Core Infrastructure ✅
- [x] Add DownloadPathKey and DownloadExtraPathKey preference keys

### Phase 2: External Download Implementation 🔄
- [ ] Create download directory manager
- [ ] Update AppModule to support external download path
- [ ] Update DownloadUtil to use external storage
- [ ] Add settings UI for directory selection
- [ ] Test external download functionality

---

## Feature Implementation

### Overview

The external download directory feature allows users to:
- Select a custom directory for downloads using Storage Access Framework
- Save downloaded music files to external storage
- Fall back to internal storage if no external path is configured

### Key Components

#### 1. Download Path Configuration
Location: `app/src/main/kotlin/moe/koiverse/archivetune/constants/PreferenceKeys.kt`

Already added:
```kotlin
val DownloadPathKey = stringPreferencesKey("dlPath")
val DownloadExtraPathKey = stringPreferencesKey("dlExtraPath")
```

#### 2. Download Directory Manager
A simplified manager to handle external storage paths using DocumentFile API.

#### 3. AppModule Updates
Update the download cache provider to support external paths when configured.

#### 4. Settings UI
Add UI to allow users to select download directory using Android's document picker.

---

## Implementation Steps

### Step 1: Create Download Directory Manager ✅

Create a simple manager to handle external storage using DocumentFile.

### Step 2: Update AppModule

Modify `provideDownloadCache` to check for user-configured external path and use it if available.

### Step 3: Add Settings UI

Add a settings screen with:
- Option to select external download directory
- Display current download location
- Button to change directory using SAF picker

### Step 4: Testing

Test scenarios:
- Download with internal storage (default)
- Select external directory
- Download to external directory
- Verify files are saved correctly
- Verify playback works from external storage

---

## Technical Notes

### Storage Access Framework
- Uses Android's DocumentFile API for external storage access
- Requires user to grant directory access permission
- Persists URI permission across app restarts

### Compatibility
- Works on Android 5.0+ (API 21+)
- No special permissions needed (uses SAF)
- Graceful fallback to internal storage

### File Structure
- Downloads saved as: `[Title] [VideoId].mka`
- Compatible with existing internal download format
- Can coexist with internal downloads

---

## Testing Checklist

- [ ] Can select external directory via settings
- [ ] Downloads save to external directory when configured
- [ ] Falls back to internal storage when no external path set
- [ ] Downloaded files play correctly
- [ ] App handles directory access revocation gracefully
- [ ] Settings UI displays current download location
- [ ] Can change download directory
- [ ] Existing internal downloads continue to work

