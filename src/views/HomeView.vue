<template>
  <AppLayout>
    <div class="container">
      <div v-if="loading" class="loader">Loading...</div>
      <div v-else class="movie-grid">
        <div 
          v-for="movie in state.movies" 
          :key="movie.movie_id" 
          class="card" 
          @click="goToDetail(movie.movie_id)"
        >
          <img 
            class="poster" 
            :src="movie.poster" 
            alt="Poster"
          />
          <div class="card-content">
            <p class="movie-title">{{ movie.title }}</p>
          </div>
        </div>
      </div>
    </div>
  </AppLayout>
</template>

<script setup>
import { onMounted, reactive, ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';

const router = useRouter();

const state = reactive({
  movies: [] 
});

const loading = ref(true);

const goToDetail = (movie_id) => {
  router.push({ name: 'MovieDetail', params: { index: movie_id } }); 
}

onMounted(() => {
  const cachedMovies = localStorage.getItem('movies');
  if (cachedMovies) {
    state.movies = JSON.parse(cachedMovies);
    loading.value = false; // ตั้งค่า loading เป็น false ทันทีเมื่อใช้ cache
  } else {
    const url = 'https://script.googleusercontent.com/macros/echo?user_content_key=6Zaipk-Qwe6VZ7ZqU8w2ZU90rICEwPtRs_EN3rbBjYdiOdHts-WyF0FLutmgMvKXLfk05TWB6dwWOc15I_EyID6t_lap3DAgm5_BxDlH2jW0nuo2oDemN9CCS2h10ox_1xSncGQajx_ryfhECjZEnL8u_AcB_1UHpYPCdf8vF9NY5MH5fDAIIq05qmQpVHYvIVZediGCqp9VPBpAUZwcVLrWvMx9lReKudZEoNYGr_Nl257qV3UnLQ&lib=MlS1jc-5j_78AMWoObAbgVkPwlNntp4vT';
    axios.get(url)
      .then((response) => {
        state.movies = response.data; 
        localStorage.setItem('movies', JSON.stringify(state.movies));
      })
      .finally(() => {
        loading.value = false; // ตั้งค่า loading เป็น false หลังจากดึงข้อมูล
      });
  }
});
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Anton&family=Itim&family=Kodchasan:ital,wght@1,600&family=Mali&family=Mooli&family=Nunito:wght@500&family=Roboto+Slab&family=Sriracha&display=swap');

.container {
  margin-top: 50px;
  margin-bottom: 70px;
}

.movie-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr); /* 4 คอลัมน์ */
  gap: 2rem; /* ระยะห่างระหว่างการ์ด */
}

.card {
  display: flex;
  flex-direction: column;
  border: 5px solid #000000;
  box-sizing: border-box;
  background-color: #ffffff;
  transition: transform 0.3s ease, box-shadow 0.3s ease; /* เพิ่มการเปลี่ยนแปลง */
}

.card:hover {
  transform: translateY(-10px); /* ลอยขึ้น 10px */
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2); /* เพิ่มเงาให้ดูน่าสนใจ */
}

.poster {
  width: 100%;
  height: auto; /* ปรับให้สูงตามสัดส่วน */
  object-fit: cover;
}

.card-content {
  padding: 0.5rem; /* ปรับ padding ให้น้อยลง */
  text-align: center;
}

.movie-title {
  padding-top: 15px;
  padding-bottom: 15px;
  padding-left: 5px;
  padding-right: 5px;
  font-family: "Anton", sans-serif;
  font-style: normal;
  font-size: 22px; /* ปรับขนาดฟอนต์ให้เล็กลง */
  margin: 0;
  color: rgb(0, 0, 0);
  text-align: center; /* จัดกลางข้อความ */
}

.loader {
  text-align: center;
  font-size: 24px;
  color: #000;
}

/* ปรับขนาดการแสดงผลสำหรับหน้าจอเล็ก */
@media (max-width: 768px) {
  .movie-grid {
    grid-template-columns: repeat(4, 1fr); /* ยังคงเป็น 4 คอลัมน์สำหรับหน้าจอเล็ก */
  }
}

@media (max-width: 480px) {
  .movie-grid {
    grid-template-columns: repeat(4, 1fr); /* ยังคงเป็น 4 คอลัมน์สำหรับหน้าจอที่เล็กที่สุด */
    
    .movie-title {
      font-size: 12px; /* ปรับขนาดฟอนต์ให้เล็กลงในมือถือ */
    }

    .card-content {
      padding: 0.25rem; /* ปรับ padding ให้น้อยลง */
    }
  }
}
</style>
