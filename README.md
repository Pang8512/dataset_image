# How to build QT 4.8 Source on Linux

Building **Qt 4.8 on Linux** is straightforward in principle, but it’s an older codebase, so a few quirks matter (Python 2, legacy X11 setup, etc.). Here’s a clean, practical guide that works on Ubuntu/Debian and Jetson-class devices.

---

# 🧱 1. Install required dependencies

```bash
sudo apt update
sudo apt install build-essential g++ perl python2 \
  libx11-dev libxext-dev libxrender-dev \
  libxrandr-dev libxfixes-dev libxcursor-dev \
  libxinerama-dev libfreetype6-dev libfontconfig1-dev
```

👉 Notes:

* Qt 4.8 still expects **Python 2**
* These are for **GUI (X11)** builds. You can trim them if doing headless.

---

# 📦 2. Get Qt 4.8 source

Download Qt 4.8.7 (final release):

```
https://download.qt.io/archive/qt/4.8/4.8.7/qt-everywhere-opensource-src-4.8.7.tar.gz
```

Extract:

```bash
tar xf qt-everywhere-opensource-src-4.8.7.tar.gz
cd qt-everywhere-opensource-src-4.8.7
```

---

# ⚙️ 3. Configure build

Basic configuration:

```bash
./configure -opensource -confirm-license \
  -prefix /opt/qt4.8 \
  -nomake examples -nomake demos
```

---

## 🔧 Common useful options

### Faster build (recommended)

```bash
-nomake tools -nomake translations
```

### For embedded / Jetson (OpenGL ES)

```bash
-opengl es2
```

### Headless build

```bash
-no-gui
```

### Static build (optional)

```bash
-static
```

---

# 🔨 4. Build Qt

```bash
make -j$(nproc)
```

👉 On low-memory devices (Jetson Nano):

```bash
make -j2
```

---

# 📥 5. Install

```bash
sudo make install
```

---

# 🌍 6. Set environment variables

Add to `~/.bashrc`:

```bash
export QTDIR=/opt/qt4.8
export PATH=$QTDIR/bin:$PATH
export LD_LIBRARY_PATH=$QTDIR/lib:$LD_LIBRARY_PATH
```

Apply:

```bash
source ~/.bashrc
```

---

# ✅ 7. Verify

```bash
qmake -v
```

Expected:

```
QMake version 2.x
Using Qt version 4.8.x
```

---

# ⚡ Jetson Nano / ARM tips (important)

If you’re on embedded Linux:

### ✔ Use OpenGL ES

```bash
./configure -opengl es2
```

### ✔ Reduce memory usage

```bash
sudo fallocate -l 4G /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### ✔ Avoid X11 if not needed

```bash
-no-xcb
```

---

# 🚀 Faster method (recommended)

Building Qt 4.8 directly on small devices is slow.

Better approach:

* Build on x86 PC
* Use ARM cross-compiler (`aarch64-linux-gnu-gcc`)
* Copy `/opt/qt4.8` to target device

---

# ⚠️ Reality check

Qt 4.8 is:

* ❌ End-of-life (since 2015)
* ❌ Not compatible with modern toolchains easily
* ❌ Missing modern SSL/TLS

If possible, consider:

* Qt 5.15
* Qt 6

---

# 🧠 Summary

* Download Qt 4.8.7 source
* `./configure` with proper flags
* `make` + `make install`
* Set environment variables
* Use OpenGL ES for embedded systems

---
