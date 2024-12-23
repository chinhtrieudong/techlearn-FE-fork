<template>
  <div class="container">
    <div class="row">
      <div class="col-xl-3 col-lg-4 col-md-6 col-sm-6 col-12 mb-4" v-for="(course, index) in courses" :key="index">
        <CourseCard :course="course" :loadingState="loadingStates[index]" :isTrial="isTrial(course.id)" :isPaid="isPaid"
          :truncatedDescriptions="truncatedDescriptions"
          :handleBuyCourse="() => handleBuyCourse(course.id, userInfo?.id, index)" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, computed } from "vue";
import axios from "axios";
import { useStore } from "vuex";
import { toast } from "vue3-toastify";
import CourseCard from "@/components/Course/CourseCard.vue";

const store = useStore();
const loadingStates = ref([]);
const courses = ref([]);
const studentCourses = ref([]);
const rootApi = process.env.VUE_APP_ROOT_API;
const userInfo = computed(() => store.getters.user);

// Fetch all courses
const fetchCourses = async () => {
  try {
    const response = await axios.get(`${rootApi}/courses`);
    courses.value = response.data.result.data.items;
    loadingStates.value = Array(courses.value.length).fill(false);
  } catch (error) {
    console.error("Error fetching courses:", error);
  }
};

// Fetch student courses only when userInfo is available
const fetchStudentCourses = async () => {
  console.log("userInfo value", userInfo.value.id);

  // if (!userInfo.value?.id) return;
  try {
    const response = await axios.get(`${rootApi}/student-courses?id=${userInfo.value.id}`);
    studentCourses.value = response.data.result;
  } catch (error) {
    console.error("Error fetching student courses:", error);
  }
};

const isTrial = (courseId) => {
  const studentCourse = studentCourses.value?.find((sc) => sc.idCourse === courseId);
  return studentCourse && studentCourse.status === "TRIAL";
};

const isPaid = (courseId) => {
  const studentCourse = studentCourses.value?.find((sc) => sc.idCourse === courseId);
  return studentCourse && studentCourse.status === "PAID";
};

const truncatedDescriptions = (description) => {
  return description.length > 120 ? description.substring(0, 120) + "..." : description;
};

const handleBuyCourse = async (idCourse, userID, index) => {
  try {
    loadingStates.value[index] = true;
    const response = await axios.post(
      `${rootApi}/buy_course?idUser=${userID}&idCourse=${idCourse}`
    );
    toast.success("Mua khóa học thành công!!");
    await fetchStudentCourses();
  } catch (error) {
    console.error("Error buying course:", error);
    toast.error("Có lỗi xảy ra");
  } finally {
    loadingStates.value[index] = false;
  }
};

// Watch for changes in userInfo and fetch student courses
watch(userInfo, async (newValue) => {
  if (newValue?.id) {
    await fetchStudentCourses();
  }
});

// On component mount
onMounted(async () => {
  await fetchCourses();
  await fetchStudentCourses();
});
</script>
