<template>
	<div class="modal" @click="uiStore.closeModal">
		<div class="modal-body" @click.stop>
			<slot />
		</div>
	</div>
</template>

<script setup lang="ts">
import { useUIStore } from "~/stores/ui";

const uiStore = useUIStore();
</script>

<style scoped lang="scss">
.modal {
	position: fixed;
	inset: 0;

	display: flex;

	text-align: center;

	background: rgb(0 0 0 / 50%);

	&-body {
		min-width: 300px;
		min-height: 50px;

		margin: auto;
		padding: 3rem 6rem;

		background-color: white;
		border: 1px solid #333;
		border-radius: 10px;
	}
}
</style>
