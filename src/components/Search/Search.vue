<template>
    <div class="dropdown-search d-flex justify-content-center m-0" ref="dropdownContainer">
        <div class="d-flex align-items-center search-wrapper" :class="{ 'focused': isFocused }">
            <FontAwesomeIcon icon="search" class="search-icon" />
            <input v-model="searchQuery" type="text" placeholder="Tìm kiếm khóa học..." @focus="onFocus" @blur="onBlur"
                @input="onSearch" class="search-input" />
        </div>
        <div v-if="showDropdown && searchQuery.trim()"
            class="dropdown d-flex align-items-center justify-content-center">
            <ul v-if="filteredItems.length > 0" class="filter-result">
                <li v-for="item in filteredItems" :key="item.id" @click="selectItem(item)"
                    class="dropdown-item d-flex align-items-center">
                    <img :src="item.thumbnailUrl" class="course-img mr-2" alt="Course thumbnail" />
                    <p class="course-name"> {{ item.name }}</p>
                </li>
            </ul>
            <div v-else class="no-results">
                Không có kết quả cho '{{ searchQuery }}'
            </div>
        </div>
    </div>
</template>


<script setup>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { ref, computed, onMounted, onBeforeUnmount } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

const router = useRouter();
const rootApi = process.env.VUE_APP_ROOT_API;
const searchQuery = ref('');
const showDropdown = ref(false);
const isFocused = ref(false);
const items = ref([]);

const filteredItems = computed(() => {
    const query = searchQuery.value.trim().toLowerCase();
    return items.value.filter((item) => item.name.toLowerCase().includes(query));
});

const fetchCourses = async (query) => {
    if (!query.trim()) {
        items.value = [];
        showDropdown.value = false;
        return;
    }

    try {
        const response = await axios.get(`${rootApi}/courses`, {
            params: {
                filter: query,
                page: 1,
                pageSize: 10,
            },
        });
        items.value = response.data.result.data.items || [];
        showDropdown.value = true;
    } catch (error) {
        console.error('Error fetching courses:', error);
        items.value = [];
    }
};

const onSearch = () => {
    if (searchQuery.value.trim()) {
        fetchCourses(searchQuery.value);
        showDropdown.value = true;
    } else {
        items.value = [];
        showDropdown.value = false;
    }
};


const selectItem = (item) => {
    searchQuery.value = item.name;
    showDropdown.value = false;
    router.push({ name: 'courseDetail', params: { id: item.id } });
};


const handleClickOutside = (event) => {
    if (
        dropdownContainer.value &&
        !dropdownContainer.value.contains(event.target) &&
        event.target !== dropdownContainer.value.querySelector('.search-input')
    ) {
        showDropdown.value = false;
    }
};

const onFocus = () => {
    isFocused.value = true;
    showDropdown.value = !!searchQuery.value.trim();
};

const onBlur = () => {
    isFocused.value = false;
};

const dropdownContainer = ref(null);

onMounted(() => {
    document.addEventListener('click', handleClickOutside);
});

onBeforeUnmount(() => {
    document.removeEventListener('click', handleClickOutside);
});
</script>


<style scoped>
.dropdown-search {
    width: 100%;
    margin: 20px auto;
    position: relative;
}

.search-wrapper {
    display: flex;
    align-items: center;
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 20px;
    transition: border 0.3s ease;
    width: 420px;
    height: 40px;
}

.search-wrapper.focused {
    border: 1px solid #101010;
}

.search-icon {
    margin-right: 8px;
    color: #aaa;
    padding-left: 5px;
    font-size: 18px;
}

.search-input {
    width: 100%;
    padding: 8px;
    border: none;
    outline: none;
    box-sizing: border-box;
    font-size: 14px;
    border-radius: 20px;
}

.dropdown {
    min-height: 50px;
    max-height: calc(90vh);
    position: absolute;
    top: 120%;
    background: #fff;
    width: 420px;
    border: 1px solid #ccc;
    border-radius: 10px;
    z-index: 1000;
    box-shadow: 0 -4px 32px #0003;
    overflow-y: auto;
}

.dropdown-item {
    padding-top: 6px;
    padding-bottom: 6px;
    cursor: pointer;
    font-size: 14px;
}

.dropdown-item:hover {
    background-color: #f5f5f5;
}

.no-results {
    padding: 8px;
    text-align: center;
    color: #888;
    font-size: 14px;
}

.filter-result {
    padding: 12px 24px;
    margin-bottom: 0;
}

.course-img {
    width: 32px;
    height: 32px;
    border-radius: 50%;
}

.course-name {
    font-size: 14px;
    color: #333;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: normal;
    display: inline-block;
    line-height: 1.4;
    max-height: calc(1.4em * 2);
    word-wrap: break-word;
}

@media screen and (min-width: 768px) {
    .search-wrapper {
        width: 100%;
    }

    .dropdown {
        width: 100%;
    }
}

@media screen and (min-width: 1024px) {
    .search-wrapper {
        width: 420px;
    }

    .dropdown {
        width: 420px;
    }
}
</style>
