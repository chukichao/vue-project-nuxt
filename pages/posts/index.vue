<template>
	<main>
		<h1 id="heading">Posts Page</h1>

		<UIInput
			v-model.trim="searchQuery"
			v-focus
			type="search"
			class="search"
			placeholder="search"
		/>

		<PaginationPosts v-if="searchQuery.length === 0" />

		<PostsList :posts="sortedAndSearchedPosts" />

		<div
			v-if="sortedAndSearchedPosts.length"
			v-observer="postsStore.fetchPosts"
		/>

		<div class="filter">
			<PagesLimit class="filter-limit" />
			<UISelect
				v-model="selectedSort"
				class="filter-sort"
				:options="sortOptions"
				title="sort"
			/>
		</div>

		<UIButton title="Add new post" @click="uiStore.openModal('addPost')"
			><IconsPlus class="add"
		/></UIButton>

		<UIModal v-if="uiStore.modal.type === 'addPost'">
			<FormAddPost />
		</UIModal>
	</main>
</template>

<script setup lang="ts">
import { IconsPlus } from "#components";
import { usePostsStore } from "~/stores/posts";
import { useUIStore } from "~/stores/ui";

definePageMeta({
	middleware: "auth",
});

useHead({
	title: "Crud App",
	meta: [{ name: "Posts", content: "list of posts" }],
});

const postsStore = usePostsStore();
const uiStore = useUIStore();

const route = useRoute();

const searchQuery = ref("");
const selectedSort = ref("");
const sortOptions = reactive([
	{ title: "by title", value: "title" },
	{ title: "by body", value: "body" },
]);

const scrollToUp = () => {
	const heading = document.getElementById("heading");

	if (heading) {
		heading.scrollIntoView();
	}
};

const sortedAndSearchedPosts = computed(() => {
	const sortedPosts = [...postsStore.posts];

	if (selectedSort.value) {
		sortedPosts.sort((post1, post2) =>
			post1[selectedSort.value as "title" | "body"].localeCompare(
				post2[selectedSort.value as "title" | "body"],
			),
		);
	}

	return sortedPosts.filter((post) =>
		post.title.toLowerCase().includes(searchQuery.value.toLowerCase()),
	);
});

watch(selectedSort, () => {
	scrollToUp();
});

onMounted(() => {
	postsStore.fetchPosts();

	const queryLimit = Number(route.query.limit);
	const queryPage = Number(route.query.page);

	if (queryLimit !== postsStore.limit || queryPage !== postsStore.page) {
		postsStore.setLimit(queryLimit, queryPage);
		postsStore.setPage(queryPage);
	}
});
</script>

<style scoped lang="scss">
.search {
	padding: 1rem;

	font-size: 20px;

	border-radius: 1rem;

	&::placeholder {
		font-style: italic;
	}

	&:focus {
		outline: 2px solid #333;
	}
}

.add {
	position: fixed;
	right: 3rem;
	bottom: 3rem;

	display: flex;
	align-items: center;
	justify-content: center;

	width: 50px;
	height: 50px;

	color: #555;

	background-color: white;
	border: 1px solid #333;
	border-radius: 50%;

	transition: 1s;

	&:hover {
		transform: scale(1.1);
	}

	button {
		font-size: 20px;

		border: none;
	}
}

.filter {
	position: fixed;
	right: 0;
	bottom: 0;

	select {
		padding: 0.4rem;

		color: white;

		background-color: #555;
	}
}
</style>
