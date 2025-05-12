<script setup>
import { ref, computed, onMounted } from 'vue'

// Audio player reference
const audioPlayer = ref(null)
const playingId = ref(null)
const selectedCategory = ref(null)

// Define categories
const categories = [
  { id: 'all', name: 'All Phrases' },
  { id: 'greetings', name: 'Greetings & Basics' },
  { id: 'food', name: 'Ordering Food' },
  { id: 'transport', name: 'Transportation' },
  { id: 'shopping', name: 'Shopping' },
  { id: 'emergency', name: 'Emergency' }
]

// Define phrases
const phrases = [
  // Greetings & Basics
  {
    id: 1,
    category: 'greetings',
    english: 'Hello',
    vietnamese: 'Xin chào',
    pronunciation: 'sin chow',
    audio: '/audio/xin-chao.mp3'
  },
  {
    id: 2,
    category: 'greetings',
    english: 'Thank you',
    vietnamese: 'Cảm ơn',
    pronunciation: 'gahm un',
    audio: '/audio/cam-on.mp3'
  },
  {
    id: 3,
    category: 'greetings',
    english: 'Goodbye',
    vietnamese: 'Tạm biệt',
    pronunciation: 'tam bee-et',
    audio: '/audio/tam-biet.mp3'
  },
  {
    id: 4,
    category: 'greetings',
    english: 'How are you?',
    vietnamese: 'Bạn khỏe không?',
    pronunciation: 'ban kwe kohng',
    audio: '/audio/ban-khoe-khong.mp3'
  },
  {
    id: 5,
    category: 'greetings',
    english: 'My name is...',
    vietnamese: 'Tôi tên là...',
    pronunciation: 'toy ten la',
    audio: '/audio/toi-ten-la.mp3'
  },

  // Ordering Food
  {
    id: 6,
    category: 'food',
    english: 'Can I see the menu?',
    vietnamese: 'Cho tôi xem thực đơn?',
    pronunciation: 'chaw toy sem tuk dun',
    audio: '/audio/cho-toi-xem-thuc-don.mp3'
  },
  {
    id: 7,
    category: 'food',
    english: 'One bowl of phở, please',
    vietnamese: 'Cho một tô phở',
    pronunciation: 'chaw mote taw fuh',
    audio: '/audio/cho-mot-to-pho.mp3'
  },
  {
    id: 8,
    category: 'food',
    english: 'Bill, please',
    vietnamese: 'Tính tiền',
    pronunciation: 'tin tee-en',
    audio: '/audio/tinh-tien.mp3'
  },
  {
    id: 9,
    category: 'food',
    english: 'This is delicious',
    vietnamese: 'Món này rất ngon',
    pronunciation: 'moan nay zut ngon',
    audio: '/audio/mon-nay-rat-ngon.mp3'
  },

  // Transportation
  {
    id: 10,
    category: 'transport',
    english: 'How much is a taxi to...?',
    vietnamese: 'Đi taxi đến... bao nhiêu tiền?',
    pronunciation: 'dee tak-see den... bow nyew tee-en',
    audio: '/audio/di-taxi-den-bao-nhieu-tien.mp3'
  },
  {
    id: 11,
    category: 'transport',
    english: 'Where is the bus station?',
    vietnamese: 'Bến xe buýt ở đâu?',
    pronunciation: 'ben say bwit uh dow',
    audio: '/audio/ben-xe-buyt-o-dau.mp3'
  },
  {
    id: 12,
    category: 'transport',
    english: 'Stop here, please',
    vietnamese: 'Xin dừng lại đây',
    pronunciation: 'sin zoong lie day',
    audio: '/audio/xin-dung-lai-day.mp3'
  },

  // Shopping
  {
    id: 13,
    category: 'shopping',
    english: 'How much is this?',
    vietnamese: 'Cái này giá bao nhiêu?',
    pronunciation: 'guy nay ya bow nyew',
    audio: '/audio/cai-nay-gia-bao-nhieu.mp3'
  },
  {
    id: 14,
    category: 'shopping',
    english: 'That\'s too expensive',
    vietnamese: 'Đắt quá',
    pronunciation: 'dat wa',
    audio: '/audio/dat-qua.mp3'
  },
  {
    id: 15,
    category: 'shopping',
    english: 'Can you lower the price?',
    vietnamese: 'Bớt giá được không?',
    pronunciation: 'bot ya duoc kohng',
    audio: '/audio/bot-gia-duoc-khong.mp3'
  },

  // Emergency
  {
    id: 16,
    category: 'emergency',
    english: 'Help!',
    vietnamese: 'Cứu với!',
    pronunciation: 'kew voy',
    audio: '/audio/cuu-voi.mp3'
  },
  {
    id: 17,
    category: 'emergency',
    english: 'I need a doctor',
    vietnamese: 'Tôi cần bác sĩ',
    pronunciation: 'toy can bac see',
    audio: '/audio/toi-can-bac-si.mp3'
  },
  {
    id: 18,
    category: 'emergency',
    english: 'Call the police',
    vietnamese: 'Gọi cảnh sát',
    pronunciation: 'goy kanh sat',
    audio: '/audio/goi-canh-sat.mp3'
  }
]

// Computed property for filtered phrases
const filteredPhrases = computed(() => {
  if (!selectedCategory.value || selectedCategory.value.id === 'all') {
    return phrases
  }
  return phrases.filter(phrase => phrase.category === selectedCategory.value.id)
})

// Function to play audio
const playAudio = (phrase) => {
  if (audioPlayer.value) {
    // In a real app with actual audio files:
    audioPlayer.value.src = phrase.audio
    audioPlayer.value.play()

    playingId.value = phrase.id
  }
}

const audioEnded = () => {
  playingId.value = null
}

const selectCategory = (category) => {
  selectedCategory.value = category
}

onMounted(() => {
  // Set default category on load
  selectedCategory.value = categories[0]
})
</script>

<template>
  <main class="container mx-auto px-4 py-8">
    <!-- Header -->
    <header class="text-center mb-8">
      <h1 class="text-3xl font-bold text-green-700">Common Vietnamese Phrases</h1>
      <p class="text-gray-600 mt-2">Learn useful Vietnamese phrases for different situations</p>
    </header>

    <!-- Category Selection -->
    <div class="mb-8">
      <div class="flex flex-wrap justify-center gap-2 md:gap-4">
        <button v-for="category in categories" :key="category.id" @click="selectCategory(category)"
          class="px-4 py-2 rounded-full text-sm md:text-base transition-colors duration-200"
          :class="selectedCategory === category ? 'bg-green-600 text-white font-bold' : 'bg-white text-green-700 border border-green-500 hover:bg-green-50'">
          {{ category.name }}
        </button>
      </div>
    </div>

    <!-- Phrase Cards -->
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 mb-8">
      <div v-for="phrase in filteredPhrases" :key="phrase.id"
        class="bg-white rounded-lg shadow-md overflow-hidden hover:shadow-lg transition-shadow duration-200">
        <div class="p-5">
          <h3 class="font-bold text-lg text-gray-800 mb-1">{{ phrase.english }}</h3>
          <p class="text-green-600 font-medium mb-3">{{ phrase.vietnamese }}</p>
          <p class="text-gray-500 text-sm mb-4" v-if="phrase.pronunciation">{{ phrase.pronunciation }}</p>
          <button @click="playAudio(phrase)"
            class="w-full bg-green-100 hover:bg-green-200 text-green-800 py-2 px-4 rounded flex items-center justify-center"
            :class="{ 'pulse-animation': playingId === phrase.id }">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd"
                d="M10 18a8 8 0 100-16 8 8 0 000 16zM9.555 7.168A1 1 0 008 8v4a1 1 0 001.555.832l3-2a1 1 0 000-1.664l-3-2z"
                clip-rule="evenodd" />
            </svg>
            Play
          </button>
        </div>
      </div>
    </div>

    <!-- Empty State -->
    <div v-if="filteredPhrases.length === 0" class="text-center py-12">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 mx-auto text-gray-400" fill="none" viewBox="0 0 24 24"
        stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
          d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z" />
      </svg>
      <p class="text-gray-500 mt-4">No phrases available in this category yet.</p>
    </div>

    <!-- Audio Element (Hidden) -->
    <audio ref="audioPlayer" @ended="audioEnded"></audio>

    <!-- Footer -->
    <footer class="mt-12 text-center text-gray-500 text-sm">
      <p>&copy; 2025 Common Vietnamese Phrases</p>
      <p class="mt-1">Created with Vue.js and Tailwind CSS</p>
    </footer>
  </main>
</template>

<style scoped>
@keyframes pulse-once {

  0%,
  100% {
    transform: scale(1);
  }

  50% {
    transform: scale(1.05);
  }
}

.pulse-animation {
  animation: pulse-once 0.3s ease-in-out;
}
</style>
