<template>
  <div class="container">
    <p class="title">Khóa học của tôi</p>
    <div class="row">
      <div class="col-xl-3 col-lg-4 col-md-6 col-sm-6 col-12 mb-4" v-for="(item, index) in myCourses" :key="index">
        <CourseCard :course="item" :loadingState="loadingStates[index]"
          :truncatedDescriptions="truncatedDescriptions" />
      </div>
    </div>
  </div>
</template>

<script setup>
import CourseCard from "@/components/Course/CourseCard.vue";
import axios from "axios";
import { ref, onMounted, computed } from "vue";
import { useRouter } from "vue-router";
import { useStore } from "vuex";

const router = useRouter();
const store = useStore();

const rootApi = process.env.VUE_APP_ROOT_API;
const myCourses = ref([]);
const loadingStates = ref([]);
const userID = computed(() => store.getters.user);

const fetchMyCourses = async (page = 1, pageSize = 30) => {
  try {
    const response = await axios.get(`${rootApi}/courses/user`, {
      params: {
        page: page,
        pageSize: pageSize,
        id: userID.value.id,
      },
    });

    myCourses.value = response.data.result.items.data || [];
    console.log("myCourses -->", myCourses.value);
  } catch (error) {
    console.error("Failed to fetch courses:", error);
  }
};

const truncatedDescriptions = (description) => {
  return description.length > 120 ? description.substring(0, 120) + "..." : description;
};

onMounted(async () => {
  await fetchMyCourses();
});
</script>

<style scoped>
.card-container {
  margin-top: 20px;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 15px;
}

.card-container img {
  width: 100%;
  height: 150px;
  border-top-left-radius: 15px;
  border-top-right-radius: 15px;
}

.card-hover {
  transition: 0.5s;
}

.card-hover:hover {
  transform: translate(10px, -10px);
}

.title {
  margin-top: 8px;
  font-weight: 650;
  font-size: 24px;
}

.card-title {
  font-weight: 580;
}

.card {
  cursor: pointer;
  border-radius: 15px;
}

.quantity-exercise {
  font-size: 14px;
  color: rgb(175, 154, 154);
}

.card-text {
  font-weight: 400;
  font-size: 14px;
  color: rgb(120, 88, 88);
}

.c-footer {
  display: flex;
  gap: 10px;
  justify-content: flex-start;
  margin-left: 15px;
  align-items: center;
}

.c-footer img {
  width: 30px;
  height: 30px;
  border-radius: 50%;
}

.avatar {
  object-fit: cover;
}

.relative {
  position: relative;
}

.trying {
  position: absolute;
  display: inline-block;
  border-top-left-radius: 4px;
  border-bottom-left-radius: 4px;
  clip-path: polygon(20px 0px, 100% 0px, 100% 100%, 0% 100%, 0% 20px);
  background: yellow;
  padding: 16px 40px;
  margin: 0 8px;
  font-weight: 600;
  font-size: 13px;
  color: red;
  top: 10px;
  right: 0;
  width: 105px;
  text-align: right;
}

.trying:after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 20px;
  height: 20px;
  background: rgb(211, 211, 0);
  box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.1);
  border-radius: 0 0 6px 0;
  transition: transform 500ms;
}

.lesson-number {
  color: #00000080;
  font-size: 15px;
}

.course-des {
  font-size: 14px;
  color: #333;
  line-height: 1.2rem;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
}

.btn-buy {
  background-color: #007aff6b;
  border: none;
  color: #333;
  font-weight: 500;
}

.spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left: 4px solid white;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  animation: spin 1s linear infinite;
  display: inline-block;
  vertical-align: middle;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
</style>
