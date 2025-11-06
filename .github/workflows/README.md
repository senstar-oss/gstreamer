# GStreamer GitHub Actions Workflows

This directory contains modular GitHub Actions workflows for building, testing, and maintaining GStreamer with all plugins.

## Workflow Architecture

The workflow system uses a **modular design** with a master dispatcher and specialized build workflows:

### Master Dispatcher (`dispatcher.yml`)
- **Primary entry point** for all builds
- **Triggers:** Push to main/master, Pull requests, Manual dispatch with options
- **Orchestrates** parallel execution of platform-specific builds
- **Manages** plugin and feature configuration across all platforms

### Modular Build Workflows

#### 1. `build-linux.yml` - Linux Build
- **Platform:** Ubuntu 22.04 LTS
- **Features:** Full GStreamer build with introspection, comprehensive dependencies
- **Supports:** All plugin sets, GPL codecs, WebRTC, testing

#### 2. `build-windows-mingw.yml` - Windows MinGW Build  
- **Platform:** Windows Server 2022 with MSYS2/MinGW
- **Features:** Unix-like build environment, extensive codec support
- **Supports:** Cross-platform compatibility, comprehensive plugin coverage

#### 3. `build-windows-msvc.yml` - Windows MSVC Build
- **Platform:** Windows Server 2022 with Visual Studio 2022
- **Features:** Native Windows compiler, optimized performance
- **Supports:** Windows SDK integration, DirectX compatibility

#### 4. `build-macos.yml` - macOS Build
- **Platform:** macOS 14 with Homebrew dependencies
- **Features:** Native macOS build with platform-specific optimizations
- **Supports:** All plugin sets, GPL codecs, WebRTC

#### 5. `verify-plugins.yml` - Plugin Verification
- **Purpose:** Validates that built plugins load and function correctly
- **Platform:** Ubuntu 22.04 (uses Linux artifacts)
- **Tests:** Plugin enumeration, basic functionality checks

#### 6. `create-release.yml` - Release Management
- **Purpose:** Creates GitHub releases with build artifacts
- **Triggers:** Automatic on main/master push, optional on manual dispatch
- **Artifacts:** Collects and packages all platform builds

### Manual Dispatch Options (Dispatcher)
- **Platform Selection**: Choose which platforms to build (max 4 platforms)
- **Plugin Configuration**: Comma-separated list (e.g., "base,good,bad,ugly,libav")
- **Feature Configuration**: Comma-separated features (e.g., "gpl,webrtc,tests")
- **Build Type**: release, debug, or both
- **Release Control**: Option to create GitHub release (main branch only)

### Additional Workflows

#### 7. `plugin-matrix.yml` - Plugin Configuration Testing
- **Triggers:** Changes to plugin directories, Manual dispatch
- **Purpose:** Tests different combinations of plugins independently
- **Test Matrix:** Various plugin combinations (base only, base+good, etc.)

#### 8. `code-quality.yml` - Code Quality Checks  
- **Triggers:** Manual dispatch only
- **Checks:** Pre-commit hooks, Python syntax, security scanning
- **Tools:** pre-commit, uncrustify, gitlint, trivy

**Plugins included:**
- ✅ **Core GStreamer** - Essential GStreamer libraries
- ✅ **Base plugins** (`gst-plugins-base`) - Fundamental plugins for audio/video playback, encoding, and protocol support
- ✅ **Good plugins** (`gst-plugins-good`) - High-quality, well-maintained plugins with good code quality
- ✅ **Bad plugins** (`gst-plugins-bad`) - Experimental plugins, often with newer features or less mature code
- ✅ **Ugly plugins** (`gst-plugins-ugly`) - Good quality plugins that might have distribution issues (patents, licensing)
- ✅ **LibAV/FFmpeg** (`gst-libav`) - FFmpeg integration for extensive codec support
- ✅ **GES** (GStreamer Editing Services) - Non-linear video editing capabilities
- ✅ **RTSP Server** - Real Time Streaming Protocol server
- ✅ **Dev tools** - Development and debugging tools

**Build configurations:**
- Release and Debug builds for Linux
- Release builds for Windows and macOS
- GPL plugins enabled (includes patented codecs)
- WebRTC support enabled
- ORC optimization enabled
- Introspection enabled (Linux/macOS)

**Artifacts:**
- Compressed archives of built binaries for each platform
- Automatic releases on main branch pushes

### 2. `plugin-matrix.yml` - Plugin Configuration Testing

**Triggers:** Changes to plugin directories, Manual dispatch

**Purpose:** Tests different combinations of plugins to ensure they can be built independently.

**Test Matrix:**
- Base only
- Base + Good
- Base + Good + Bad  
- Base + Good + Ugly
- All except LibAV
- All plugins

**Features:**
- Manual testing of specific plugin sets
- Plugin counting and verification
- Dependency validation

### 3. `code-quality.yml` - Code Quality Checks

**Triggers:** Push to main/master, Pull requests

**Checks:**
- Pre-commit hooks validation
- Python script syntax checking
- Meson build file validation
- Security scanning with Trivy
- Sensitive data detection

**Tools used:**
- `pre-commit` - Code formatting and linting
- `uncrustify` - C/C++ code formatting
- `gitlint` - Commit message validation
- `trivy` - Vulnerability scanning

## Plugin Details

### Base Plugins (`gst-plugins-base`)
Essential plugins that most applications need:
- **Audio**: audiorate, audioconvert, audioresample, volume
- **Video**: videorate, videoconvert, videoscale
- **Playback**: playbin, decodebin, uridecodebin
- **Network**: tcp, udp
- **Formats**: ogg, vorbis, theora, opus

### Good Plugins (`gst-plugins-good`)
Stable, well-maintained plugins:
- **Formats**: avi, flv, matroska, mp4, wav
- **Codecs**: vpx, png, jpeg, flac, speex, lame
- **Effects**: audioecho, videomixer, alpha
- **Sources**: v4l2, pulse, jack, osxaudio
- **Network**: soup (HTTP), rtsp, rtp, udp multicast

### Bad Plugins (`gst-plugins-bad`)
Experimental or specialized plugins:
- **Advanced codecs**: h264parse, h265parse, av1
- **WebRTC**: webrtc, dtls, srtp, nice
- **Computer Vision**: opencv
- **Advanced effects**: facedetect, geometrictransform
- **Hardware acceleration**: va, nvcodec, msdk
- **Specialized formats**: mxf, dash, hls

### Ugly Plugins (`gst-plugins-ugly`)
Good quality but potentially legally problematic:
- **MP3**: mad (decoder), lame (encoder)  
- **x264**: High-quality H.264 encoder
- **DVD**: dvdread, dvdnav
- **Other patented**: amrnb, amrwb, sid

### LibAV (`gst-libav`)
FFmpeg integration providing:
- Extensive codec support (hundreds of formats)
- Hardware acceleration
- Advanced filtering capabilities
- Format conversion and muxing/demuxing

## Usage Examples

### Running the workflows

1. **Automatic builds**: Push to main/master branch
   - Triggers dispatcher which runs all platform builds in parallel
   - Uses default configuration (all plugins, GPL enabled, release builds)
   - Creates GitHub release automatically

2. **Custom builds**: Go to Actions tab → "GStreamer Build Dispatcher" → "Run workflow"
   - **Platform Selection**: Check desired platforms (Linux, Windows MinGW, Windows MSVC, macOS)
   - **Plugin Configuration**: Enter comma-separated list (e.g., "base,good,bad")
   - **Feature Configuration**: Enter features (e.g., "gpl,webrtc" or "webrtc,tests")
   - **Build Type**: Choose release, debug, or both
   - **Create Release**: Option to create GitHub release (main branch only)

3. **Individual platform builds**: Go to specific build workflow (e.g., "Build GStreamer - Linux")
   - Useful for testing platform-specific changes
   - Same plugin/feature configuration options

4. **Quality checks**: Go to Actions tab → "Code Quality" → "Run workflow"
   - Manual-only execution for code quality validation

### Modular Build Examples

**Linux-only debug build with minimal plugins:**
```yaml
Platforms: ✅ Linux only
Plugins: "base"  
Features: "webrtc"
Build Type: debug
```

**Windows comparison build (both MinGW and MSVC):**
```yaml
Platforms: ✅ Windows MinGW + Windows MSVC
Plugins: "base,good,bad,ugly,libav"
Features: "gpl,webrtc"  
Build Type: release
```

**Cross-platform build without patent issues:**
```yaml
Platforms: ✅ All platforms
Plugins: "base,good,bad" (excludes ugly/libav)
Features: "webrtc" (excludes gpl)
Build Type: release
```

**Development build with testing:**
```yaml
Platforms: ✅ Linux only
Plugins: "base,good,bad,ugly,libav"
Features: "gpl,webrtc,tests"
Build Type: both (includes debug for testing)
```

### Using built binaries

Linux/macOS:
```bash
# Extract the tarball
tar -xzf gstreamer-release-linux.tar.gz

# Set environment
export GST_PLUGIN_PATH=$PWD/usr/lib/gstreamer-1.0
export LD_LIBRARY_PATH=$PWD/usr/lib  
export PATH=$PWD/usr/bin:$PATH

# Test installation
gst-inspect-1.0 --version
gst-launch-1.0 videotestsrc ! autovideosink
```

Windows:
```powershell
# Extract the zip file
# Add bin directory to PATH
$env:PATH = "path\to\extracted\bin;$env:PATH"

# Test installation
gst-inspect-1.0.exe --version
gst-launch-1.0.exe videotestsrc ! autovideosink
```

## Build Requirements

### System Dependencies
The workflows install comprehensive dependencies for multimedia support:

**Audio codecs**: Opus, Vorbis, FLAC, MP3, AAC, AMR, Speex  
**Video codecs**: VP8/VP9, AV1, H.264, H.265, Theora  
**Containers**: MP4, MKV, AVI, WebM, FLV  
**Protocols**: RTSP, RTP, HTTP, UDP  
**Hardware**: VA-API, VDPAU, OpenGL, Vulkan  
**Development**: Introspection, debugging tools, documentation

### Build Options
Key meson options used:
- `-Dgpl=enabled` - Include GPL-licensed plugins
- `-Dorc=enabled` - Enable SIMD optimizations
- `-Dwebrtc=enabled` - WebRTC support
- `-Dintrospection=enabled` - GObject Introspection (Linux/macOS)
- `-Dtests=enabled` - Build test suites (debug builds)

## Troubleshooting

### Common Issues

1. **Missing dependencies**: The workflows include comprehensive dependency installation
2. **Build failures**: Check the plugin matrix workflow for specific plugin issues
3. **Runtime issues**: Ensure proper environment variables are set (see usage examples)

### Platform-Specific Notes

- **Linux**: Uses Ubuntu 22.04 LTS with apt packages, full introspection support
- **Windows MinGW**: Uses MSYS2/MinGW for consistent Unix-like build environment
- **Windows MSVC**: Uses Visual Studio 2022 compiler, native Windows build with `--vsenv`
- **macOS**: Uses Homebrew for dependency management on macOS 14

### MSVC vs MinGW Builds

**MSVC Build Benefits:**
- Native Windows compiler and toolchain
- Better integration with Windows development tools
- Optimized for Windows performance
- Compatible with Visual Studio debugging
- Uses Windows SDK and DirectX headers

**MinGW Build Benefits:**  
- More comprehensive plugin support (some plugins work better with GCC)
- Better compatibility with Unix-style dependencies
- Easier cross-compilation setup
- More extensive codec support through MSYS2 packages

**Recommendation:** Use MSVC for Windows-native applications requiring maximum performance and Windows integration. Use MinGW for broader plugin compatibility and development flexibility.

### Getting Help

- Check workflow logs in the Actions tab
- Review the [GStreamer documentation](https://gstreamer.freedesktop.org/documentation/)
- File issues in this repository for workflow-specific problems