<script setup lang="ts">
import { RouterView } from 'vue-router';
</script>

<template>
	<VApp>
		<VAppBar title="Media Logger"></VAppBar>
		<VMain id="content-area">
			<RouterView></RouterView>
		</VMain>

		<VBottomNavigation grow>
			<VBtn :to="{ name: 'Home' }">Home</VBtn>
			<VBtn :to="{ name: 'Entry' }">Entry</VBtn>
			<VBtn :to="{ name: 'Database' }">Log</VBtn>
			<VBtn :to="{ name: 'Settings' }">Settings</VBtn>
		</VBottomNavigation>
	</VApp>
</template>

<style scoped>
#content-area {
	display: flex;
	flex-direction: column;
	align-items: center;
}
</style>
