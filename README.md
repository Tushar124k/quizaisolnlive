# Quiz Vision AI

> AI-powered MCQ scanner — point your camera at any multiple-choice question and get the answer in under a second.

---

## Features

| Feature | Detail |
|---|---|
| **Live Camera** | Full-screen rear camera, auto-focus, auto-exposure, flash support |
| **OCR Engine** | Google ML Kit — Latin + Devanagari (Hindi) scripts |
| **Auto-scan loop** | Polls every 300 ms, deduplicates questions automatically |
| **AI Providers** | OpenAI GPT-4o-mini · Google Gemini 2.0 Flash · Claude Haiku |
| **Offline Mode** | SQLite question bank — no internet required |
| **History** | Full question/answer log · bookmarks · search |
| **Export** | CSV and HTML (print-to-PDF) via system share sheet |
| **TTS** | Speak answer aloud with flutter_tts |
| **Dark UI** | Material 3 · glassmorphism · teal/orange palette |

---

## Architecture

```
lib/
├── main.dart                    # Entry point
├── models/
│   └── models.dart              # All data models + enums
├── core/
│   ├── theme/app_theme.dart     # Dark M3 theme + glass helpers
│   └── constants/app_constants.dart
├── ocr/
│   └── ocr_service.dart         # ML Kit wrapper + question parser
├── ai/
│   └── ai_service.dart          # OpenAI / Gemini / Claude API calls
├── services/
│   ├── offline_qbank_service.dart  # SQLite history + offline search
│   ├── settings_service.dart       # SharedPreferences persistence
│   ├── export_service.dart         # CSV + HTML export
│   └── tts_service.dart            # flutter_tts wrapper
├── providers/
│   └── providers.dart           # Riverpod notifiers + state
├── widgets/
│   ├── shared_widgets.dart      # GlassCard, OptionChip, StatusBadge, …
│   └── answer_panel.dart        # Bottom overlay panel
└── screens/
    ├── camera_screen.dart       # Main live camera screen
    ├── history_screen.dart      # History list + search + export
    └── settings_screen.dart     # API keys, provider, preferences
```

---

## Quick Start

### Prerequisites

| Tool | Version |
|---|---|
| Flutter SDK | ≥ 3.16.0 |
| Dart | ≥ 3.2.0 |
| Android SDK | API 21+ (minSdk) |
| Gradle | 8.x (bundled) |

### Install & Run

```bash
# 1. Clone / extract project
cd quiz_vision_ai

# 2. Install dependencies
flutter pub get

# 3. Connect Android device (or start emulator with camera support)
flutter devices

# 4. Run in debug mode
flutter run

# 5. Build release APK
flutter build apk --release --split-per-abi

# The APK will be at:
# build/app/outputs/flutter-apk/app-arm64-v8a-release.apk
```

### First-time setup in the app

1. Open **Settings** (gear icon, top-right)
2. Select your AI provider (Gemini recommended for free tier)
3. Paste your API key and tap **Save**
4. Return to camera — auto-scan starts immediately

---

## API Key Setup

### Google Gemini (Free tier available)
1. Go to [https://aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey)
2. Create an API key → paste into Settings → Gemini

### OpenAI
1. Go to [https://platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. Create key → paste into Settings → OpenAI

### Anthropic Claude
1. Go to [https://console.anthropic.com/account/keys](https://console.anthropic.com/account/keys)
2. Create key → paste into Settings → Claude

---

## Offline Question Bank

To use the app without internet:

1. Switch provider to **Offline Bank** in Settings
2. Import questions via SQLite (future feature: CSV import screen)
3. The app queries the local `question_bank` table for keyword matches

---

## Build Variants

```bash
# Debug APK (larger, with debugging symbols)
flutter build apk --debug

# Release APK (optimized, smaller)
flutter build apk --release --split-per-abi

# Single universal APK
flutter build apk --release

# App Bundle for Play Store
flutter build appbundle --release
```

---

## Permissions Required

| Permission | Reason |
|---|---|
| `CAMERA` | Live preview and frame capture |
| `INTERNET` | AI API calls |
| `READ_EXTERNAL_STORAGE` | Export file sharing (Android ≤12) |
| `READ_MEDIA_IMAGES` | Export file sharing (Android 13+) |

---

## Performance Notes

- OCR runs on each camera frame (≈300 ms interval) on the main thread via ML Kit's optimized native pipeline
- AI calls are async and non-blocking — UI stays responsive
- Duplicate detection prevents re-processing the same question
- Target latency: **< 500 ms OCR + < 1 s AI** (network dependent)

---

## Extending the Offline Bank

Insert rows directly into the `question_bank` SQLite table:

```sql
INSERT INTO question_bank (question, option_a, option_b, option_c, option_d, answer, subject)
VALUES (
  'What is the SI unit of electric current?',
  'Volt', 'Ampere', 'Ohm', 'Watt',
  'B',
  'Physics'
);
```

Or pre-populate via a CSV file — a CSV import screen can be added as a future feature.

---

## License

MIT — free to use, modify, and distribute.
