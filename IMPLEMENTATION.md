# Implementation Guide: Cross-Repository Feature Integration

## Project Overview
This document tracks the implementation of two major features:
1. **Adding External Download Directory support to ArchiveTune** (from OuterTune)
2. **Adding Scrobbling support to OuterTune** (from ArchiveTune)

## TODO List

### Phase 1: Setup and Analysis ✅
- [x] Analyze ArchiveTune's scrobbling service implementation
- [x] Identify download functionality in ArchiveTune
- [x] Add OuterTune as a git submodule using user's fork
- [x] Examine OuterTune's download implementation
- [x] Identify differences in external storage handling

### Phase 2: Add External Download Directory to ArchiveTune 🔄
- [ ] Add DownloadPathKey and DownloadExtraPathKey preference keys
- [ ] Create DownloadDirectoryManagerOt class for managing external directories
- [ ] Create DownloadManagerOt class for file operations
- [ ] Update DownloadUtil to support external storage paths
- [ ] Add UI for selecting download directories in settings
- [ ] Add migration functionality from internal to external storage
- [ ] Test external download functionality

### Phase 3: Add Scrobbling to OuterTune 🔄
- [ ] Copy lastfm module to OuterTune submodule
- [ ] Update OuterTune's settings.gradle.kts to include lastfm module
- [ ] Copy ScrobbleManager to OuterTune
- [ ] Add scrobbling constants and preferences
- [ ] Integrate scrobbling into OuterTune's MusicService
- [ ] Add LastFM settings UI to OuterTune
- [ ] Add LastFM/ListenBrainz integration screen
- [ ] Test scrobbling in OuterTune

### Phase 4: Documentation and Testing ⏸️
- [x] Create comprehensive IMPLEMENTATION.md file
- [ ] Document setup steps for both projects
- [ ] Document testing procedures
- [ ] Add troubleshooting guide
- [ ] Commit and push all changes

---

## Feature 1: External Download Directory for ArchiveTune

### Background
OuterTune has advanced download management that allows users to specify custom download directories on external storage. This feature needs to be ported to ArchiveTune.

### Key Components to Port

#### 1. Preference Keys
**Location**: `app/src/main/kotlin/moe/koiverse/archivetune/constants/PreferenceKeys.kt`

Add the following preference keys:
```kotlin
val DownloadPathKey = stringPreferencesKey("dlPath")
val DownloadExtraPathKey = stringPreferencesKey("dlExtraPath")
```

#### 2. Download Directory Manager
**New File**: `app/src/main/kotlin/moe/koiverse/archivetune/playback/downloadManager/DownloadDirectoryManagerOt.kt`

This class manages multiple download directories:
- Main download directory
- Extra download directories
- File scanning and validation
- URI to file path conversion

**Source**: `OuterTune/app/src/main/java/com/dd3boh/outertune/playback/downloadManager/DownloadDirectoryManagerOt.kt`

#### 3. Download Manager
**New File**: `app/src/main/kotlin/moe/koiverse/archivetune/playback/downloadManager/DownloadManagerOt.kt`

Handles file operations:
- Enqueueing downloads to external storage
- File copying and management
- Progress tracking

**Source**: `OuterTune/app/src/main/java/com/dd3boh/outertune/playback/downloadManager/DownloadManagerOt.kt`

#### 4. Update DownloadUtil
**File**: `app/src/main/kotlin/moe/koiverse/archivetune/playback/DownloadUtil.kt`

Changes needed:
- Add localMgr and downloadMgr properties
- Add migration support from internal to external storage
- Add scan/rescan functionality
- Update download tracking to support both internal and external

#### 5. Storage Settings UI
**New File**: `app/src/main/kotlin/moe/koiverse/archivetune/ui/screens/settings/StorageSettings.kt`

UI for:
- Selecting main download directory
- Adding/removing extra download directories
- Migrating existing downloads
- Scanning/rescanning downloads

#### 6. Required Utilities
**Files to create/update**:
- `app/src/main/kotlin/moe/koiverse/archivetune/utils/scanners/FileUtils.kt` - File scanning utilities
- `app/src/main/kotlin/moe/koiverse/archivetune/utils/Coroutines.kt` - Download coroutine context

### Implementation Steps

1. **Add Constants**
   - Add DownloadPathKey and DownloadExtraPathKey to PreferenceKeys.kt

2. **Create Download Managers**
   - Create downloadManager package
   - Port DownloadDirectoryManagerOt
   - Port DownloadManagerOt

3. **Update DownloadUtil**
   - Add external storage support
   - Add migration functionality
   - Update download state tracking

4. **Add Settings UI**
   - Create StorageSettings screen
   - Add directory picker
   - Add migration button
   - Add scan/rescan controls

5. **Testing**
   - Test internal downloads still work
   - Test external directory selection
   - Test migration from internal to external
   - Test scan/rescan functionality

---

## Feature 2: Scrobbling Support for OuterTune

### Background
ArchiveTune has full Last.fm and ListenBrainz scrobbling support. This needs to be ported to OuterTune.

### Key Components to Port

#### 1. LastFM Module
**Directory**: Copy entire `lastfm` module

Contains:
- LastFM API client
- Authentication (OAuth and mobile session)
- Scrobbling methods
- Now Playing updates

Files to copy:
- `lastfm/build.gradle.kts`
- `lastfm/src/main/kotlin/moe/koiverse/archivetune/lastfm/LastFM.kt`
- `lastfm/src/main/kotlin/moe/koiverse/archivetune/lastfm/models/Authentication.kt`

#### 2. ScrobbleManager
**Source File**: `app/src/main/kotlin/moe/koiverse/archivetune/utils/ScrobbleManager.kt`
**Target**: `OuterTune/app/src/main/java/com/dd3boh/outertune/utils/ScrobbleManager.kt`

Manages:
- Song start/stop/pause/resume tracking
- Scrobble timing (50% or 4 minutes)
- Now Playing updates
- Integration with LastFM API

#### 3. LastFM Settings UI
**Source File**: `app/src/main/kotlin/moe/koiverse/archivetune/ui/screens/settings/LastFMSettings.kt`
**Target**: `OuterTune/app/src/main/java/com/dd3boh/outertune/ui/screens/settings/LastFMSettings.kt`

Provides:
- LastFM login (OAuth and username/password)
- Session management
- Configuration options
- Logout functionality

#### 4. Integration Screen
**Source File**: `app/src/main/kotlin/moe/koiverse/archivetune/ui/screens/settings/IntegrationScreen.kt`
**Target**: `OuterTune/app/src/main/java/com/dd3boh/outertune/ui/screens/settings/IntegrationScreen.kt`

Navigation screen for:
- LastFM settings
- ListenBrainz settings (future)
- Other integrations

#### 5. Preference Keys
Add to OuterTune's PreferenceKeys:
```kotlin
val LastFmEnabledKey = booleanPreferencesKey("lastfm_enabled")
val LastFmUsernameKey = stringPreferencesKey("lastfm_username")
val LastFmSessionKeyKey = stringPreferencesKey("lastfm_session_key")
val ScrobbleDelayPercentKey = floatPreferencesKey("scrobble_delay_percent")
val ScrobbleMinSongDurationKey = intPreferencesKey("scrobble_min_song_duration")
val ScrobbleDelaySecondsKey = intPreferencesKey("scrobble_delay_seconds")
val UseNowPlayingKey = booleanPreferencesKey("use_now_playing")
```

#### 6. MusicService Integration
**File**: `OuterTune/app/src/main/java/com/dd3boh/outertune/playback/MusicService.kt`

Add:
- ScrobbleManager initialization
- Integration with player state changes
- Configuration from preferences

### Implementation Steps

1. **Copy LastFM Module**
   - Copy lastfm directory to OuterTune
   - Update package names from `moe.koiverse.archivetune` to `com.dd3boh.outertune`
   - Update settings.gradle.kts to include lastfm module
   - Update OuterTune's app build.gradle.kts to depend on lastfm

2. **Add App Integration**
   - Initialize LastFM in App.kt
   - Add API keys (from BuildConfig or GitHub Secrets)

3. **Copy ScrobbleManager**
   - Port ScrobbleManager.kt
   - Update package references
   - Ensure MediaMetadata compatibility

4. **Add Preferences**
   - Add LastFM preference keys
   - Add scrobbling configuration keys

5. **Integrate with MusicService**
   - Add ScrobbleManager to MusicService
   - Hook into player state changes
   - Add configuration loading

6. **Add Settings UI**
   - Copy LastFMSettings.kt
   - Copy IntegrationScreen.kt
   - Update NavigationBuilder to include new screens
   - Ensure proper routing

7. **Testing**
   - Test LastFM authentication
   - Test scrobbling with various song durations
   - Test pause/resume behavior
   - Test Now Playing updates

---

## File Locations Reference

### ArchiveTune Files
```
lastfm/
  build.gradle.kts
  src/main/kotlin/moe/koiverse/archivetune/lastfm/
    LastFM.kt
    models/Authentication.kt

app/src/main/kotlin/moe/koiverse/archivetune/
  utils/ScrobbleManager.kt
  ui/screens/settings/
    LastFMSettings.kt
    IntegrationScreen.kt
  constants/PreferenceKeys.kt
  playback/
    DownloadUtil.kt
    MusicService.kt
  App.kt
```

### OuterTune Files
```
OuterTune/
  app/src/main/java/com/dd3boh/outertune/
    playback/
      DownloadUtil.kt
      downloadManager/
        DownloadDirectoryManagerOt.kt
        DownloadManagerOt.kt
    ui/screens/settings/fragments/
      StorageFrag.kt
    constants/PreferenceKeys.kt
```

---

## Dependencies

### Required in OuterTune for Scrobbling
```kotlin
// In lastfm/build.gradle.kts
dependencies {
    implementation(libs.ktor.client.core)
    implementation(libs.ktor.client.okhttp)
    implementation(libs.ktor.client.content.negotiation)
    implementation(libs.ktor.serialization.json)
    implementation(libs.ktor.client.encoding)
}

// In app/build.gradle.kts
dependencies {
    implementation(project(":lastfm"))
}
```

### Required in ArchiveTune for External Downloads
- DocumentFile API (already available in Android)
- Storage Access Framework permissions

---

## Permissions

### ArchiveTune (for external storage)
```xml
<uses-permission android:name="android.permission.MANAGE_EXTERNAL_STORAGE" />
```

### OuterTune (already has required permissions)
No additional permissions needed for scrobbling (internet already granted)

---

## Testing Checklist

### External Downloads (ArchiveTune)
- [ ] Internal downloads continue to work
- [ ] Can select external download directory
- [ ] Can add multiple extra directories
- [ ] Migration from internal to external works
- [ ] Scan/rescan finds all downloads
- [ ] Downloads appear in selected directory
- [ ] Downloaded files play correctly

### Scrobbling (OuterTune)
- [ ] Can authenticate with LastFM (OAuth)
- [ ] Can authenticate with LastFM (username/password)
- [ ] Songs scrobble at 50% or 4 minutes
- [ ] Now Playing updates correctly
- [ ] Pause/resume preserves scrobble timer
- [ ] Short songs (<30s) don't scrobble
- [ ] Settings persist across app restarts
- [ ] Logout clears session

---

## Troubleshooting

### External Downloads Issues
1. **Downloads not appearing in external directory**
   - Check directory permissions
   - Verify URI is valid
   - Check DocumentFile access

2. **Migration fails**
   - Ensure sufficient storage space
   - Check file permissions
   - Verify cache is readable

### Scrobbling Issues
1. **Songs not scrobbling**
   - Verify LastFM session is valid
   - Check internet connectivity
   - Verify API keys are set

2. **Authentication fails**
   - Check API key and secret
   - Verify network connection
   - Try alternative auth method

---

## Notes
- Both features are independent and can be implemented in parallel
- Maintain package naming conventions for each project
- Test thoroughly after each major change
- Keep this document updated as implementation progresses
