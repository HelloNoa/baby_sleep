# 🌙 Baby Sleep Sound Mixer

아기 잠재우기를 위한 백색소음 멀티트랙 믹서. 18개의 진짜 녹음 음원을 동시에 믹싱하고 개별 볼륨을 조절할 수 있습니다. 인터넷 없이 오프라인으로 작동합니다.

## 사용법

`baby_player.html`을 브라우저로 열기만 하면 됩니다. 같은 폴더에 있는 MP3 파일들을 자동으로 참조합니다.

```bash
# macOS
open baby_player.html

# Windows
start baby_player.html

# Linux
xdg-open baby_player.html
```

## 기능

- 18개 음원 카테고리별 정리 (백색소음, 빗소리, 자연 소리, 쉬쉬, 분위기, 자장가)
- 여러 소리 동시 재생 가능 (믹싱)
- 각 소리별 개별 볼륨 슬라이더 + 마스터 볼륨
- 자동 종료 타이머 (15분 ~ 4시간, 부드러운 페이드아웃)
- 12가지 프리셋 조합 (신생아 잠재우기, 비 오는 밤, 캠프파이어 등)
- 무한 반복

## 음원 목록

### 🔊 백색소음 (Internet Archive, Public Domain)
- `white_noise.mp3` — 화이트 노이즈, 10분, 24-bit 스튜디오 녹음
- `pink_noise.mp3` — 핑크 노이즈, 10분
- `brown_noise.mp3` — 브라운 노이즈, 10분 (⭐⭐⭐⭐⭐ 인기)

### 🌧️ 빗소리 (강도별, Internet Archive Public Domain)
- `soft_rain.mp3` — 잔잔한 보슬비, 9분
- `light_rain.mp3` — 약한 비, 15분
- `rain_window.mp3` — 창문 빗방울
- `rain.mp3` — 폭우, 15분
- `thunderstorm.mp3` — 번개 뇌우, 16분

### 🌿 자연 소리 (실제 현장 녹음)
- `ocean.mp3` — 파도 소리, La Jolla 해변, 15분
- `fireplace.mp3` — 모닥불, 진짜 벽난로, 15분
- `birds_seoul.mp3` — 한강 새소리, 반포한강공원
- `forest.mp3` — 체코 숲 소리

### 🤫 쉬쉬 (Pixabay)
- `shushing_real.mp3` — 진짜 쉬이이이~, 56초 (8회 반복 + 크로스페이드 루프)

### 🎐 분위기
- `wind_chimes.mp3` — 풍경 + 귀뚜라미 + 종, 3분

### 🎼 자장가 / 오르골 (Public Domain)
- `lullaby.mp3` — 부드러운 자장가, 3분
- `musicbox_brahms.mp3` — 브람스 왈츠 (오르골)
- `musicbox_bach.mp3` — Bach G선상의 아리아 (오르골)
- `musicbox_chopin.mp3` — 쇼팽 야상곡 (오르골)

## 라이선스 / 출처

- 백색소음, 빗소리, 자연 소리, 자장가: [Internet Archive](https://archive.org) — Public Domain (CC0 / Public Domain Mark)
- 쉬이이이 음원: [Pixabay](https://pixabay.com/sound-effects/film-special-effects-shh-sound-effect-354531/) — No attribution required

플레이어 코드는 자유롭게 사용/수정/배포 가능합니다.
