# Implementation Guide: Cross-Repository Feature Integration

## ⚠️ IMPORTANT: Current Status

This implementation is **PARTIALLY COMPLETE**. The following has been accomplished:

### ✅ Completed in ArchiveTune Repository:
1. OuterTune added as a submodule at `/OuterTune`
2. Download path preference keys (`DownloadPathKey`, `DownloadExtraPathKey`) added to `PreferenceKeys.kt`
3. This IMPLEMENTATION.md document created with full instructions

### ✅ Completed in OuterTune Submodule (uncommitted):
1. LastFM module copied with proper package renaming (`com.dd3boh.outertune.lastfm`)
2. ScrobbleManager utility added
3. settings.gradle.kts updated to include `:lastfm` module
4. app/build.gradle.kts updated to depend on lastfm module

### ⚠️ Required Manual Actions:

#### For OuterTune Scrobbling (Priority 1):
The OuterTune submodule has uncommitted changes. To complete this feature:

```bash
cd OuterTune
git add lastfm/ app/build.gradle.kts app/src/main/java/com/dd3boh/outertune/utils/ScrobbleManager.kt settings.gradle.kts
git commit -m "Add lastfm module and scrobbling infrastructure"
git push origin main  # or your working branch
cd ..
git add OuterTune
git commit -m "Update OuterTune submodule with scrobbling support"
```

Then follow the remaining steps in Phase 3 of the TODO list below.

#### For ArchiveTune External Downloads (Priority 2):
This requires porting complex file scanner and document utilities from OuterTune. See Phase 2 for details.

---

## Project Overview
This document tracks the implementation of two major features:
1. **Adding External Download Directory support to ArchiveTune** (from OuterTune)
2. **Adding Scrobbling support to OuterTune** (from ArchiveTune)

## TODO List

### Phase 1: Setup and Analysis ✅ COMPLETE
- [x] Analyze ArchiveTune's scrobbling service implementation
- [x] Identify download functionality in ArchiveTune
- [x] Add OuterTune as a git submodule using user's fork
- [x] Examine OuterTune's download implementation
- [x] Identify differences in external storage handling

### Phase 2: Add External Download Directory to ArchiveTune 🔄 IN PROGRESS
- [x] Add DownloadPathKey and DownloadExtraPathKey preference keys
- [ ] **MANUAL ACTION NEEDED**: Port utility files from OuterTune
  - Copy `OuterTune/app/src/main/java/androidx/documentfile/provider/TreeDocumentFileOt.java`
  - Copy scanner utilities from `OuterTune/app/src/main/java/com/dd3boh/outertune/utils/scanners/`
- [ ] **MANUAL ACTION NEEDED**: Port download managers
  - Copy `OuterTune/app/src/main/java/com/dd3boh/outertune/playback/downloadManager/`
  - Update package names from `com.dd3boh.outertune` to `moe.koiverse.archivetune`
- [ ] Update DownloadUtil to support external storage paths
- [ ] Add UI for selecting download directories in settings
- [ ] Test external download functionality

### Phase 3: Add Scrobbling to OuterTune 🔄 PARTIAL
- [x] Copy lastfm module to OuterTune submodule
- [x] Update OuterTune's settings.gradle.kts to include lastfm module
- [x] Update OuterTune's app build.gradle.kts to depend on lastfm
- [x] Copy ScrobbleManager to OuterTune
- [ ] **MANUAL ACTION NEEDED**: Commit OuterTune submodule changes
  - `cd OuterTune && git add . && git commit -m "Add lastfm module and scrobbling support"`
  - `git push origin main` (or appropriate branch)
- [ ] Add scrobbling preference keys to OuterTune
- [ ] Initialize LastFM in OuterTune's App class
- [ ] Integrate scrobbling into OuterTune's MusicService  
- [ ] Add LastFM settings UI to OuterTune
- [ ] Test scrobbling in OuterTune

### Phase 4: Documentation and Testing ⏸️ PENDING
- [x] Create comprehensive IMPLEMENTATION.md file
- [ ] Document testing procedures
- [ ] Final review and cleanup

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

**NOTE**: This is a complex feature requiring significant porting effort. The external download functionality in OuterTune depends on extensive file scanning and DocumentFile utilities.

**STEP 1: Port Required Utility Files**

Create directory: `app/src/main/kotlin/moe/koiverse/archivetune/utils/scanners/`

Port these files from `OuterTune/app/src/main/java/com/dd3boh/outertune/utils/scanners/`:
- `UriFileUtils.kt` - URI and file path conversion utilities
- Update package from `com.dd3boh.outertune` to `moe.koiverse.archivetune`

**STEP 2: Port TreeDocumentFileOt**

Copy `OuterTune/app/src/main/java/androidx/documentfile/provider/TreeDocumentFileOt.java` to:
`app/src/main/java/androidx/documentfile/provider/TreeDocumentFileOt.java`

This extends Android's DocumentFile with media ID tracking.

**STEP 3: Create Download Manager Package**

Create directory: `app/src/main/kotlin/moe/koiverse/archivetune/playback/downloadManager/`

Port these files from `OuterTune/app/src/main/java/com/dd3boh/outertune/playback/downloadManager/`:

1. `DirectoryMangerOt.kt` (note the typo in original name) → `DownloadDirectoryManagerOt.kt`
   - Manages multiple download directories
   - Handles file scanning and validation
   - Update package to `moe.koiverse.archivetune.playback.downloadManager`

2. `DownloadManagerOt.kt`
   - Handles download operations to external storage
   - Progress tracking via events
   - Update package to `moe.koiverse.archivetune.playback.downloadManager`

**STEP 4: Update DownloadUtil**

In `app/src/main/kotlin/moe/koiverse/archivetune/playback/DownloadUtil.kt`:

1. Add imports:
```kotlin
import moe.koiverse.archivetune.constants.DownloadPathKey
import moe.koiverse.archivetune.constants.DownloadExtraPathKey
import moe.koiverse.archivetune.playback.downloadManager.DownloadDirectoryManagerOt
import moe.koiverse.archivetune.playback.downloadManager.DownloadManagerOt
import moe.koiverse.archivetune.utils.scanners.uriListFromString
```

2. Add new properties:
```kotlin
var localMgr = DownloadDirectoryManagerOt(
    context,
    context.dataStore.get(DownloadPathKey, "").toUri(),
    uriListFromString(context.dataStore.get(DownloadExtraPathKey, ""))
)
val downloadMgr = DownloadManagerOt(localMgr)
var isProcessingDownloads = MutableStateFlow(false)
```

3. Add migration and scanning functions (see OuterTune's DownloadUtil for reference):
   - `migrateDownloads()` - Migrate from internal to external storage
   - `scanDownloads()` - Scan and import from external directories
   - `rescanDownloads()` - Refresh download status
   - `cd()` - Change directory

**STEP 5: Add Storage Settings UI**

Create `app/src/main/kotlin/moe/koiverse/archivetune/ui/screens/settings/StorageSettings.kt`:

Reference OuterTune's `StorageFrag.kt` for implementation. This should include:
- Directory picker for main download location
- Extra directories management
- Migration button from internal to external storage
- Scan/rescan controls
- Storage usage display

**STEP 6: Add to Settings Navigation**

Update your settings navigation to include the new StorageSettings screen.

**STEP 7: Add Required Permissions**

In `AndroidManifest.xml`, ensure you have:
```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" 
    android:maxSdkVersion="28" />
```

For Android 10+, you'll need to handle Storage Access Framework permissions.

**STEP 8: Testing**

1. Test internal downloads still work
2. Test external directory selection via DocumentsUI
3. Test downloads save to external directory
4. Test migration from internal to external
5. Test scan/rescan finds all files
6. Test playback from external storage
7. Test multiple extra directories

**Known Challenges**:
- LocalMediaScanner is extensive in OuterTune (1583 lines) - may need simplified version
- TreeDocumentFileOt extends Android framework classes - must remain in androidx package
- File scanning is CPU-intensive - requires background processing
- Permissions vary by Android version - need careful handling

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

**STEP 1: Commit OuterTune Submodule Changes** (see manual actions above)

**STEP 2: Add Scrobbling Preference Keys**

Add to `OuterTune/app/src/main/java/com/dd3boh/outertune/constants/PreferenceKeys.kt`:

```kotlin
// Last.fm scrobbling
val LastFmEnabledKey = booleanPreferencesKey("lastfm_enabled")
val LastFmUsernameKey = stringPreferencesKey("lastfm_username")
val LastFmSessionKeyKey = stringPreferencesKey("lastfm_session_key")
val ScrobbleDelayPercentKey = floatPreferencesKey("scrobble_delay_percent")
val ScrobbleMinSongDurationKey = intPreferencesKey("scrobble_min_song_duration")
val ScrobbleDelaySecondsKey = intPreferencesKey("scrobble_delay_seconds")
val UseNowPlayingKey = booleanPreferencesKey("use_now_playing")
```

**STEP 3: Initialize LastFM in App Class**

Find `OuterTune/app/src/main/java/com/dd3boh/outertune/App.kt` and add:

```kotlin
import com.dd3boh.outertune.lastfm.LastFM

// In onCreate() or similar initialization:
LastFM.initialize(
    apiKey = BuildConfig.LASTFM_API_KEY,  // You'll need to add these to BuildConfig
    secret = BuildConfig.LASTFM_SECRET
)
```

**STEP 4: Add API Keys**

Add LastFM API keys to your build configuration. You can:
- Use GitHub Secrets for CI/CD
- Add to local.properties for local builds
- Add to BuildConfig in build.gradle.kts

**STEP 5: Integrate into MusicService**

In `OuterTune/app/src/main/java/com/dd3boh/outertune/playback/MusicService.kt`:

1. Add ScrobbleManager field:
```kotlin
private var scrobbleManager: ScrobbleManager? = null
```

2. Initialize in onCreate():
```kotlin
// Load preferences
val lastFmEnabled = dataStore[LastFmEnabledKey] ?: false
if (lastFmEnabled) {
    val sessionKey = dataStore[LastFmSessionKeyKey]
    if (sessionKey != null) {
        LastFM.sessionKey = sessionKey
        scrobbleManager = ScrobbleManager(
            scope = serviceScope,
            minSongDuration = dataStore[ScrobbleMinSongDurationKey] ?: 30,
            scrobbleDelayPercent = dataStore[ScrobbleDelayPercentKey] ?: 0.5f,
            scrobbleDelaySeconds = dataStore[ScrobbleDelaySecondsKey] ?: 180
        ).apply {
            useNowPlaying = dataStore[UseNowPlayingKey] ?: true
        }
    }
}
```

3. Hook into player state changes:
```kotlin
// In player listener or where playback state changes
scrobbleManager?.onPlayerStateChanged(
    isPlaying = player.isPlaying,
    metadata = currentMetadata,
    duration = player.duration
)

// On song change
scrobbleManager?.onSongStop()
scrobbleManager?.onSongStart(newMetadata, newDuration)
```

4. Cleanup in onDestroy():
```kotlin
scrobbleManager?.destroy()
```

**STEP 6: Add LastFM Settings UI**

This is more involved. You'll need to create a settings screen similar to ArchiveTune's `LastFMSettings.kt`. 
The file includes:
- Authentication options (OAuth or username/password)
- Session status display
- Configuration options for scrobbling behavior
- Logout functionality

Reference: `/app/src/main/kotlin/moe/koiverse/archivetune/ui/screens/settings/LastFMSettings.kt` in ArchiveTune

**STEP 7: Add Navigation**

Update OuterTune's navigation/settings to include the LastFM settings screen.

**STEP 8: Testing**

1. Build and run OuterTune
2. Go to LastFM settings
3. Authenticate with Last.fm
4. Play songs and verify scrobbling on Last.fm website
5. Test pause/resume behavior
6. Test short songs don't scrobble

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
