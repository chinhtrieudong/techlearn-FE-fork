<template>
    <div class="card shadow mx-2 d-flex flex-column" style="width: 100%" @click="navigateToAssignment(course.id)">
        <img :src="course.thumbnailUrl" class="card-img-top" alt="Course thumbnail" @error="handleImageError" />
        <!-- <p class="trying p-1" v-if="isTrial(course.id)">
            Đang học thử
        </p> -->
        <div class="card-body d-flex flex-column flex-grow-1">
            <p class="card-name">{{ course.name }}</p>
            <p class="card-total-exercises">{{ course.totalExercises }}</p>
            <p class="card-text flex-grow-1">
                {{ truncatedDescriptions(course.description) }}
            </p>
        </div>
        <div class="c-footer pb-2" v-if="course.teacher.length > 1">
            <img class="avatar" :src="avatar" alt="Teacher avatar" />
            <p class="my-auto">{{ course?.teacher[0]?.name }}</p>
        </div>
        <div v-if="loadingState" class="d-flex justify-content-center pb-3">
            <div class="spinner"></div>
        </div>
        <div v-else class="d-flex gap-2 justify-content-center pb-3 container">
            <button v-if="isPaid(course.id)" type="button" class="btn btn-primary btn-learn-only"
                @click="navigateToAssignment(course.id)">
                Học
            </button>
            <button v-else="!isPaid(course.id)" type="button" class="btn btn-primary btn-learn-only"
                @click.stop="handleBuyCourse(course.id)">
                Mua ngay
            </button>
        </div>
    </div>
</template>

<script setup>
import { useRouter } from "vue-router";

const props = defineProps({
    course: {
        type: Object,
        required: false,
        default: () => ({}),
    },
    loadingState: {
        type: Boolean,
        required: false,
        default: false,
    },
    isTrial: {
        type: Function,
        required: false,
        default: () => () => false,
    },
    isPaid: {
        type: Function,
        required: false,
        default: () => () => false,
    },
    truncatedDescriptions: {
        type: Function,
        required: false,
        default: () => (description) => description,
    },
    handleBuyCourse: {
        type: Function,
        required: false,
        default: () => () => { },
    },
});

const router = useRouter();

const navigateToAssignment = (courseId) => {
    router.push({
        name: "courseDetail",
        params: { id: courseId },
    });
};

const handleImageError = (event) => {
    event.target.src = "https://media.istockphoto.com/id/1409329028/vector/no-picture-available-placeholder-thumbnail-icon-illustration-design.jpg?s=612x612&w=0&k=20&c=_zOuJu755g2eEUioiOUdz_mHKJQJn-tDgIAhQzyeKUQ=";
};

</script>

<style scoped>
.card-img-top {
    width: 100%;
    height: 190px;
    object-fit: cover;
    border-top-left-radius: 15px;
    border-top-right-radius: 15px;
    border-bottom-left-radius: 0px;
    border-bottom-right-radius: 0px;
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
    display: flex;
    flex-direction: column;
    height: 370px;
}

.quantity-exercise {
    font-size: 14px;
    color: rgb(175, 154, 154);
}

.card-text {
    font-size: 10px;
    font-weight: 300;
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
    border: 2px solid rgba(0, 122, 255, 0.42);
    padding: 2px;
    background-color: white;
}

.avatar {
    object-fit: cover;
}

.card-name {
    font-size: 15px;
    font-weight: 500;
}

.card-total-exercises {
    font-weight: 300;
    font-size: 12px;
    opacity: 50%;
}

.btn {
    width: 100%;
    height: 29px;
    display: flex;
    justify-content: center;
    align-items: center;
    border: none;
    text-align: center;
    font-size: 12px;
    font-weight: 400;
    transition: background-color 0.1s ease;
}

.btn-buy-only {
    background-color: rgba(212, 28, 37, 0.541);
    color: rgba(0, 0, 0, 1);
    width: 100%;
    font-size: 14px;
    color: rgba(0, 0, 0, 1);
}

.btn-buy-only:hover {
    font-size: 14px;
    background-color: rgba(190, 47, 47, 0.651);
    color: white(0, 0, 0, 1);
    width: 100%;
}

.btn-learn-only {
    width: 100%;
    background-color: #00e3cc;
    color: rgba(0, 0, 0, 1);
    font-size: 14px;
}

.btn-learn-only:hover {
    width: 100%;
    background-color: #03b3a1;
    color: white(0, 0, 0, 1);
    font-size: 14px;
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