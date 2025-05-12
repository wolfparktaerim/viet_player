<script setup>
import { ref, computed, onMounted } from 'vue'

// Audio player reference
const audioPlayer = ref(null)
const playingId = ref(null)
const selectedCategory = ref(null)

// Currency converter
const showConverter = ref(false)
const vndAmount = ref('')
const sgdAmount = ref('')


// Fixed exchange rates (as of 9 May 2025)
const vndToSgdRate = 0.000050 // 1 VND = 0.000058 SGD
const sgdToVndRate = 20011.72     // 1 SGD = 17,241 VND

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
  // greetings
  {
    "id": 1,
    "category": "greetings",
    "english": "Hello",
    "vietnamese": "Xin chào",
    "pronunciation": "sin chow",
    "audio": "/audio/greetings/hello.mp3"
  },
  {
    "id": 2,
    "category": "greetings",
    "english": "Hi!",
    "vietnamese": "Chào!",
    "pronunciation": "chow",
    "audio": "/audio/greetings/hi.mp3"
  },
  {
    "id": 3,
    "category": "greetings",
    "english": "Thank you",
    "vietnamese": "Cảm ơn",
    "pronunciation": "gahm un",
    "audio": "/audio/greetings/thankyou.mp3"
  },
  {
    "id": 4,
    "category": "greetings",
    "english": "Sorry",
    "vietnamese": "Xin lỗi",
    "pronunciation": "sin loy",
    "audio": "/audio/greetings/sorry.mp3"
  },
  {
    "id": 5,
    "category": "greetings",
    "english": "Please",
    "vietnamese": "Làm ơn",
    "pronunciation": "lahm un",
    "audio": "/audio/greetings/please.mp3"
  },
  {
    "id": 6,
    "category": "greetings",
    "english": "Goodbye",
    "vietnamese": "Tạm biệt",
    "pronunciation": "tahm byet",
    "audio": "/audio/greetings/goodbye.mp3"
  },
  {
    "id": 7,
    "category": "greetings",
    "english": "Where is the bathroom?",
    "vietnamese": "Phòng tắm ở đâu?",
    "pronunciation": "fong tahm uh dow",
    "audio": "/audio/greetings/where_bathroom.mp3"
  },
  {
    "id": 8,
    "category": "greetings",
    "english": "Yes",
    "vietnamese": "Có",
    "pronunciation": "caw",
    "audio": "/audio/greetings/yes.mp3"
  },
  {
    "id": 9,
    "category": "greetings",
    "english": "No",
    "vietnamese": "Không",
    "pronunciation": "khum",
    "audio": "/audio/greetings/no.mp3"
  },
  {
    "id": 10,
    "category": "greetings",
    "english": "Very good",
    "vietnamese": "Rất tốt",
    "pronunciation": "zut tote",
    "audio": "/audio/greetings/very_good.mp3"
  },
  {
    "id": 11,
    "category": "greetings",
    "english": "Beautiful",
    "vietnamese": "Xinh đẹp",
    "pronunciation": "sing dep",
    "audio": "/audio/greetings/beautiful.mp3"
  },
  {
    "id": 12,
    "category": "greetings",
    "english": "I like it",
    "vietnamese": "Tôi thích nó",
    "pronunciation": "toy tick naw",
    "audio": "/audio/greetings/i_like_it.mp3"
  },
  {
    "id": 13,
    "category": "greetings",
    "english": "Ok",
    "vietnamese": "Ổn rồi",
    "pronunciation": "ohn zoy",
    "audio": "/audio/greetings/ok.mp3"
  },
  {
    "id": 14,
    "category": "greetings",
    "english": "Good morning",
    "vietnamese": "Chào buổi sáng",
    "pronunciation": "chow boo-ee sahng",
    "audio": "/audio/greetings/good_morning.mp3"
  },
  {
    "id": 15,
    "category": "greetings",
    "english": "Good night",
    "vietnamese": "Chúc ngủ ngon",
    "pronunciation": "chook ngoo ngon",
    "audio": "/audio/greetings/good_night.mp3"
  },
  {
    "id": 16,
    "category": "greetings",
    "english": "Thank you very much!",
    "vietnamese": "Cảm ơn bạn rất nhiều",
    "pronunciation": "gahm un ban zut nyew",
    "audio": "/audio/greetings/thank_you_very_much.mp3"
  },
  {
    "id": 17,
    "category": "greetings",
    "english": "Of course",
    "vietnamese": "Tất nhiên",
    "pronunciation": "tut nyen",
    "audio": "/audio/greetings/of_course.mp3"
  },
  {
    "id": 18,
    "category": "greetings",
    "english": "Correct",
    "vietnamese": "Đúng",
    "pronunciation": "doong",
    "audio": "/audio/greetings/correct.mp3"
  },
  {
    "id": 19,
    "category": "greetings",
    "english": "I agree",
    "vietnamese": "Tôi đồng ý",
    "pronunciation": "toy dome ee",
    "audio": "/audio/greetings/i_agree.mp3"
  },
  {
    "id": 20,
    "category": "greetings",
    "english": "I don't understand",
    "vietnamese": "Tôi không hiểu",
    "pronunciation": "toy khum hew",
    "audio": "/audio/greetings/i_dont_understand.mp3"
  },
  {
    "id": 21,
    "category": "greetings",
    "english": "I understand",
    "vietnamese": "Tôi hiểu",
    "pronunciation": "toy hew",
    "audio": "/audio/greetings/i_understand.mp3"
  },
  {
    "id": 22,
    "category": "greetings",
    "english": "Can you speak English?",
    "vietnamese": "Bạn có nói tiếng Anh không?",
    "pronunciation": "ban caw noy tyeng anh khum",
    "audio": "/audio/greetings/can_you_speak_english.mp3"
  },
  {
    "id": 23,
    "category": "greetings",
    "english": "Can you speak Chinese?",
    "vietnamese": "Bạn có thể nói tiếng Trung không?",
    "pronunciation": "ban caw teh noy tyeng choong khum",
    "audio": "/audio/greetings/can_you_speak_chinese.mp3"
  },
  {
    "id": 24,
    "category": "greetings",
    "english": "I am from Singapore",
    "vietnamese": "Tôi đến từ Singapore",
    "pronunciation": "toy den tuh Singapore",
    "audio": "/audio/greetings/i_am_from_singapore.mp3"
  },
  {
    "id": 25,
    "category": "greetings",
    "english": "I don't know how to speak Vietnamese",
    "vietnamese": "Tôi không biết nói tiếng Việt",
    "pronunciation": "toy khum byet noy tyeng vee-et",
    "audio": "/audio/greetings/i_dont_know_how_to_speak_vietnamese.mp3"
  },
  {
    "id": 26,
    "category": "food",
    "english": "Coffee",
    "vietnamese": "Cà phê",
    "pronunciation": "gah feh",
    "audio": "/audio/food/coffee.mp3"
  },
  {
    "id": 27,
    "category": "food",
    "english": "Black coffee",
    "vietnamese": "cà phê đen",
    "pronunciation": "gah feh den",
    "audio": "/audio/food/coffee_black.mp3"
  },
  {
    "id": 28,
    "category": "food",
    "english": "Coffee with milk",
    "vietnamese": "cà phê sữa",
    "pronunciation": "gah feh soo-ah",
    "audio": "/audio/food/coffee_with_milk.mp3"
  },
  {
    "id": 29,
    "category": "food",
    "english": "Iced Coffee",
    "vietnamese": "cà phê đá",
    "pronunciation": "gah feh dah",
    "audio": "/audio/food/coffee_iced.mp3"
  },
  {
    "id": 30,
    "category": "food",
    "english": "Is this spicy?",
    "vietnamese": "cái này có cay không?",
    "pronunciation": "guy nye caw kai khum",
    "audio": "/audio/food/is_this_spicy.mp3"
  },
  {
    "id": 31,
    "category": "food",
    "english": "I want to order this",
    "vietnamese": "tôi muốn đặt hàng này",
    "pronunciation": "toy muon dat hahng nye",
    "audio": "/audio/food/i_want_to_order_this.mp3"
  },
  {
    "id": 32,
    "category": "food",
    "english": "How much is it?",
    "vietnamese": "Cái đó giá bao nhiêu?",
    "pronunciation": "guy daw zah bao nyew",
    "audio": "/audio/food/how_much_is_it.mp3"
  },
  {
    "id": 33,
    "category": "food",
    "english": "Delicious",
    "vietnamese": "Ngon",
    "pronunciation": "ngawn",
    "audio": "/audio/food/delicious.mp3"
  },
  {
    "id": 34,
    "category": "food",
    "english": "The check, please",
    "vietnamese": "Cho hóa đơn tính tiền đi",
    "pronunciation": "chaw hwah dun ting tee-en dee",
    "audio": "/audio/food/the_check_please.mp3"
  },
  {
    id: 35,
    category: 'food',
    english: '',
    vietnamese: '',
    pronunciation: '',
    audio: '/audio/food/null.mp3'
  },
  {
    id: 36,
    category: 'food',
    english: '',
    vietnamese: '',
    pronunciation: '',
    audio: '/audio/food/null.mp3'
  },
  {
    id: 37,
    category: 'food',
    english: '',
    vietnamese: '',
    pronunciation: '',
    audio: '/audio/food/null.mp3'
  },
  {
    id: 38,
    category: 'food',
    english: '',
    vietnamese: '',
    pronunciation: '',
    audio: '/audio/food/null.mp3'
  },
  {
    id: 39,
    category: 'food',
    english: '',
    vietnamese: '',
    pronunciation: '',
    audio: '/audio/food/null.mp3'
  },
  {
    id: 40,
    category: 'food',
    english: '',
    vietnamese: '',
    pronunciation: '',
    audio: '/audio/food/null.mp3'
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

// Currency converter functions
const convertVndToSgd = () => {
  if (vndAmount.value === '') {
    sgdAmount.value = ''
    return
  }
  const vnd = parseFloat(vndAmount.value.replace(/,/g, ''))
  if (!isNaN(vnd)) {
    sgdAmount.value = (vnd * vndToSgdRate).toFixed(2)
  }
}

const convertSgdToVnd = () => {
  if (sgdAmount.value === '') {
    vndAmount.value = ''
    return
  }
  const sgd = parseFloat(sgdAmount.value.replace(/,/g, ''))
  if (!isNaN(sgd)) {
    vndAmount.value = Math.round(sgd * sgdToVndRate).toLocaleString()
  }
}

const scrollToConverter = () => {
  showConverter.value = true
  setTimeout(() => {
    document.getElementById('currency-converter').scrollIntoView({ behavior: 'smooth' })
  }, 100)
}

onMounted(() => {
  // Set default category on load
  selectedCategory.value = categories[0]
})
</script>

<template>
  <main class="container mx-auto px-4 py-8">
    <!-- Header -->
    <header class="text-center mb-8 relative">
      <h1 class="text-3xl font-bold text-green-700">Common Vietnamese Phrases</h1>
      <p class="text-gray-600 mt-2">Learn / play useful Vietnamese phrases for different situations</p>

      <!-- Currency Converter Quick Link -->
      <button @click="scrollToConverter"
        class="absolute right-0 top-0 bg-blue-500 hover:bg-blue-600 text-white rounded-full px-3 py-1 text-sm flex items-center">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24"
          stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
            d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
        </svg>
        Currency Converter
      </button>
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

    <!-- VND to SGD Converter -->
    <div id="currency-converter" v-show="showConverter" class="mt-20 mb-8">
      <div class="max-w-lg mx-auto bg-white rounded-lg shadow-lg overflow-hidden">
        <div class="bg-blue-500 text-white p-4">
          <h2 class="text-xl font-bold flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mr-2" fill="none" viewBox="0 0 24 24"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            Currency Converter
          </h2>
          <p class="text-sm mt-1">Convert between Vietnamese Dong (VND) and Singapore Dollar (SGD)</p>
        </div>

        <div class="p-6">
          <!-- VND to SGD -->
          <div class="mb-6">
            <label class="block text-gray-700 mb-2 font-medium">
              Vietnamese Dong (VND)
            </label>
            <div
              class="flex border border-gray-300 rounded overflow-hidden focus-within:border-blue-500 focus-within:ring-1 focus-within:ring-blue-500">
              <div class="bg-gray-100 px-3 py-2 text-gray-500 flex items-center">₫</div>
              <input type="text" v-model="vndAmount" @input="convertVndToSgd" placeholder="Enter amount in VND"
                class="w-full p-2 outline-none">
            </div>
          </div>

          <!-- Exchange icon -->
          <div class="flex justify-center items-center mb-6">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-400" fill="none" viewBox="0 0 24 24"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M7 16V4m0 0L3 8m4-4l4 4m6 0v12m0 0l4-4m-4 4l-4-4" />
            </svg>
          </div>

          <!-- SGD to VND -->
          <div>
            <label class="block text-gray-700 mb-2 font-medium">
              Singapore Dollar (SGD)
            </label>
            <div
              class="flex border border-gray-300 rounded overflow-hidden focus-within:border-blue-500 focus-within:ring-1 focus-within:ring-blue-500">
              <div class="bg-gray-100 px-3 py-2 text-gray-500 flex items-center">S$</div>
              <input type="text" v-model="sgdAmount" @input="convertSgdToVnd" placeholder="Enter amount in SGD"
                class="w-full p-2 outline-none">
            </div>
          </div>

          <div class="mt-6 text-xs text-gray-500">
            <p>Exchange rates as of 9 May 2025:</p>
            <p>1 SGD = {{ sgdToVndRate.toLocaleString() }} VND</p>
            <p>1 VND = {{ vndToSgdRate.toFixed(6) }} SGD</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <footer class="mt-12 text-center text-gray-500 text-sm">
      <p>&copy; 2025 Common Vietnamese Phrases</p>
      <p class="mt-1">Created with Vue.js and Tailwind CSS, by Piao TaiLin, for his Vietnam Trip</p>
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
