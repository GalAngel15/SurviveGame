# SurviveGame – Reverse Engineering APK Project

**Course:** Mobile Security
**Assignment:** Reverse Engineering & APK Analysis

---

## 🎮 Introduction

**SurviveGame** is a mobile game provided as an APK file as part of a university mobile security course. The goal of this assignment was to reverse engineer the application, recover missing components, fix hidden bugs, and restore full functionality.

The game begins by prompting the user to enter a 9-digit ID. Based on this ID, the player must follow a sequence of directional arrows to survive and reach a final city. If successful, the game displays:

> 🌆 "Survived in <City>"

---

## 🔧 Reverse Engineering Process

### APK Decompilation

To decompile the APK and inspect its contents, I used the following tools:

* [JADX](https://github.com/skylot/jadx) – For local code inspection
* [Java Decompiler Online](http://www.javadecompilers.com/apk) – For quick structure previews

These tools allowed me to extract Java source files, layout XMLs, and resources.

### Code & Structure Analysis

Key components identified:

#### Activities

* `Activity_Menu`: Main launcher screen where users input their ID.
* `Activity_Game`: Core gameplay logic and directional navigation.

#### Manifest (`AndroidManifest.xml`)

* **Package name**: `com.example.survivegame`
* **Permissions**: `android.permission.INTERNET`
* **Issues found:**

  * Missing `android:exported="true"` on main activity ✅ Fixed
  * Deprecated attributes like `platformBuildVersionCode` removed
  * Updated `targetSdkVersion` from 30 → 34 for compatibility

#### SDK

* `minSdkVersion`: 24
* `targetSdkVersion`: Updated to 34

#### Resources Reconstructed

* `res/layout`: Rebuilt `activity_menu.xml` and `activity_game.xml`
* `res/drawable`: Re-added arrow images
* `res/values`: Reconstructed `strings.xml`, styles, and restored city list URL

---

## 🤖 Game Logic Overview

* **ID requirement**: Must be exactly 9 digits.

* Each digit determines a movement direction via:

  ```java
  steps[i] = Integer.parseInt(String.valueOf(id.charAt(i))) % 4;
  ```

* Mapping:

  | Digit % 4 | Direction |
  | --------- | --------- |
  | 0         | Left (←)  |
  | 1         | Right (→) |
  | 2         | Up (↑)    |
  | 3         | Down (↓)  |

* The final city is determined using the 8th digit (index 7):

  ```java
  String state = data.split(",")[Integer.parseInt(String.valueOf(id.charAt(7)))];
  ```

* City list is fetched from:

  ```xml
  <string name="url">https://pastebin.com/raw/T67TVJG9</string>
  ```

### Updated Toast Messages

```java
Toast.makeText(this, "Survived in " + state, Toast.LENGTH_SHORT).show();
// or
Toast.makeText(this, "You Failed", Toast.LENGTH_SHORT).show();
```

---

## 🔢 Example ID and Gameplay

### Input ID: `123789456`

| Digit | Digit % 4 | Direction |
| ----- | --------- | --------- |
| 1     | 1         | Right (→) |
| 2     | 2         | Up (↑)    |
| 3     | 3         | Down (↓)  |
| 7     | 3         | Down (↓)  |
| 8     | 0         | Left (←)  |
| 9     | 1         | Right (→) |
| 4     | 0         | Left (←)  |
| 5     | 1         | Right (→) |
| 6     | 2         | Up (↑)    |

### Result:

* Correct sequence to press:
  → ↑ ↓ ↓ ← → ← → ↑
* Final City: **Pennsylvania**

---

## 🎥 Demo

Watch the demo video:


---

## ✅ Summary of Fixes

| Problem                     | Solution                                   |
| --------------------------- | ------------------------------------------ |
| `android:exported` missing  | Added to manifest                          |
| Broken city list URL        | Restored in `strings.xml`                  |
| Invalid manifest attributes | Removed outdated attributes                |
| Missing layouts & resources | Rebuilt UI & re-added drawables            |
| Toast messages unclear      | Updated to show success/failure explicitly |
| Outdated SDK                | Updated to target API level 34             |

---

Feel free to fork or reuse this project for learning purposes.

> "Reverse engineering isn't just about breaking things — it's about understanding them."
