<script setup>
import { ref, computed, onMounted, watch } from 'vue'

// Audio player reference
const audioPlayer = ref(null)
const playingId = ref(null)
const selectedCategory = ref(null)
const searchQuery = ref('')

// Currency converter
const showConverter = ref(false)
const vndAmount = ref('')
const sgdAmount = ref('')

// Emergency section
const showEmergency = ref(false)

// Fixed exchange rates (as of 9 May 2025)
const vndToSgdRate = 0.000050 // 1 VND = 0.000050 SGD
const sgdToVndRate = 20011.72 // 1 SGD = 20,011.72 VND

// Define categories
const categories = [
  { id: 'all', name: 'All Phrases' },
  { id: 'greetings', name: 'Greetings & Basics' },
  { id: 'food', name: 'Ordering Food' },
  { id: 'transport', name: 'Transportation' },
  { id: 'shopping', name: 'Shopping' },
  { id: 'emergency', name: 'Emergency' }
]

// Define emergency numbers
const emergencyNumbers = [
  { name: 'Police', number: '113', description: 'For emergencies requiring police assistance' },
  { name: 'Fire Department', number: '114', description: 'For fire emergencies' },
  { name: 'Ambulance', number: '115', description: 'For medical emergencies' },
  { name: 'Traffic Police', number: '118', description: 'For traffic accidents or violations' },
  { name: 'Tourist Police (Hanoi)', number: '(024) 3825 7047', description: 'For tourists needing police assistance in Hanoi' },
  { name: 'Tourist Police (Ho Chi Minh City)', number: '(028) 3838 7200', description: 'For tourists needing police assistance in HCMC' }
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
  }, {
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
    "id": 35,
    "category": "shopping",
    "english": "I want to buy this",
    "vietnamese": "Tôi muốn mua cái này",
    "pronunciation": "toy moon mua guy nay",
    "audio": "/audio/shopping/i_want_to_buy_this.mp3"
  },
  {
    "id": 36,
    "category": "shopping",
    "english": "I'll pay in cash",
    "vietnamese": "Tôi sẽ trả bằng tiền mặt",
    "pronunciation": "toy seh cha bang tien mat",
    "audio": "/audio/shopping/i_will_pay_in_cash.mp3"
  },
  {
    "id": 37,
    "category": "shopping",
    "english": "Do you accept cards?",
    "vietnamese": "Có chấp nhận thẻ không?",
    "pronunciation": "caw chup nyun theh khong",
    "audio": "/audio/shopping/do_you_accept_cards.mp3"
  },
  {
    "id": 38,
    "category": "shopping",
    "english": "May I have the receipt?",
    "vietnamese": "Tôi có thể xin biên lai được không?",
    "pronunciation": "toy caw thay sin been lie duke khong",
    "audio": "/audio/shopping/can_i_have_a_receipt.mp3"
  },
  {
    "id": 39,
    "category": "emergency",
    "english": "Help!",
    "vietnamese": "Cứu tôi với!",
    "pronunciation": "koo toy voy",
    "audio": "/audio/emergency/help.mp3"
  },
  {
    "id": 40,
    "category": "emergency",
    "english": "Police",
    "vietnamese": "Cảnh sát",
    "pronunciation": "kahnh saht",
    "audio": "/audio/emergency/police.mp3"
  },
  {
    "id": 41,
    "category": "emergency",
    "english": "Doctor",
    "vietnamese": "Bác sĩ",
    "pronunciation": "bahk see",
    "audio": "/audio/emergency/doctor.mp3"
  },
  {
    "id": 42,
    "category": "emergency",
    "english": "Where is the nearest pharmacy?",
    "vietnamese": "Nhà thuốc gần nhất ở đâu?",
    "pronunciation": "nya took gun nyut uh dow?",
    "audio": "/audio/emergency/where_is_nearest_pharmacy.mp3"
  },
  {
    "id": 43,
    "category": "transport",
    "english": "Please stop here.",
    "vietnamese": "Làm ơn dừng lại ở đây",
    "pronunciation": "lahm uhn zuhng lai uh day",
    "audio": "/audio/transport/please_stop_here.mp3"
  },
  {
    "id": 44,
    "category": "transport",
    "english": "How much for the trip?",
    "vietnamese": "Chuyến đi này bao nhiêu tiền?",
    "pronunciation": "chwin dee nai bao nyew tien?",
    "audio": "/audio/transport/how_much_for_the_trip.mp3"
  },
  {
    "id": 45,
    "category": "transport",
    "english": "Is there a rice bar (local eatery that serves rice dishes) nearby?",
    "vietnamese": "Gần đây có quán cơm nào không?",
    "pronunciation": "gun day kaw kwan kuhm now khong?",
    "audio": "/audio/transport/is_there_rice_bar_nearby.mp3"
  },
  {
    "id": 46,
    "category": "transport",
    "english": "Is there a coffee shop nearby?",
    "vietnamese": "Gần đây có quán cà phê nào không?",
    "pronunciation": "gun day kaw kwan gah feh now khong?",
    "audio": "/audio/transport/is_there_coffee_shop_nearby.mp3"
  },

  // add more here

]

// Scroll to top function
const scrollToTop = () => {
  window.scrollTo({
    top: 0,
    behavior: 'smooth'
  })
}


// Computed property for filtered phrases
const filteredPhrases = computed(() => {
  let result = phrases

  // First filter by category
  if (selectedCategory.value && selectedCategory.value.id !== 'all') {
    result = result.filter(phrase => phrase.category === selectedCategory.value.id)
  }

  // Then filter by search query if it exists
  if (searchQuery.value.trim()) {
    const query = searchQuery.value.trim().toLowerCase()
    result = result.filter(phrase =>
      phrase.english.toLowerCase().includes(query) ||
      phrase.vietnamese.toLowerCase().includes(query) ||
      (phrase.pronunciation && phrase.pronunciation.toLowerCase().includes(query))
    )
  }

  return result
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

const scrollToSection = (sectionId, show = true) => {
  if (sectionId === 'converter') {
    showConverter.value = show
    showEmergency.value = false
  } else if (sectionId === 'emergency') {
    showEmergency.value = show
    showConverter.value = false
  }

  setTimeout(() => {
    const element = document.getElementById(sectionId)
    if (element) {
      element.scrollIntoView({ behavior: 'smooth' })
    }
  }, 100)
}

// Call emergency number
const callNumber = (number) => {
  // In a real mobile app, this would use a native API to initiate a call
  // For web, we'll just show an alert
  alert(`Calling ${number}...`)
  // Or could use:
  // window.location.href = `tel:${number}`
}

// Clear search when changing category
watch(selectedCategory, () => {
  searchQuery.value = ''
})

onMounted(() => {
  // Set default category on load
  selectedCategory.value = categories[0]
})
</script>

<template>
  <main class="container mx-auto px-4 py-6 max-w-6xl">
    <!-- Header -->
    <header class="text-center mb-6 md:mb-8 relative">
      <h1 class="text-2xl md:text-3xl font-bold text-green-700">Common Vietnamese Phrases</h1>
      <p class="text-gray-600 mt-1 text-sm md:text-base">Learn useful Vietnamese phrases for your trip</p>

      <!-- Quick Action Buttons -->
      <div class="flex flex-wrap justify-center gap-2 mt-4">
        <button @click="scrollToSection('converter')"
          class="bg-blue-500 hover:bg-blue-600 text-white rounded-full px-3 py-1 text-xs md:text-sm flex items-center transition-colors">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3 md:h-4 md:w-4 mr-1" fill="none" viewBox="0 0 24 24"
            stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
          Currency Converter
        </button>
        <button @click="scrollToSection('emergency')"
          class="bg-red-500 hover:bg-red-600 text-white rounded-full px-3 py-1 text-xs md:text-sm flex items-center transition-colors">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3 md:h-4 md:w-4 mr-1" fill="none" viewBox="0 0 24 24"
            stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M18.364 5.636l-3.536 3.536m0 5.656l3.536 3.536M9.172 9.172L5.636 5.636m3.536 9.192l-3.536 3.536M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
          Emergency Numbers
        </button>
      </div>
    </header>

    <!-- Search Bar -->
    <div class="mb-4 md:mb-6 relative max-w-md mx-auto">
      <div class="relative">
        <input type="text" v-model="searchQuery" placeholder="Search phrases..."
          class="w-full pl-10 pr-4 py-2 rounded-full border border-gray-300 focus:border-green-500 focus:ring-1 focus:ring-green-500 focus:outline-none">
        <svg xmlns="http://www.w3.org/2000/svg"
          class="h-5 w-5 absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" fill="none"
          viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
        </svg>
        <button v-if="searchQuery" @click="searchQuery = ''"
          class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-gray-600">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Category Selection -->
    <div class="mb-6 overflow-x-auto scrollbar-hide">
      <div class="flex justify-start md:justify-center gap-2 min-w-max pb-2">
        <button v-for="category in categories" :key="category.id" @click="selectCategory(category)"
          class="px-3 py-1.5 rounded-full text-xs md:text-sm whitespace-nowrap transition-colors duration-200"
          :class="selectedCategory === category ? 'bg-green-600 text-white font-bold' : 'bg-white text-green-700 border border-green-500 hover:bg-green-50'">
          {{ category.name }}
        </button>
      </div>
    </div>

    <!-- Phrase Cards -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-3 md:gap-4 mb-8">
      <div v-for="phrase in filteredPhrases" :key="phrase.id"
        class="bg-white rounded-lg shadow-md overflow-hidden hover:shadow-lg transition-shadow duration-200">
        <div class="p-4">
          <h3 class="font-bold text-base md:text-lg text-gray-800 mb-1">{{ phrase.english }}</h3>
          <p class="text-green-600 font-medium mb-2">{{ phrase.vietnamese }}</p>
          <p class="text-gray-500 text-xs md:text-sm mb-3" v-if="phrase.pronunciation">{{ phrase.pronunciation }}</p>
          <button @click="playAudio(phrase)"
            class="w-full bg-green-100 hover:bg-green-200 text-green-800 py-1.5 px-3 rounded flex items-center justify-center text-sm transition-colors"
            :class="{ 'pulse-animation': playingId === phrase.id }">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1.5" viewBox="0 0 20 20" fill="currentColor">
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
    <div v-if="filteredPhrases.length === 0" class="text-center py-10">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 md:h-16 md:w-16 mx-auto text-gray-400" fill="none"
        viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
          d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z" />
      </svg>
      <p class="text-gray-500 mt-3 text-sm md:text-base">
        {{ searchQuery ? 'No phrases match your search.' : 'No phrases available in this category yet.' }}
      </p>
      <button v-if="searchQuery" @click="searchQuery = ''"
        class="mt-2 text-green-600 hover:text-green-700 text-sm font-medium">
        Clear search
      </button>
    </div>

    <!-- Audio Element (Hidden) -->
    <audio ref="audioPlayer" @ended="audioEnded"></audio>

    <!-- VND to SGD Converter -->
    <div id="converter" v-show="showConverter" class="mt-16 mb-8 scroll-mt-4">
      <div class="max-w-lg mx-auto bg-white rounded-lg shadow-lg overflow-hidden border border-blue-100">
        <div class="bg-blue-500 text-white p-3 md:p-4">
          <h2 class="text-lg md:text-xl font-bold flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 md:h-6 md:w-6 mr-2" fill="none" viewBox="0 0 24 24"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            Currency Converter
          </h2>
          <p class="text-xs md:text-sm mt-1">Convert between Vietnamese Dong (VND) and Singapore Dollar (SGD)</p>
        </div>

        <div class="p-4 md:p-6">
          <!-- VND to SGD -->
          <div class="mb-5">
            <label class="block text-gray-700 mb-1.5 font-medium text-sm">
              Vietnamese Dong (VND)
            </label>
            <div
              class="flex border border-gray-300 rounded overflow-hidden focus-within:border-blue-500 focus-within:ring-1 focus-within:ring-blue-500">
              <div class="bg-gray-100 px-3 py-2 text-gray-500 flex items-center text-sm">₫</div>
              <input type="text" v-model="vndAmount" @input="convertVndToSgd" placeholder="Enter amount in VND"
                class="w-full p-2 text-sm outline-none">
            </div>
          </div>

          <!-- Exchange icon -->
          <div class="flex justify-center items-center mb-5">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M7 16V4m0 0L3 8m4-4l4 4m6 0v12m0 0l4-4m-4 4l-4-4" />
            </svg>
          </div>

          <!-- SGD to VND -->
          <div>
            <label class="block text-gray-700 mb-1.5 font-medium text-sm">
              Singapore Dollar (SGD)
            </label>
            <div
              class="flex border border-gray-300 rounded overflow-hidden focus-within:border-blue-500 focus-within:ring-1 focus-within:ring-blue-500">
              <div class="bg-gray-100 px-3 py-2 text-gray-500 flex items-center text-sm">S$</div>
              <input type="text" v-model="sgdAmount" @input="convertSgdToVnd" placeholder="Enter amount in SGD"
                class="w-full p-2 text-sm outline-none">
            </div>
          </div>

          <div class="mt-5 text-xs text-gray-500">
            <p>Exchange rates as of 9 May 2025:</p>
            <p>1 SGD = {{ sgdToVndRate.toLocaleString() }} VND</p>
            <p>1 VND = {{ vndToSgdRate.toFixed(6) }} SGD</p>
          </div>
        </div>
      </div>

      <div class="text-center mt-4">
        <button @click="showConverter = false" class="text-blue-500 hover:text-blue-700 text-sm font-medium">
          Hide Currency Converter
        </button>
      </div>
    </div>

    <!-- Emergency Numbers Section -->
    <div id="emergency" v-show="showEmergency" class="mt-16 mb-8 scroll-mt-4">
      <div class="max-w-2xl mx-auto">
        <div class="bg-red-500 text-white p-3 md:p-4 rounded-t-lg">
          <h2 class="text-lg md:text-xl font-bold flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 md:h-6 md:w-6 mr-2" fill="none" viewBox="0 0 24 24"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M18.364 5.636l-3.536 3.536m0 5.656l3.536 3.536M9.172 9.172L5.636 5.636m3.536 9.192l-3.536 3.536M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            Emergency Numbers in Vietnam
          </h2>
          <p class="text-xs md:text-sm mt-1">Important contact numbers for emergencies in Vietnam</p>
        </div>

        <div class="bg-white shadow-lg rounded-b-lg divide-y">
          <div v-for="(item, index) in emergencyNumbers" :key="index"
            class="p-3 md:p-4 flex flex-col sm:flex-row sm:items-center">
            <div class="flex-grow mb-2 sm:mb-0">
              <h3 class="font-medium text-gray-900">{{ item.name }}</h3>
              <p class="text-xs text-gray-500 mt-0.5">{{ item.description }}</p>
            </div>
            <div class="flex items-center">
              <span class="font-bold text-red-600 mr-3 text-lg">{{ item.number }}</span>
              <!-- <button @click="callNumber(item.number)"
                class="bg-red-100 hover:bg-red-200 text-red-800 px-3 py-1 rounded text-sm flex items-center transition-colors">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24"
                  stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                    d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z" />
                </svg>
                Call
              </button> -->
            </div>
          </div>
        </div>

        <div class="bg-yellow-50 border-l-4 border-yellow-400 p-3 md:p-4 mt-4 rounded text-sm text-yellow-800">
          <div class="flex">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 flex-shrink-0 mr-2" fill="none" viewBox="0 0 24 24"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            <div>
              <p class="font-medium">Emergency Tip</p>
              <p class="mt-1">When calling emergency services in Vietnam, try to have a local Vietnamese speaker assist
                you if possible, as English proficiency may vary among emergency operators.</p>
            </div>
          </div>
        </div>

        <div class="text-center mt-4">
          <button @click="showEmergency = false" class="text-red-500 hover:text-red-700 text-sm font-medium">
            Hide Emergency Numbers
          </button>
        </div>
      </div>
    </div>

    <!-- Back to Top Button -->
    <div class="fixed bottom-4 right-4">
      <button @click="scrollToTop"
        class="bg-green-600 hover:bg-green-700 text-white rounded-full p-2 shadow-lg transition-colors">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 15l7-7 7 7" />
        </svg>
      </button>
    </div>

    <!-- Footer -->
    <footer class="mt-16 pb-6 pt-8 border-t border-gray-200">
      <div class="container mx-auto px-4 max-w-6xl">
        <div class="flex flex-col items-center">

          <!-- Social Links -->
          <div class="flex justify-center space-x-4 mb-4">
            <a href="https://www.linkedin.com/in/tailin-piao/" target="_blank" rel="noopener noreferrer"
              class="text-gray-500 hover:text-blue-600 transition-colors" title="Connect on LinkedIn">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24">
                <path
                  d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433c-1.144 0-2.063-.926-2.063-2.065 0-1.138.92-2.063 2.063-2.063 1.14 0 2.064.925 2.064 2.063 0 1.139-.925 2.065-2.064 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z" />
              </svg>
            </a>
            <a href="https://github.com/wolfparktaerim" target="_blank" rel="noopener noreferrer"
              class="text-gray-500 hover:text-gray-900 transition-colors" title="View GitHub Projects">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24">
                <path
                  d="M12 .297c-6.63 0-12 5.373-12 12 0 5.303 3.438 9.8 8.205 11.385.6.113.82-.258.82-.577 0-.285-.01-1.04-.015-2.04-3.338.724-4.042-1.61-4.042-1.61C4.422 18.07 3.633 17.7 3.633 17.7c-1.087-.744.084-.729.084-.729 1.205.084 1.838 1.236 1.838 1.236 1.07 1.835 2.809 1.305 3.495.998.108-.776.417-1.305.76-1.605-2.665-.3-5.466-1.332-5.466-5.93 0-1.31.465-2.38 1.235-3.22-.135-.303-.54-1.523.105-3.176 0 0 1.005-.322 3.3 1.23.96-.267 1.98-.399 3-.405 1.02.006 2.04.138 3 .405 2.28-1.552 3.285-1.23 3.285-1.23.645 1.653.24 2.873.12 3.176.765.84 1.23 1.91 1.23 3.22 0 4.61-2.805 5.625-5.475 5.92.42.36.81 1.096.81 2.22 0 1.606-.015 2.896-.015 3.286 0 .315.21.69.825.57C20.565 22.092 24 17.592 24 12.297c0-6.627-5.373-12-12-12" />
              </svg>
            </a>
            <a href="https://wolfparktaerim.vercel.app/" target="_blank" rel="noopener noreferrer"
              class="text-gray-500 hover:text-green-600 transition-colors" title="Visit Personal Website">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24"
                stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M21 12a9 9 0 01-9 9m9-9a9 9 0 00-9-9m9 9H3m9 9a9 9 0 01-9-9m9 9c1.657 0 3-4.03 3-9s-1.343-9-3-9m0 18c-1.657 0-3-4.03-3-9s1.343-9 3-9m-9 9a9 9 0 019-9" />
              </svg>
            </a>
          </div>

          <p class="text-gray-500 text-xs md:text-sm">Created with Vue.js and Tailwind CSS, by Piao TaiLin, for his
            Vietnam Trip</p>
        </div>
      </div>
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

/* Hide scrollbar for Chrome, Safari and Opera */
.scrollbar-hide::-webkit-scrollbar {
  display: none;
}

/* Hide scrollbar for IE, Edge and Firefox */
.scrollbar-hide {
  -ms-overflow-style: none;
  /* IE and Edge */
  scrollbar-width: none;
  /* Firefox */
}
</style>
