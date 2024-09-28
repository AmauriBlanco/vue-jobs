<script setup>
import { RouterLink } from "vue-router";
import JobListing from "@/components/JobListing.vue";
import { reactive, ref, onMounted } from "vue";
import PulseLoader from "vue-spinner/src/PulseLoader.vue";
import axios from "axios";

const state = reactive({
    jobs: [],
    isLoading: true,
});

defineProps({
    limit: Number,
    showButtom: {
        type: Boolean,
        default: false,
    },
});

onMounted(async () => {
    try {
        const response = await axios.get("/api/jobs");
        state.jobs = response.data;
    } catch (error) {
        console.error("Error fetching jobs", error);
    } finally {
        state.isLoading = false;
    }
});
</script>
<template>
    <section class="bg-blue-50 px-4 py-10">
        <div class="container-xl lg:container m-auto">
            <h2 class="text-3xl font-bold text-green500 mb-6 text-center">
                Browse Jobs
            </h2>
            <!-- Show Loading spinner while loading is true -->
            <div v-if="state.isLoading" class="text-center text-gray-500">
                <PulseLoader />
            </div>

            <!-- Show jobListing when done loading -->
            <div v-else class="grid grid-cols-1 gap-6 md:grid-cols-3">
                <JobListing
                    v-for="job in state.jobs.slice(
                        0,
                        limit || state.jobs.length
                    )"
                    :key="job.id"
                    :job="job"
                />
            </div>
        </div>
    </section>

    <section v-if="showButtom" class="m-auto max-w-lg my-10 px-6">
        <RouterLink
            to="/jobs"
            class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
            >View All Jobs</RouterLink
        >
    </section>
</template>

