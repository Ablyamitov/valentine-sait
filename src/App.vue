<template>
  <div class="valentine-app">
    <!-- Floating Hearts Background -->
    <div class="hearts-container">
      <div v-for="n in 20" :key="n" class="heart-float" :style="getRandomHeartStyle()">❤️</div>
    </div>

    <!-- Hero Section -->
    <section class="hero section">
      <h1 class="title glow">С Днем Святого Валентина, Любимая! ❤️</h1>
      <div class="phrase-carousel">
        <transition name="fade" mode="out-in">
          <p :key="currentPhraseIndex" class="phrase">{{ currentPhrase }}</p>
        </transition>
      </div>
    </section>

    <!-- Photo Gallery -->
    <section class="gallery section">
      <h2 class="section-title">Наши моменты ✨</h2>
      <div class="photo-grid">
        <div v-for="(photo, index) in photos" :key="index" class="photo-card" @click="selectedPhoto = photo">
          <img :src="photo" alt="Valentine image" />
          <div class="photo-overlay">❤️</div>
        </div>
      </div>
    </section>

    <!-- Love Multiverse Section -->
    <section class="multiverse section">
      <h2 class="section-title glow">Мультивселенная нашей любви 🌌</h2>
      <p class="section-subtitle">Как бы мы выглядели в других мирах...</p>
      
      <div class="reality-display">
        <transition name="reality-fade" mode="out-in">
          <div :key="currentReality.id" class="reality-card" :class="currentReality.id">
            <img :src="currentReality.img" :alt="currentReality.name" />
            <div class="reality-label">{{ currentReality.name }}</div>
          </div>
        </transition>
      </div>

      <div class="reality-controls">
        <button 
          v-for="reality in realities" 
          :key="reality.id" 
          @click="currentRealityIndex = realities.indexOf(reality)"
          class="reality-btn"
          :class="{ 'active': currentRealityIndex === realities.indexOf(reality) }"
        >
          {{ reality.btnLabel }}
        </button>
      </div>
    </section>

    <!-- Video Section -->
    <section class="video-section section">
      <h2 class="section-title">Маленький сюрприз 🎥</h2>
      <div class="video-container">
        <!-- Placeholder for AI Video -->
        <video controls width="100%" class="ai-video">
          <source src="/assets/video.mp4" type="video/mp4">
        </video>
      </div>
    </section>

    <!-- Final Message -->
    <section class="final-message section">
      <blockquote class="special-phrase">
        "Я бы тебя мочканул... ❤️"
      </blockquote>
      <p class="sub-text">Но только в объятиях!</p>
      
      <button @click="triggerSurprise" class="surprise-btn" :class="{ 'pulse': !surpriseTriggered }">
        {{ surpriseTriggered ? 'Я тебя очень люблю! 💕' : 'Нажми для сюрприза' }}
      </button>

      <div v-if="surpriseTriggered" class="heart-burst">
        <div v-for="n in 50" :key="n" class="burst-heart" :style="getBurstStyle()">💖</div>
      </div>
    </section>

    <!-- Photo Modal -->
    <div v-if="selectedPhoto" class="modal" @click="selectedPhoto = null">
      <img :src="selectedPhoto" class="modal-content" />
    </div>

    <!-- Music Player -->
    <div class="music-player" :class="{ 'playing': isPlaying }">
      <div class="music-waves" v-if="isPlaying">
        <div v-for="n in 4" :key="n" class="music-wave"></div>
      </div>
      <button @click="toggleMusic" class="music-toggle" :title="isPlaying ? 'Выключить музыку' : 'Включить музыку'">
        <span v-if="isPlaying" class="icon">⏸️</span>
        <span v-else class="icon">🎵</span>
      </button>
      <audio ref="audioRef" loop>
        <source src="/music/Egor_Krid_-_Potrachu_47828609.mp3" type="audio/mp3">
      </audio>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, useTemplateRef } from 'vue'

const currentPhraseIndex = ref(0)
const surpriseTriggered = ref(false)
const selectedPhoto = ref(null)
const isPlaying = ref(false)
const audioRef = useTemplateRef('audioRef')
const currentRealityIndex = ref(0)

const realities = [
  { id: 'original', name: 'Наша реальность', btnLabel: 'Оригинал', img: '/assets/photo4.jpg' },
  { id: 'anime', name: 'Мир Аниме', btnLabel: 'Аниме', img: '/assets/photo4_anime.png' },
  { id: 'medieval', name: 'Средневековая сага', btnLabel: 'Средневековье', img: '/assets/photo4_medieval.png' },
  { id: 'cyberpunk', name: 'Неоновое будущее', btnLabel: 'Киберпанк', img: '/assets/photo4_cyberpunk.png' }
]

const currentReality = computed(() => realities[currentRealityIndex.value])

// ... existing constants ...
const phrases = [
  "Моя лисица 🦊",
  "Моя тигрица 🐯",
  "Моя пантера 🐆",
  "Самая лучшая в мире 🎀"
]

const photos = [
  '/assets/photo11.jpg',
  '/assets/photo22.jpg',
  '/assets/photo33.jpg'
]

const currentPhrase = computed(() => phrases[currentPhraseIndex.value])

onMounted(() => {
  setInterval(() => {
    currentPhraseIndex.value = (currentPhraseIndex.value + 1) % phrases.length
  }, 3000)
})

const toggleMusic = () => {
  if (isPlaying.value) {
    audioRef.value.pause()
  } else {
    audioRef.value.play().catch(err => {
      console.log("Autoplay prevented, click again or higher volume.", err)
    })
  }
  isPlaying.value = !isPlaying.value
}

const triggerSurprise = () => {
  surpriseTriggered.value = true
}

const getRandomHeartStyle = () => {
  const left = Math.random() * 100
  const duration = 5 + Math.random() * 10
  const delay = Math.random() * 5
  return {
    left: `${left}%`,
    animationDuration: `${duration}s`,
    animationDelay: `${delay}s`,
    fontSize: `${10 + Math.random() * 20}px`
  }
}

const getBurstStyle = () => {
  const angle = Math.random() * Math.PI * 2
  const distance = 100 + Math.random() * 300
  const tx = Math.cos(angle) * distance
  const ty = Math.sin(angle) * distance
  return {
    '--tx': `${tx}px`,
    '--ty': `${ty}px`,
    animationDuration: `${1 + Math.random()}s`
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Dancing+Script:wght@700&family=Playfair+Display:ital,wght@0,400;0,700;1,400&display=swap');

:root {
  --primary-pink: #ff4d6d;
  --secondary-pink: #ff758f;
  --deep-red: #800f2f;
  --bg-gradient: linear-gradient(135deg, #1a0a0d 0%, #4a0e1c 100%);
}

body {
  margin: 0;
  padding: 0;
  font-family: 'Playfair Display', serif;
  background: #1a0a0d;
  color: #fff;
  overflow-x: hidden;
}

.valentine-app {
  min-height: 100vh;
  position: relative;
  z-index: 1;
}

/* Floating Hearts */
.hearts-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
}

.heart-float {
  position: absolute;
  bottom: -50px;
  animation: floatUp linear infinite;
  opacity: 0.6;
}

@keyframes floatUp {
  0% { transform: translateY(0) rotate(0deg); opacity: 0.6; }
  100% { transform: translateY(-110vh) rotate(360deg); opacity: 0; }
}

/* Sections */
.section {
  padding: 80px 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Dancing Script', cursive;
  font-size: 4rem;
  margin-bottom: 30px;
  color: var(--primary-pink);
}

.glow {
  text-shadow: 0 0 15px rgba(255, 77, 109, 0.7);
}

.phrase-carousel {
  height: 50px;
  font-size: 2rem;
  color: #fad0c4;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

/* Gallery */
.photo-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 20px;
  max-width: 1000px;
  width: 100%;
}

.photo-card {
  position: relative;
  border-radius: 15px;
  overflow: hidden;
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  aspect-ratio: 1;
}

.photo-card:hover {
  transform: scale(1.03);
  box-shadow: 0 10px 30px rgba(255, 77, 109, 0.4);
}

.photo-card img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.photo-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(128, 15, 47, 0.4);
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 3rem;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.photo-card:hover .photo-overlay {
  opacity: 1;
}

/* Multiverse Section */
.multiverse {
  background: rgba(255, 255, 255, 0.02);
  border-radius: 30px;
  margin: 40px 20px;
  padding: 60px 40px;
}

.section-subtitle {
  font-size: 1.2rem;
  opacity: 0.8;
  margin-bottom: 40px;
  font-style: italic;
}

.reality-display {
  width: 100%;
  max-width: 600px;
  margin-bottom: 40px;
}

.reality-card {
  position: relative;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.5);
  background: #000;
  transition: all 0.5s ease;
  aspect-ratio: 1;
}

.reality-card img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.reality-label {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 20px;
  background: linear-gradient(transparent, rgba(0,0,0,0.8));
  font-size: 1.8rem;
  font-weight: bold;
}

/* Reality Specific Themes */
.reality-card.anime { border: 4px solid #ff758d; box-shadow: 0 0 30px rgba(255,117,141,0.4); }
.reality-card.medieval { border: 4px solid #d4af37; box-shadow: 0 0 30px rgba(212,175,55,0.4); }
.reality-card.cyberpunk { border: 4px solid #00f2ff; box-shadow: 0 0 30px rgba(0,242,255,0.4); }

.reality-controls {
  display: flex;
  gap: 15px;
  flex-wrap: wrap;
  justify-content: center;
}

.reality-btn {
  background: rgba(255, 255, 255, 0.1);
  color: white;
  border: 1px solid rgba(255, 255, 255, 0.2);
  padding: 10px 25px;
  border-radius: 20px;
  cursor: pointer;
  font-family: 'Playfair Display', serif;
  transition: all 0.3s ease;
}

.reality-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: translateY(-2px);
}

.reality-btn.active {
  background: var(--primary-pink);
  border-color: var(--primary-pink);
  box-shadow: 0 5px 15px rgba(255, 77, 109, 0.4);
}

.reality-fade-enter-active, .reality-fade-leave-active {
  transition: all 0.5s ease;
}
.reality-fade-enter-from { opacity: 0; transform: scale(0.9) rotate(-2deg); }
.reality-fade-leave-to { opacity: 0; transform: scale(1.1) rotate(2deg); }

/* Video Section */
.video-container {
  max-width: 800px;
  width: 100%;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 20px;
  padding: 20px;
  border: 1px dashed var(--secondary-pink);
}

.video-placeholder {
  aspect-ratio: 16/9;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: #ccc;
}

.play-icon {
  font-size: 4rem;
  margin-bottom: 10px;
  color: var(--primary-pink);
}

.hint {
  font-size: 0.8rem;
  opacity: 0.6;
}

/* Final Message */
.special-phrase {
  font-family: 'Dancing Script', cursive;
  font-size: 3rem;
  margin: 40px 0 10px;
  color: var(--primary-pink);
}

.sub-text {
  font-style: italic;
  font-size: 1.2rem;
  margin-bottom: 40px;
}

.surprise-btn {
  background: var(--primary-pink);
  color: white;
  border: none;
  padding: 15px 40px;
  font-size: 1.5rem;
  border-radius: 50px;
  cursor: pointer;
  font-family: 'Playfair Display', serif;
  transition: all 0.3s ease;
}

.surprise-btn:hover {
  background: var(--deep-red);
  transform: translateY(-5px);
  box-shadow: 0 5px 20px rgba(255, 77, 109, 0.5);
}

.pulse {
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); }
  100% { transform: scale(1); }
}

/* Heart Burst */
.heart-burst {
  position: relative;
  width: 10px;
  height: 10px;
  margin-top: 50px;
}

.burst-heart {
  position: absolute;
  top: 0;
  left: 0;
  font-size: 1.5rem;
  animation: burstOut 1.5s forwards ease-out;
}

@keyframes burstOut {
  0% { transform: translate(0, 0) scale(1); opacity: 1; }
  100% { transform: translate(var(--tx), var(--ty)) scale(0); opacity: 0; }
}

/* Modal */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.9);
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  z-index: 100;
}

.modal-content {
  max-width: 90%;
  max-height: 90vh;
  border-radius: 10px;
}

/* Music Player Styles */
.music-player {
  position: fixed;
  bottom: 30px;
  right: 30px;
  display: flex;
  align-items: center;
  gap: 15px;
  z-index: 1000;
}

.music-toggle {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background: var(--primary-pink);
  border: none;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 5px 15px rgba(255, 77, 109, 0.4);
  transition: all 0.3s ease;
}

.music-toggle:hover {
  transform: scale(1.1);
  background: var(--deep-red);
}

.music-toggle .icon {
  font-size: 24px;
}

.music-player.playing .music-toggle {
  animation: spin 5s linear infinite;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

.music-waves {
  display: flex;
  align-items: flex-end;
  gap: 3px;
  height: 30px;
}

.music-wave {
  width: 4px;
  background: var(--secondary-pink);
  border-radius: 2px;
  animation: wave 1s ease-in-out infinite;
}

.music-wave:nth-child(1) { height: 10px; animation-delay: 0s; }
.music-wave:nth-child(2) { height: 20px; animation-delay: 0.1s; }
.music-wave:nth-child(3) { height: 15px; animation-delay: 0.2s; }
.music-wave:nth-child(4) { height: 25px; animation-delay: 0.3s; }

@keyframes wave {
  0%, 100% { transform: scaleY(1); }
  50% { transform: scaleY(1.5); }
}

@media (max-width: 600px) {
  .title { font-size: 2.5rem; }
  .phrase-carousel { font-size: 1.5rem; }
  .special-phrase { font-size: 2rem; }
  .music-player { bottom: 20px; right: 20px; }
  .music-toggle { width: 50px; height: 50px; }
}
</style>
